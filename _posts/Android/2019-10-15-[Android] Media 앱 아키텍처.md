---
layout: post
title: "[Android] Media 앱 아키텍처"
date: 2019-10-15
categories: Android
tags: Android
comments: true
---

*이 글은 [안드로이드 공식문서](https://developer.android.com/guide/topics/media-apps/media-apps-overview)를 공부하며 정리한 글입니다.*

# Media 앱 아키텍처 개요(Media app architecture overview)
이 섹션에서는 UI를 위한 미디어 컨트롤러와, 실제 플레이어를 위한 미디어 세션을 어떻게 구분하는지에 대해 설명합니다. 

여기에서는 두 가지의 미디어 애플리케이션 아키텍쳐를 다룹니다.
- 클라이언트/서버 구조: 오디오 앱에 적합합니다.
- 단일 액티비티 구조: 비디오 플레이어에 적합합니다.
  
또한, 어떻게 미디어 애플리케이션이 하드웨어에 응답하고, 오디오 출력 스트림을 사용하여 다른 애플리케이션에 협력하는지에 대해 설명합니다.  

## 플레이어와 UI(Player and UI)
오디오 혹은 비디오를 재생하는 멀티미디어 애플리케이션에는 두 가지 부분이 있습니다.
- 디지털 미디어를 받아 비디오/오디오로 렌더링하는 플레이어
- 플레이어를 실행하고 선택적으로 플레이어의 상태를 표시하는 transport control이 있는 UI

![ui-and-player.png](/resources/images/ui-and-player.png)

안드로이드에서는 아예 처음부터 자신만의 플레이어를 만들 수도 있고, 아래의 옵션 중 하나를 선택할 수도 있습니다.

- [MediaPlayer](https://developer.android.com/guide/topics/media/mediaplayer.html)클래스가 일반적으로 가장 많이 쓰이는 오디오/비디오 형식과 데이터 소스를 지원하는 bare-bones 플레이어에 대한 기본 기능을 제공해줍니다.
- [ExoPlayer](https://developer.android.com/guide/topics/media/exoplayer.html)는 하위 레벨의 안드로이드 오디오 API를 공개하는 오픈 소스 라이브러리입니다. 엑소플레이어는 `MediaPlayer`에서 지원하지 않는 DASH나 HLS 스트리밍과 같은 높은 수준의 퍼포먼스를 지원합니다. 쉽게 새로운 컴포넌트를 추가할 수 있도록 엑소플레이어의 코드를 커스터마이즈할 수도 있습니다. 엑소플레이어는 안드로이드 버전 4.1(API 레벨 16) 이상부터 사용 가능합니다. 

## 미디어 세션과 미디어 컨트롤러(Media Session and media controller)


## 비디오 앱 vs 오디오 앱(Video apps versus audio apps)
비디오를 재생할때에는 우리의 눈과 귀 모두 관여합니다. 하지만 오디오를 재생할때에는 귀로는 소리를 들으며 다른 앱을 동시에 사용할 수 있습니다. 각 사용법에 따라 다른 설계를 사용합니다.

### 비디오 앱(Video app)
비디오 앱은 컨텐츠를 보여줄 창(window)이 필요합니다. 이로 인해 비디오앱은 대부분 단일 안드로이드 액티비티(single Android activity)로 구현됩니다. 
이 때 비디오가 보여지는 스크린은 액티비티의 일부입니다.

![video-player-activity.png](/resources/images/video-player-activity.png)

### 오디오 앱(Audio app)

오디오 플레이어는 UI가 보여질 필요가 없습니다. 한 번 재생을 시작하면 플레이어는 백그라운드 작업으로 계속 재생을 실행합니다. 따라서 오디오를 계속 들으며 다른 앱을 사용할 수 있습니다. 

안드로이드에서 이러한 설게를 구현하기 위해서는 두 가지 컴포넌트를 사용하여 오디오 앱을 만들 수 있습니다: 하나는 **UI를 위한 액티비티**이고, 다른 하나는 **플레이어를 위한 서비스**입니다. 만약 유저가 다른 앱을 사용하게된다면, 플레이어 서비스는 백그라운드에서 실행됩니다. 오디오 앱의 두 부분을 별도의 구성 요소로 나눈다면, 각각의 구성 요소가 더욱 효율적으로 동작할 수 있습니다. UI 없이도 꽤 오랜 시간 실행되는 플레이어에 비해 일반적으로 UI는 짧은 수명을 가집니다.

![audio-activity-and-service.png](/resources/images/audio-activity-and-service.png)

오디오 앱에 선호되는 아키텍처는 클라이언트/서버 구조입니다. 플레이어와 플레이어의 미디어는 `MediaBrowserService` 내부에 구현되고, UI와 미디어 컨트롤러는 `MediaBrowser`와 함께 안드로이드 액티비티 내부에서 작동합니다. 

클라이언트/서버 접근 방법을 구현하기 위해 지원되는 라이브러리는 두 가지 클래스를 제공합니다: 하나는 `MediaBrowserService`이고, 다른 하나는 `MediaBrowser`입니다. 서비스 컴포넌트는 미디어 세션과 플레이어를 포함한 `MediaBrowserService`의 서브클래스로 구현되어 있습니다. UI와 미디어 컨트롤러가 있는 액티비티는 `MediaBrowserService`와 통신하는 `MediaBrowser`를 포함해야만 합니다. 

`MediaBrowserService`를 사용하면 직접적으로 앱의 UI 액티비티에 접근하지 않고도, Android Auto나 Android Wear와 같은 주변 기기가 앱을 쉽게 찾고, 컨텐츠를 열고, 재생을 제어할 수 있습니다. 각각의 앱은 자신만의 `MediaController`를 가지고 있기 때문에 하나의 동일한 `MediaBrowserService`에 여러 개의 앱을 연결할 수 있습니다. 따라서 `MediaBrowserService`를 제공하는 앱은 다중 동시 연결(multiple simultaneous connections)을 제어할 수 있어야만합니다.

`MediaBrowserService`는 두 가지 주요 기능을 제공합니다:
- `MediaBrowserService`를 사용하면, `MediaBrowserService`가 있는 다른 컴포넌트와 애플리케이션에서 서비스를 검색하고, 자체 미디어 컨트롤러를 생성하고, 미디어 세션에 연결하고 플레이어를 제어할 수 있습니다. 이것이 바로 Wear OS와 Android Auto Application이 당신의 미디어 애플리케이션에 접근하는 방법입니다.
- 선택적 *브라우징 API(browsing API)*를 제공합니다. 애플리케이션은 이 기능을 사용할 필요가 없습니다. 브라우징 API를 사용하면 클라이언트가 서비스를 조회하고 해당 컨텐츠 계층의 representation을 build out할 수 있습니다. 이 컨텐츠 계층은 재생 목록, 미디어 라이브러리 혹은 다른 종류의 컬렉션을 나타낼 수 있습니다.

> **★Note** 
> 
> 미디어 세션과 미디어 컨트롤러의 경우와 마찬가지로, 권장되는 미디어 브라우저 서비스 및 미디어 브라우저 구현 방법은 [media-compat support library](https://developer.android.com/topic/libraries/support-library/features.html#v4-media-compat)에 정의된 **MediaBrowserService**와 **MediaBrowserCompat** 클래스를 이용하는 것입니다. 두 클래스는 API 21부터 도입된 이전 버전의 **MediaBrowserService**와 **MediaBrowser** 클래스를 대체합니다. "MediaBrowserService"와 "MediaBrowser"라는 용어는 **MediaBrowserServiceCompat** 및 **MediaBrowserCompat**의 인스턴스를 간결하게 나타냅니다.

## 미디어 앱과 안드로이드 오디오 인프라(Media apps and the Android audio infrastructure)
잘 설계된 미디어 앱은 다른 오디오 재생 앱과 함께 잘 재생되어야(play well together)합니다. 휴대전화 기기를 공유하고 오디오를 사용하는 디바이스의 다른 앱과 협력할 수 있도록 준비(prpepare)되어야 합ㄴ디ㅏ. 또한, 디바이스의 하드웨어 컨트롤에도 응답해야합니다.

![plays-with-others.png](/resources/images/plays-with-others.png)

이 모든 동작들은 [Controlling Audio Output](https://developer.android.com/guide/topics/media-apps/volume-and-earphones.html)에 나와있습니다.


### 참고 문헌
- [안드로이드 공식문서 - Media app architecture overview](https://developer.android.com/guide/topics/media-apps/media-apps-overview)
- [안드로이드 공식문서 - Audio app overview](https://developer.android.com/guide/topics/media-apps/audio-app/building-an-audio-app)