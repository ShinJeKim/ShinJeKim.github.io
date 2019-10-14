---
layout: post
title: "[Android] Service란?"
date: 2019-10-11
categories: Android
tags: Android
comments: true
---

*이 글은 [안드로이드 공식문서](#참고-문헌)를 공부하며 번역 및 요약한 글입니다.*

# Service
```kotlin
abstract class Service : ContextWrapper, ComponentCallbacks2
```

안드로이드에서의 서비스(Service)는 유저와 상호작용하지 않으면서 시간이 상대적으로 오래 걸리는 작업을 수행하거나, 다른 애플리케이션에서 사용할 기능을 제공하는 역할을 수행하는 애플리케이션 컴포넌트입니다. 각 서비스 클래스는 패키지의 `AndroidManifest.xml` 파일에 적절한 [\<service\>](https://shinjekim.github.io/android/2019/10/10/Android-AndrodManifest%EC%97%90%EC%84%9C-service-%EC%86%8D%EC%84%B1%EC%9D%98-%EC%A2%85%EB%A5%98/) 선언을 가지고 있어야합니다. 서비스는 [`Contenxt.startService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#startService(android.content.Intent))와 [`Context.bindService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#bindService(android.content.Intent,%20android.content.ServiceConnection,%20kotlin.Int))로 시작할 수 있습니다.

서비스는 다른 애플리케이션 객체처럼 호스팅 프로세스의 메인 스레드에서 실행됩니다. 즉, 서비스가 CPU 집약적(MP3 재생 등)이거나 차단(네트워킹 등)을 하는 작업을 수행하려는 경우에는 해당 작업을 수행할 자체 스레드를 생성해야합니다. 이것과 관련된 더 많은 정보는 [Processes and Threads](https://developer.android.com/guide/topics/fundamentals/processes-and-threads.html)에서 확인할 수 있습니다. [`IntentService`](https://developer.android.com/reference/kotlin/android/app/IntentService.html) 클래스는 서비스의 표준 구현으로 사용될 수 있으며, 작업이 완료되는 순간 자체적인 스레드를 가집니다.

## 서비스란 무엇인가?
서비스가 무엇인지에 대한 혼란스러움은 대부분 *무엇이 서비스가 아닌지*에 대한 것입니다.
- 서비스는 별개의 프로세스가 **아닙니다**. 서비스 객체는 그 자체로 실행을 암시하거나 자체적인 프로세스를 가지지 않습니다; 특별히 지정된것이 아니라면, 애플리케이션이 실행되고 있는것과 동일한 스레드에서 실행됩니다.
- 서비스는 스레드가 **아닙니다**. ANR(Application Not Responding) 에러를 피하기 위해 메인 스레드에서 작업을 수행하는 수단 자체는 아닙니다.

서비스 그 자체는 매우 간단하며, 아래의 두 가지 주요 기능을 가지고 있습니다.
- 애플리케이션이 백그라운드에서 수행하려는 작업에 *대해* 시스템에 알려주는 기능(사용자가 애플리케이션가 직접적으로 상호작용하지 않더라도):
  
  이는 서비스 혹은 다른 사람이 명시적으로 중단(stop)할 때까지 시스템에서 서비스 작업을 예약하도록 요청하는 [`Context.startService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#startService(android.content.Intent)) 호출에 해당합니다.
- 애플리케이션의 일부 기능을 다른 애플리케이션에 노출시키는 기능:
  
  이는 
[`Context.bindService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#bindService(android.content.Intent,%20android.content.ServiceConnection,%20kotlin.Int)) 호출에 해당하며, 이로 인해 서비스와 상호작용하기 위한 long-standing connection을 할 수 있습니다.

이러한 이유로 인해 서비스 컴포넌트가 생성될 때 시스템이 실제로 하는 것은 컴포넌트를 인스턴스화하고 메인스레드에서 [`onCreate`](https://developer.android.com/reference/kotlin/android/app/Service.html#onCreate())와 다른 적절한 콜백을 호출하는 것입니다. 보조 스레드(secondary thread)를 생성하는 것과 같은 적절한 동작을 구현하는 것은 서비스에 달려있습니다.

Service 자체는 아주 단순하기 때문에 만들고 싶은 상호작용을 원하는 만큼 간단하게 혹은 복잡하게 만들 수 있습니다: from treating it as a local Java object that you make direct method calls on (as illustrated by Local Service Sample), to providing a full remoteable interface using AIDL.

## 서비스의 유형
서비스에는 세 가지 유형이 있습니다

**포그라운드(Foreground)**

포그라운드 서비스는 사용자에게 보여지는 여러 작업을 수행합니다. 예를 들어 오디오 앱이라면 오디오 트랙을 재생할 때 포그라운드 서비스를 사용합니다. 포그라운드 서비스는 [알림(Notificaiton)](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)을 표시해야 합니다. 포그라운드 서비스는 사용자가 앱과 상호작용하지 않을 때에도 계속 실행됩니다.

**백그라운드(Background)**

백그라운드 서비스는 사용자에게 직접 보이지 않는 작업을 수행합니다. 예를 들어 어떠한 앱이 저장소를 압축하는 데 서비스를 사용했다면 이것은 대부분 백그라운드 서비스입니다. 
>참고: 앱이 API 레벨 26 이상을 대상으로 한다면 앱이 포그라운드에 있지 않을 때 시스템에서 [백그라운드 서비스 실행에 대한 제한](https://developer.android.com/about/versions/oreo/background.html)을 적용합니다. 이와 같은 경우에서는 대부분 앱이 [예약된 작업](https://developer.android.com/topic/performance/scheduling.html)을 사용해야 합니다.

**바인드(Bound)**

애플리케이션 구성 요소가 [`bindService()`](https://developer.android.com/reference/android/content/Context.html#bindService(android.content.Intent,%20android.content.ServiceConnection,%20int))를 호출하여 해당 서비스에 바인딩되면 서비스가 *바인딩*됩니다. 바인딩된 서비스는 클라이언트-서버 인터페이스를 제공하여 구성 요소가 서비스와 상호작용하게 하며, 결과를 받을 수도 있고 심지어 이와 같은 작업을 여러 프로세스에 걸쳐 프로세스 간 통신(IPC)으로 수행할 수도 있습니다. 바인딩된 서비스는 또 다른 애플리케이션 구성 요소가 이에 바인딩되어 있는 경우에만 실행됩니다. 여러 개의 구성 요소가 서비스에 한꺼번에 바인딩될 수 있지만, 이 모든 것에서 바인딩이 해제되면 해당 서비스는 소멸됩니다.

## 기본 사항
서비스를 생성하기 위해서는 [`Service`](https://developer.android.com/reference/android/app/Service.html)의 하위 클래스(혹은 이것의 기존 하위 클래스 중 하나)를 생성해야 합니다. 구현에서는 서비스 수명 주기의 주요 측면을 처리하는 콜백 메서드를 몇 가지 재정의해야 하며 서비스에 바인딩할 구성 요소에 대한 메커니즘을 제공해야 합니다(해당되는 경우). 서비스를 구현할 때 오버라이딩해야하는 가장 중요한 콜백 메서드는 아래와 같습니다.

[`onStartCommand()`](https://developer.android.com/reference/android/app/Service.html#onStartCommand(android.content.Intent,%20int,%20int))

시스템이 이 메서드를 호출하는 것은 또 다른 구성 요소(예: 액티비티)가 서비스를 시작하도록 요청하는 경우입니다. 이때 `startService()`를 호출하는 방법을 씁니다. 이 메서드가 실행되면 서비스가 시작되고 백그라운드에서 무한히 실행될 수 있습니다. 이것을 구현하면 서비스의 작업이 완료되었을 때 해당 서비스를 중단하는 것은 개발자 본인의 책임이며, 이때 `stopSelf()` 또는 `stopService()`를 호출하면 됩니다. 바인딩만 제공하고자 하는 경우, 이 메서드를 구현하지 않아도 됩니다.

[`onBind()`](https://developer.android.com/reference/android/app/Service.html#onBind(android.content.Intent))

시스템은 다른 구성 요소가 해당 서비스에 바인딩되고자 하는 경우(예를 들어 RPC를 수행하기 위해)에도 이 메서드를 호출합니다. 이때 `bindService()`를 호출하는 방법을 사용합니다. 이 메서드를 구현할 때에는 클라이언트가 서비스와 통신을 주고받기 위해 사용할 인터페이스를 제공해야 합니다. 이때 `IBinder`를 반환하면 됩니다. 이 메서드는 항상 구현해야 하지만, 바인딩을 허용하지 않으려면 null을 반환해야 합니다.

[`onCreate()`](https://developer.android.com/reference/android/app/Service.html#onCreate())

시스템은 서비스가 처음 생성되었을 때(즉 서비스가 `onStartCommand()` 또는 `onBind()`를 호출하기 전에) 이 메서드를 호출하여 일회성 설정 절차를 수행합니다. 서비스가 이미 실행 중인 경우, 이 메서드는 호출되지 않습니다.

[`onDestroy()`](https://developer.android.com/reference/android/app/Service.html#onDestroy())

시스템이 이 메서드를 호출하는 것은 서비스를 더 이상 사용하지 않고 소멸시킬 때입니다. 서비스는 스레드, 등록된 리스너 또는 수신기 등의 각종 리소스를 정리하기 위해 이것을 구현해야 합니다. 이는 서비스가 수신하는 마지막 호출입니다.

## 서비스 라이프사이클(Service Lifecycle)
서비스가 시스템에 의해 실행될 수 있는 두 가지 이유가 있습니다. 만약 [`Context.startService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#startService(android.content.Intent))를 호출한다면 시스템은 서비스를 가져와(필요하다면 서비스를 생성하여 `onCreate` 메소드를 호출할 것입니다) 클라이언트가 제공한 인자로 [`onStartCommand`](https://developer.android.com/reference/kotlin/android/app/Service.html#onStartCommand(android.content.Intent,%20kotlin.Int,%20kotlin.Int)) 메소드를 호출할 것입니다. 이 시점에서 서비스는 [`Context.stopService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#stopService(android.content.Intent)) 혹은 [`stopSelf()`](https://developer.android.com/reference/kotlin/android/app/Service.html#stopSelf())가 호출되기 전까지 계속 실행될 것입니다. `Context.startService()`에 대한 여러 호출은 중첩되지 않으므로 서비스가 시작된 횟수와는 관계 없이 `Context.stopService()` 혹은 `stopSelf()`가 호출되면 서비스는 중단(stop)될 것입니다; 그러나 서비스는 `stopSelf(int)` 메소드를 사용하여 start intent가 실행되기 전까지 서비스가 중단되지 않도록 할 수 있습니다. 

시작된 서비스의 경우, `onStartCommand()`에서 리턴하는 값에 따라 실행할 수 있는 두 가지 주요 operation mode가 있습니다: [`START_STICKY`](https://developer.android.com/reference/kotlin/android/app/Service.html#START_STICKY:kotlin.Int)는 필요에 의해 명시적으로 시작되고 중단되는 서비스에 사용됩니다. 반면에 [`START_NOT_STICKY`](https://developer.android.com/reference/kotlin/android/app/Service.html#START_NOT_STICKY:kotlin.Int) 혹은 [`START_REDELIVER_INTENT`](https://developer.android.com/reference/kotlin/android/app/Service.html#START_REDELIVER_INTENT:kotlin.Int)는 전송된 명령을 처리하는 동안에만 서비스가 실행되어야 하는 경우에 사용됩니다. 

클라이언트는 서비스에 지속적으로 연결하기 위해 [`Context.bindService()`](https://developer.android.com/reference/kotlin/android/content/Context.html#bindService(android.content.Intent,%20android.content.ServiceConnection,%20kotlin.Int))를 사용할 수 있습니다. 이는 마찬가지로 서비스가 아직 실행중이 아니지만(`onCreate`를 호출하는 동안) `onStartCommand()`를 호출하지 않은 경우에 서비스를 생성(create)합니다. 클라이언트는 서비스가 `onBind` 메소드에서 리턴하는 [`android.os.IBinder`](https://developer.android.com/reference/kotlin/android/os/IBinder.html) 객체를 수신하여 클라이언트가 다시 서비스를 호출할 수 있도록 해줍니다. 서비스는 연결(connection)이 설정되는 한 계속 실행될것입니다(서비스의 IBinder에 대한 참조를 클라이언트가 가지고 있는지의 여부와는 상관없습니다). 리턴된 IBinder는 일반적으로 [`aidl로 작성된(written in aidl)`](https://developer.android.com/guide/components/aidl.html) 복합 인터페이스(complex interface)로 리턴됩니다.

서비스는 시작될 수도 있고 서비스에 연결이 바인딩 될 수도 있습니다. 이 경우에 시스템은 서비스가 시작되거나, 혹은 [`Context.BIND_AUTO_CREATE`](https://developer.android.com/reference/kotlin/android/content/Context.html#BIND_AUTO_CREATE:kotlin.Int)와 하나 이상의 연결이 있는 한 서비스를 계속 실행하도록 합니다. 두 가지 상황 모두 발생하지 않으면 서비스의 [`onDestroy`](https://developer.android.com/reference/kotlin/android/app/Service.html#onDestroy()) 메소드가 호출되고, 서비스가 효과적으로 종료(terminate)됩니다. `onDestroy()` 반환이 완료되면 모든 정리(스레드 중지, unregistering receivers)가 완료되어야만 합니다.


### 참고 문헌
- [안드로이드 공식문서 - Service](https://developer.android.com/reference/kotlin/android/app/Service.html#WhatIsAService)
- [안드로이드 공식문서 - Service Overview](https://developer.android.com/guide/components/services#Choosing-service-thread)