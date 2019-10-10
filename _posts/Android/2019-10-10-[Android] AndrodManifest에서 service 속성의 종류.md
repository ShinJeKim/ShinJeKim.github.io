---
layout: post
title: "[Android] AndrodManifest에서 \<service> 속성의 종류"
date: 2019-10-10
categories: Android
tags: Android
comments: true
---

## \<service\>

### **syntax**

```xml
<service android:description="string resource"
         android:directBootAware=["true" | "false"]
         android:enabled=["true" | "false"]
         android:exported=["true" | "false"]
         android:foregroundServiceType=["connectedDevice" | "dataSync" |
                                        "location" | "mediaPlayback" | "mediaProjection" |
                                        "phoneCall"]
         android:icon="drawable resource"
         android:isolatedProcess=["true" | "false"]
         android:label="string resource"
         android:name="string"
         android:permission="string"
         android:process="string" >
    . . .
</service>
```

### **contained in:**
`<application>`

### **can contain:**
- `<intent-filter>`
- `<meta-data>`

### **description:**
서비스([`Service`](https://developer.android.com/reference/android/app/Service.html))를 애플리케이션의 컴포넌트로 선언하는 부분입니다. 액티비티와는 달리 서비스는 보여지는 유저 인터페이스가 없습니다. 주로 오래 지속되는 백그라운드 오퍼레이션이나 다른 애플리케이션으로부터 호출되는 많은 통신 API(rich communications API)를 구현하는데에 사용됩니다. 

모든 서비스는 매너페스트 파일의 `<service>`에서 선언되어야만 합니다. 이 곳에 선언되지 않은 서비스는 시스템에서 보여지지 않으며 절대 실행되지 않습니다.

### **attributes:**
- `android:description`
  
  사용자에게 서비스를 설명하는 문자열입니다. 사용자 인터페이스의 다른 문자열처럼 현지화될 수 있도록 이 레이블 또한 스트링 리소스를 참조하도록 설정해야합니다.
- `android:directBootAware`
  
  서비스가 *direct-boot aware* 한지를 설정합니다. *direct-boot aware*란, 사용자가 기기의 잠금을 해제하지 않고도 실행할 수 있는지의 여부를 의미합니다. 디폴트값은 `"false"`입니다.
    > **주의**: [Direct Boot](https://developer.android.com/training/articles/direct-boot.html)를 하는 동안 당신의 애플리케이션의 서비스는 *device protected* 저장소에 저장된 데이터에만 접근할 수 있습니다. 
- `android:enabled`
  
  시스템에 의해 서비스가 인스턴스화 될 수 있는지를 의미합니다. 디폴트값은 `"true"`입니다. [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html)의 엘리먼트에도 [`<enabled>`](https://developer.android.com/guide/topics/manifest/application-element.html#enabled) 속성이 있습니다. 따라서 [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html)과 `<service>`속성 모두 `"true"`로 되어 있어야(두 속성 모두 디폴트값은 true입니다) 서비스가 활성화됩니다. 둘 중 하나라도 "`false`"로 설정되어 있으면 서비스가 비활성화되고 인스턴스화 될 수 없습니다.
- `andorid:exported`
  
  다른 애플리케이션의 컴포넌트가 서비스를 호출하거나 서비스와 상호작용 할 수 있는지를 결정합니다. 값이 `"false"`이면 같은 user ID를 가진 동일한 애플리케이션 내에서만 서비스를 시작하거나 bind할 수 있습니다. 디폴트값은 해당 서비스가 intent filter를 포함하고 있는지의 여부에 따라 다릅니다. intent filter가 없다는 것은 서비스가 해당 애플리케이션 내에서만 사용하도록 만들어졌다는 의미입니다. 따라서 이 때의 디폴트값은 `"false"`입니다. 반면 intent filter가 하나라도 있다면 이는 external use를 염두에 두고 만들었다는 뜻이므로 `"true"`가 디폴트값으로 설정됩니다. 
- `android:foregroundServiceType`
  
  서비스가 특정 사용 사례를 만족시키는 [`foregound service`](https://developer.android.com/guide/components/services)인지를 지정합니다. 예를 들어 `"location"`의 foreground 서비스 타입은 일반적으로 사용자 시작 작업을 계속(continue a user-initiated action)하기 위해 앱이 디바이스의 현재 위치를 가져온다는 것을 의미합니다.

  특정 서비스에 여러개의 foregound 서비스 타입을 지정할 수 있습니다.

- `android:icon`
  
  서비스를 나타내는 아이콘입니다. 이 속성은 이미지 정의를 포함하는 drawable 리소스에 대한 찹조로 설정해야만 합니다. 만약 설정하지 않는다면, 애플리케이션 전체에 지정된 아이콘이 사용됩니다([`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html)요소의 [`icon`](https://developer.android.com/guide/topics/manifest/application-element.html#icon)속성 참조)

  매너페스트 파일에서 설정했든, [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html)요소에 의해 설정되었든 서비스 아이콘은 모든 서비스의 intent filter의 디폴트 아이콘이기도 합니다([`<intent-filter>`]() 요소의 `icon` 속성 참조).
- `android:isolatedProcess`
  
  true로 설정하면 이 서비스는 다른 시스템과 분리되어 자체 권한이 없는 특수한 프로세스에서 실행됩니다. 이 서비스와의 유일한 통신 방법은 Service API(binding과 starting)를 통한 것입니다.

- `android:label`
  
  사용자에게 보여지는 서비스의 이름입니다. 만약 이 속성을 설정하지 않으면 전체 애플리케이션에 설정된 레이블이 대신 사용됩니다([`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html)요소의 [`label`](https://developer.android.com/guide/topics/manifest/application-element.html#label)속성을 참고하십시오).

  이곳에서 설정되었든 [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html) 요소에서 설정되었든, 서비스의 레이블은 모든 서비스 intent filter의 디폴트 레이블입니다([`<intent-filter>`](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 요소의 [`label`](https://developer.android.com/guide/topics/manifest/intent-filter-element.html#label) 속성 참조).

  레이블은 유저 인터페이스의 다른 문자열처럼 현지화될 수 있도록 스트링 리소스를 참조하도록 설정해야합니다. 하지만 개발상의 편의를 위해 raw 스트링으로도 설정할 수는 있습니다.

- `android:name`
  
  서비스를 구현하는 [`Service`](https://developer.android.com/reference/android/app/Service.html) 서브클래스의 이름을 의미하며, 이는 fully qualified 클래스 이름이어야 합니다(예: `com.example.project.RoomService`). 하지만 간단히 표현하여 이름의 첫 문자가 마침표이면(예: `.RoomService`) [`<manifest>`](https://developer.android.com/guide/topics/manifest/manifest-element.html) 요소에 지정된 패키지 이름에 추가됩니다. 

  애플리케이션을 발행한 이후에는, [이 이름을 변경하지 않도록 해야합니다](http://android-developers.blogspot.com/2011/06/things-that-cannot-change.html)(`android:exported="false"`로 설정하지 않은 경우에).

  디폴트값은 없으며, 이름을 꼭 설정해야합니다.
- `android:permission`
  
  서비스를 시작하거나 바인딩하기 위해 엔티티가 가져야 하는 권한의 이름을 의미합니다. 만약 [`startService()`](https://developer.android.com/reference/android/content/Context.html#startService(android.content.Intent)), [`bindService()`](https://developer.android.com/reference/android/content/Context.html#bindService(android.content.Intent,%20android.content.ServiceConnection,%20int)), 혹은 [`stopService()`](https://developer.android.com/reference/android/content/Context.html#stopService(android.content.Intent))의 호출자에게 이 권한이 부여되지 않으면 메서드가 작동하지 않으며 Intent object가 서비스에 전달되지 않습니다.

  이 속성을 설정하지 않으면 [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html) 요소의 [`permission`](https://developer.android.com/guide/topics/manifest/application-element.html#prmsn) 속성으로 설정된 권한이 해당 서비스에 적용됩니다. 만약 두 가지 속성 모두 설정되지 않으면, 서비스는 권한으로 보호되지 않습니다.

  권한에 대한 자세한 내용은 소개의 [Permissions](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) 섹션과 별도의 문서인 [Security and Permissions](https://developer.android.com/guide/topics/security/security.html)를 참조하십시오.

- `android:process`
  
  서비스가 실행될 프로세스의 이름을 의미합니다. 일반적으로 애플리케이션의 모든 구성요소는 애플리케이션에 대해 생성된 기본 프로세스에서 실행됩니다. 이는 애플리케이션 패키지의 이름과 동일한 이름을 가집니다. [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html) 요소의 [`process`](https://developer.android.com/guide/topics/manifest/application-element.html#proc) 속성은 모든 컴포넌트에 대해 다른 기본값을 설정할 수 있습니다. 하지만 컴포넌트는 자체적인 `process` 속성을 재정의하여 당신의 애플리케이션을 여러 개의 프로세스로 분산시킬 수 있습니다.

  만약 이 속성에 지정된 이름이 콜론(':')으로 시작한다면, 필요에 의해 애플리케이션 전용의 새 프로세스가 생성되고, 서비스가 해당 프로세스에서 실행될 수 있습니다. 만약 프로세스 이름이 소문자로 시작한다면, 서비스는 *권한이 있는 경우* 해당 이름의 글로벌(global) 프로세스에서 실행됩니다. 이를 통해 서로 다른 애플리케이션에 속한 구성요소가 프로세스를 공유하여 리소스 사용량을 줄일 수 있습니다.


### 참고문헌
[구글 공식문서 - \<service>](https://developer.android.com/guide/topics/manifest/service-element)