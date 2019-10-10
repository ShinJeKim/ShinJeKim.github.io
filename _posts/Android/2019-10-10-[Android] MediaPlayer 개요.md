---
layout: post
title: "[Android] 미디어 플레이어(MediaPlayer) 개요"
date: 2019-10-10
categories: Android
tags: Android
comments: true
---

## The basics
안드로이드 프레임워크에서 소리와 영상을 재생하기 위해 사용되는 클래스는 아래와 같습니다.
- [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)
  
  소리와 영상을 재생하기 위한 기본 API입니다.

- [`AudioManager`](https://developer.android.com/reference/android/media/AudioManager.html)
  
  기기의 오디오 소스와 오디오 출력을 관리하는 클래스입니다.

## 매너페스트 선언하기(Manifest declarations)
애플리케이션에서 MediaPlayer를 사용하기 위해서는 매너페스트 파일에 적절한 선언을 해주어야 합니다.
- **Internet Permission** - 스트림 네트워크 기반의 MediaPlayer를 사용한다면 애플리케이션이 네트워크 접근을 요청해야만 합니다.
```xml
<uses-permission android:name="android.permission.INTERNET" />
```
- **Wake Lock Permission** - 플레이어 구동시 앱 화면이 꺼지지 않게 방지하거나 프로세서가 잠들지 않게 할 때, 혹은 [`MediaPlayer.setScreenOnWhilePlaying()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setScreenOnWhilePlaying(boolean)) 이나 [`MediaPlayer.setWakeMode()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setWakeMode(android.content.Context,%20int))를 사용할떄에는 이 권한의 허용을 요청해야만 합니다.
```xml
<uses-permission android:name="android.permission.WAKE_LOCK" />
```

## MediaPlayer 사용하기(Using MediaPlayer)
미디어 프레임워크의 가장 중요한 컴포넌트 중 하나는 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 클래스입니다. 이 클래스의 객체는 최소한의 세팅으로 오디오와 비디오 모두를 가져오고(fetch), 디코딩하고(decode), 재생(play)할 수 있습니다. 이 클래스는 아래와 같은 여러 종류의 미디어를 지원합니다.
- 로컬 리소스(Local resources)
- 내부의 URI(Internal URIs, such as one you might obtain from a Content Resolver)
- 외부의 URL(External URLs (streaming))

[Supported Media Formats](https://developer.android.com/guide/topics/media/media-formats.html) 페이지에서 안드로이드에서 지원하는 미디어 형식을 확인할 수 있습니다.

아래는 로컬의 raw 리소스(당신의 앱의 `res/raw/` 디렉토리에 위치한 리소스)를 재생하도록 하는 예시 코드입니다.

```kotlin
var mediaPlayer: MediaPlayer? = MediaPlayer.create(context, R.raw.sound_file_1)
mediaPlayer?.start() // prepare()를 호출할 필요가 없습니다; create()가 같은 역할을 해줍니다.
```

위의 예시에서 "raw" 리소스는 시스템이 특정한 방식으로 파싱할 필요가 없는 파일입니다. 하지만 이 리소스의 컨텐츠가 raw 오디오여서는 안됩니다. 리소스의 컨텐츠는 안드로이드에서 지원되는 양식에 따라 적절한 방식으로 인코딩되고 포맷팅 된 미디어 파일이어야합니다.

아래는 로컬 시스템의 URI에서(예를 들어 Content Resolver를 통해 얻은) 미디어를 재생하는 예제 코드입니다.

```kotlin
val myUri: Uri = .... // 이곳에서 Uri를 초기화합니다.
val mediaPlayer: MediaPlayer? = MediaPlayer().apply {
    setAudioStreamType(AudioManager.STREAM_MUSIC)
    setDataSource(applicationContext, myUri)
    prepare()
    start()
}
```

HTTP 스트리밍을 통해 리모트 URL로부터 미디어를 재생하는 방법은 아래와 같습니다.

```kotlin
val url = "http://........" // 이곳에 URL을 입력합니다.
val mediaPlayer: MediaPlayer? = MediaPlayer().apply {
    setAudioStreamType(AudioManager.STREAM_MUSIC)
    setDataSource(url)
    prepare() // 오랜 시간이 걸릴 수도 있습니다! (buffering 혹은 기타 등등)
    start()
}
```

> **중요:** 온라인상의 미디어 파일을 스트림하기 위한 URL을 전달한다면, 해당 파일은 [progressive download](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%A0%88%EC%8B%9C%EB%B8%8C_%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C)가 가능한 파일이어야만 합니다.

> ***주의***: `setDataSource()`를 사용할떄에는 `IllegalArgumentException`과 `IOException`을 catch하거나 pass하도록 해야합니다. 참조하고있는 해당 파일이 존재하지 않을 수도 있기 때문입니다.

### 비동기 준비(Asynchronous preparation)
[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 사용할 때에 주의해야할 점이 몇 가지 있습니다. 예를 들면, [`prepare()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepare()) 호출은 미디어 데이터를 가져오고 디코딩하는 과정을 포함할 수 있기때문에 실행하는 데에 시간이 많이 걸릴 수 있습니다. 따라서 이와 같이 실행하는데에 시간이 많이 걸릴 수 있는 메서드는 ***절대 애플리케이션의 UI 스레드에서 호출하면 안됩니다.*** 만약 UI 스레드에서 시간이 오래 걸리는 메서드를 호출한다면, 해당 메서드가 리턴될때까지 UI가 멈춰있게되며 이는 매우 나쁜 사용자 경험을 제공합니다. 또한 이는 ANR(Application Not Responding) 에러를 발생시킬 수도 있습니다. 만약 당신의 리소스가 빨리 다운로드 될 것이라고 기대할지라도, UI 응답이 1/10초 이상 걸리는 것은 일시적으로 정지된다는 것을 감지할 수 있는 정도이며 이는 사용자에게 앱이 느리다는 인상을 줄 수 있습니다.

이를 방지하기 위해서는 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 위한 별도의 스레드를 만들어서 작업이 완료되었을 때 메인스레드에 알려주어야합니다. 이 로직을 직접 작성할 수도 있지만 안드로이드 프레임워크에서 제공하는 [`prepareAsync()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepareAsync())라는 메서드를 사용하면 간편하게 세팅할 수 있습니다. 이 메서드는 백그라운드에서 미디어 준비를 시작하고 바로 리턴합니다. 미디어 준비가 완료되면 [`setOnPreparedListener()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setOnPreparedListener(android.media.MediaPlayer.OnPreparedListener))를 통해 구성된 [`MediaPlayer.OnPreparedListener`](https://developer.android.com/reference/android/media/MediaPlayer.OnPreparedListener.html)의 [`onPrepared()`](https://developer.android.com/reference/android/media/MediaPlayer.OnPreparedListener.html#onPrepared(android.media.MediaPlayer)) 메서드가 호출됩니다. 

### 상태 관리하기(Managing state)
[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)에서 기억해야 할 또 다른 중요한 것은 미디어 플레이어는 상태 베이스(state-based)라는 것입니다. 즉, [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)는 내부적으로 '상태'를 가지고 있으며 특정 operation은 플레이어가 특정 상태일때만 유효하므로, 코드를 작성할 때 유의해야 합니다. 만약 잘못된 상태일 때 operation을 수행하려 한다면 시스템이 예외를 발생시키거나 바람직하지 않은 동작을 발생시킬 수도 있습니다. 

[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 클래스의 공식 문서에는 한 상태에서 다른 상태로 이동할 어떤 메서드가 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 움직이는지 보여주는 상태 다이어그램이 나와있습니다. 예를 들면, 새로운 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 생성할때에 MediaPlayer는 *Idle* 상태에 있습니다. 이 시점에서 [`setDataSource()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setDataSource(android.content.Context,%20android.net.Uri))를 호출하고 초기화하여 *initialized* 상태로 옮겨가야합니다. 이후에는, [`prepare()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepare()) 혹은 [`prepareAsync()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepareAsync()) 메서드를 사용하여 MediaPlayer를 준비해야합니다. [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)의 준비가 완료되면 *Prepared* 상태에 돌입하게 되는데, 이는 미디어를 재생하기 위한 [`start()`](https://developer.android.com/reference/android/media/MediaPlayer.html#start())를 호출할 수 있다는 의미입니다. 이 시점에서, 다이어그램이 보여주듯이, [`start()`](https://developer.android.com/reference/android/media/MediaPlayer.html#start()), [`pause()`](https://developer.android.com/reference/android/media/MediaPlayer.html#pause()), [`seekTo()`](https://developer.android.com/reference/android/media/MediaPlayer.html#seekTo(int))와 같은 메서드를 호출하여 *Started, Paused, PlaybackCompleted*의 상태들 사이를 이동할 수 있습니다. 하지만 한 번 [`stop()`](https://developer.android.com/reference/android/media/MediaPlayer.html#stop())을 호출하고 나면 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 다시 준비하기 전까지는 [`start()`](https://developer.android.com/reference/android/media/MediaPlayer.html#start())를 호출할 수 없습니다. 

[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 객체와 상호작용하는 코드를 작성할 때에는 항상 [상태 다이어그램](https://developer.android.com/images/mediaplayer_state_diagram.gif)을 염두에 두세요. 잘못된 상태에서 메서드를 호출하는 것은 자주 발생하는 버그의 원인입니다. 

## 서비스에서 MediaPlayer 사용하기(Using MediaPlayer in a service)
애플리케이션이 스크린에서 보이지 않을때에도 백그라운드에서 앱이 동작하도록 만들고 싶다는 것은, 사용자가 다른 애플리케이션과 상호작용 할 때에도 당신의 애플리케이션의 미디어가 재생되게 하고 싶다는 것입니다. 그렇게 하기 위해서는 우선 Service를 시작한 다음 여기에서 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 인스턴스를 제어해야만 합니다. MediaPlayer를 [`MediaBrowserServiceCompat`](https://developer.android.com/reference/androidx/media/MediaBrowserServiceCompat.html)에 임베드하여 다른 액티비티의 [`MediaBrowserCompat`](https://developer.android.com/reference/android/support/v4/media/MediaBrowserCompat.html)과 상호작용 하도록 해야합니다.

이 떄 클라이언트/서버 설정에 주의해야합니다. 백그라운드 서비스에서 실해중인 플레이어가 시스템의 다른 부분과 어떻게 상호작용 하는지에 대한 기대(expectations)가 있습니다. 만약 당신의 애플리케이션이 이러한 기대를 충족하지 못한다면 사용자에게 좋지 않은 경험을 줄 수 있습니다. 자세한 내용은 [Building an Audio App](https://developer.android.com/guide/topics/media-apps/audio-app/building-an-audio-app.html)을 참조하시면 됩니다.

이 섹선에서는 서비스 내부에서 MediaPlayer를 구현할 때 이를 관리하기 위한 지침에 대해 다룹니다. 

### 비동기로 실행하기(Running asynchronously)
