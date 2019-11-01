---
layout: post
title: "[Android] 안드로이드 Context란?"
date: 2019-11-01
categories: Android
tags: Android
comments: true
---

*이 글은 MindOrks의 [Understanding Context In Android Application](https://blog.mindorks.com/understanding-context-in-android-application-330913e32514)을 (나름대로) 번역한 글입니다. 잘못된 부분이 있으면 언제든지 알려주세요.*

## 안드로이드에서 컨텍스트(Context) 무엇인가?
이름 그대로 해석한다면 애플리케이션(객체)의 현재 상태의 맥락(context)를 의미합니다. 컨텍스트는 새로 생성된 객체가 지금 어떤 일이 일어나고 있는지 알 수 있도록 합니다. 따라서 액티비티와 애플리케이션에 대한 정보를 얻기 위해서는 컨텍스트를 사용하면 됩니다. 

또한, `컨텍스트(Context)`는 시스템의 핸들과도 같습니다. 리소스. 데이터베이스, preferences 등에 대한 접근을 제공합니다. 안드로이드 앱에는 '액티비티'라는 것이 있습니다. 액티비티는 애플리케이션이 현재 실행중인 환경에 대한 핸들과도 같습니다. 액티비티 객체는 컨텍스트 객체를 상속받습니다. 액티비티는 애플리케이션의 특정 리소스와 클래스, 그리고 애플리케이션 환경에 대한 정보에 대해 접근할 수 있게 해줍니다. 

안드로이드 개발에서 `컨텍스트(Context)`는 어디에나 있고, 가장 중요한 것입니다. 따라서 컨텍스트를 이해하고 올바르게 써야만합니다.

`컨텍스트(Context)`를 잘못 사용하는 것은 애플리케이션의 메모리 누수를 일으킬 수 있습니다. 

안드로이드에는 다양한 유형의 컨텍스트가 있습니다. 아래에서 어떤 컨텍스트가 있는지, 그리고 언제 어떻게 사용해야 하는지 알아보겠습니다.

## 애플리케이션 컨텍스트(Application Context)
애플리케이션 컨텍스트는 싱글턴 인스턴스이며 액티비티에서 `getApplicationContext()`를 통해 접근할 수 있습니다. 이 컨텍스트는 애플리케이션의 라이프사이클과 연결되어 있습니다. 애플리케이션 컨텍스트는 현재의 컨텍스트와 분리된 라이프사이클을 가진 컨텍스트가 필요할 때나 액티비티의 범위를 넘어서 컨텍스트를 전달할 떄에 사용합니다. 

예시: 만약 당신의 애플리케이션에서 싱글턴 객체를 생성하였는데 그 객체에 컨텍스트가 필요하다면, 애플리케이션 컨텍스트를 사용하면 됩니다.

만약 이러한 상황에서 액티비티 컨텍스트를 전달한다면 메모리 누수가 발생할 것입니다. 액티비티는 가비지 콜렉터에 의해 수집되지 않는데 액티비티 컨텍스트는 액티비티에 대한 참조를 계속 유지하기 때문입니다.

액티비티에서 라이브러리를 초기화해야 하는 경우, **액티비티 컨텍스트가 아닌** 애플리케이션 컨텍스트를 전달해야합니다.

그 어떤 `컨텍스트(Context)`보다 오래 유지되는 `컨텍스트(Context)`가 필요할때에만 `getApplicationContext()`를 사용하십시오.

(이후 내용 추후 추가 예정)

### 참고 자료
- [MindOrks - Understanding Context In Android Application](https://blog.mindorks.com/understanding-context-in-android-application-330913e32514)
- [찰스의 안드로이드 - 안드로이드의 Context](https://stackoverflow.com/questions/3572463/what-is-context-on-android)