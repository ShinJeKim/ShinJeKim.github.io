---
layout: post
title: "[Android] 미디어 플레이어(MediaPlayer) 개요"
date: 2019-10-11
categories: Android
tags: Android
comments: true
---

*이 글은 [안드로이드 공식문서](https://developer.android.com/guide/topics/media/mediaplayer)를 공부하며 번역한 글입니다.*

MediaPlayer 클래스는 오디오 및 비디오 파일과 스트림 재생을 위해 쓰입니다. MediaPlayer 클래스의 메소드를 쓰는 방법은 [`VideoView`](https://developer.android.com/reference/android/widget/VideoView.html)에서 볼 수 있습니다.

## 상태 다이어그램(State Diagram)
오디오 및 비디오 파일 스트림 재생은 상태 머신(state machine)으로 관리됩니다. 아래의 다이어그램은 지원되는 재생 제어 작업(palyback control operations)으로 구동되는 수명주기(lifecycle)및 MediaPlayer 객체의 상태를 보여줍니다. 타원은 MediaPlayer 객체가 상주할 수 있는 상태를 나타냅니다. 아크(arc)는 객체의 상태 전환을 구동하는 재생 제어 작업을 나타냅니다. 아크(arc)에는 두 가지 유형이 있습니다. 단일 화살촉(single arrow head)을 가진 아크(arc)는 동기 함수 호출을 나타내고, 이중 화살촉(double arrow head)이 있는 아크(arc)는 비동기식 함수 호출을 나타냅니다.

![mediaplayer-state-diagram](/resources/images/mediaplayer_state_diagram.gif)

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

- **Wake Lock Permission** - 플레이어 구동시 앱 화면이 꺼지지 않게 방지하거나 프로세서가 잠들지 않게 할 때, 혹은 [`MediaPlayer.setScreenOnWhilePlaying()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setScreenOnWhilePlaying(boolean)) 이나 [`MediaPlayer.setWakeMode()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setWakeMode(android.content.Context,%20int))를 사용할때에는 이 권한의 허용을 요청해야만 합니다.

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

> ***주의***: `setDataSource()`를 사용할때에는 `IllegalArgumentException`과 `IOException`을 catch하거나 pass하도록 해야합니다. 참조하고있는 해당 파일이 존재하지 않을 수도 있기 때문입니다.

### 비동기 준비(Asynchronous preparation)
[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 사용할 때에 주의해야할 점이 몇 가지 있습니다. 예를 들면, [`prepare()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepare()) 호출은 미디어 데이터를 가져오고 디코딩하는 과정을 포함할 수 있기때문에 실행하는 데에 시간이 많이 걸릴 수 있습니다. 따라서 이와 같이 실행하는데에 시간이 많이 걸릴 수 있는 메서드는 ***절대 애플리케이션의 UI 스레드에서 호출하면 안됩니다.*** 만약 UI 스레드에서 시간이 오래 걸리는 메서드를 호출한다면, 해당 메서드가 리턴될때까지 UI가 멈춰있게되며 이는 매우 나쁜 사용자 경험을 제공합니다. 또한 이는 ANR(Application Not Responding) 에러를 발생시킬 수도 있습니다. 만약 당신의 리소스가 빨리 다운로드 될 것이라고 기대할지라도, UI 응답이 1/10초 이상 걸리는 것은 일시적으로 정지된다는 것을 감지할 수 있는 정도이며 이는 사용자에게 앱이 느리다는 인상을 줄 수 있습니다.

이를 방지하기 위해서는 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 위한 별도의 스레드를 만들어서 작업이 완료되었을 때 메인스레드에 알려주어야합니다. 이 로직을 직접 작성할 수도 있지만 안드로이드 프레임워크에서 제공하는 [`prepareAsync()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepareAsync())라는 메서드를 사용하면 간편하게 세팅할 수 있습니다. 이 메서드는 백그라운드에서 미디어 준비를 시작하고 바로 리턴합니다. 미디어 준비가 완료되면 [`setOnPreparedListener()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setOnPreparedListener(android.media.MediaPlayer.OnPreparedListener))를 통해 구성된 [`MediaPlayer.OnPreparedListener`](https://developer.android.com/reference/android/media/MediaPlayer.OnPreparedListener.html)의 [`onPrepared()`](https://developer.android.com/reference/android/media/MediaPlayer.OnPreparedListener.html#onPrepared(android.media.MediaPlayer)) 메서드가 호출됩니다. 

### 상태 관리하기(Managing state)
[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)에서 기억해야 할 또 다른 중요한 것은 미디어 플레이어는 상태 베이스(state-based)라는 것입니다. 즉, [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)는 내부적으로 '상태'를 가지고 있으며 특정 operation은 플레이어가 특정 상태일때만 유효하므로, 코드를 작성할 때 유의해야 합니다. 만약 잘못된 상태일 때 operation을 수행하려 한다면 시스템이 예외를 발생시키거나 바람직하지 않은 동작을 발생시킬 수도 있습니다. 

[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 클래스의 공식 문서에는 한 상태에서 다른 상태로 이동할 어떤 메서드가 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 움직이는지 보여주는 상태 다이어그램이 나와있습니다. 예를 들면, 새로운 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 생성할때에 MediaPlayer는 *Idle* 상태에 있습니다. 이 시점에서 [`setDataSource()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setDataSource(android.content.Context,%20android.net.Uri))를 호출하고 초기화하여 *initialized* 상태로 옮겨가야합니다. 이후에는, [`prepare()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepare()) 혹은 [`prepareAsync()`](https://developer.android.com/reference/android/media/MediaPlayer.html#prepareAsync()) 메서드를 사용하여 MediaPlayer를 준비해야합니다. [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)의 준비가 완료되면 *Prepared* 상태에 돌입하게 되는데, 이는 미디어를 재생하기 위한 [`start()`](https://developer.android.com/reference/android/media/MediaPlayer.html#start())를 호출할 수 있다는 의미입니다. 이 시점에서, 다이어그램이 보여주듯이, [`start()`](https://developer.android.com/reference/android/media/MediaPlayer.html#start()), [`pause()`](https://developer.android.com/reference/android/media/MediaPlayer.html#pause()), [`seekTo()`](https://developer.android.com/reference/android/media/MediaPlayer.html#seekTo(int))와 같은 메서드를 호출하여 *Started, Paused, PlaybackCompleted*의 상태들 사이를 이동할 수 있습니다. 하지만 한 번 [`stop()`](https://developer.android.com/reference/android/media/MediaPlayer.html#stop())을 호출하고 나면 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html)를 다시 준비하기 전까지는 [`start()`](https://developer.android.com/reference/android/media/MediaPlayer.html#start())를 호출할 수 없습니다. 

[`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 객체와 상호작용하는 코드를 작성할 때에는 항상 [상태 다이어그램](https://developer.android.com/images/mediaplayer_state_diagram.gif)을 염두에 두세요. 잘못된 상태에서 메서드를 호출하는 것은 자주 발생하는 버그의 원인입니다. 

## 서비스에서 MediaPlayer 사용하기(Using MediaPlayer in a service)
애플리케이션이 스크린에서 보이지 않을때에도 백그라운드에서 앱이 동작하도록 만들고 싶다는 것은, 사용자가 다른 애플리케이션과 상호작용 할 때에도 당신의 애플리케이션의 미디어가 재생되게 하고 싶다는 것입니다. 그렇게 하기 위해서는 우선 Service를 시작한 다음 여기에서 [`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 인스턴스를 제어해야만 합니다. MediaPlayer를 [`MediaBrowserServiceCompat`](https://developer.android.com/reference/androidx/media/MediaBrowserServiceCompat.html)에 임베드하여 다른 액티비티의 [`MediaBrowserCompat`](https://developer.android.com/reference/android/support/v4/media/MediaBrowserCompat.html)과 상호작용 하도록 해야합니다.

이 때 클라이언트/서버 설정에 주의해야합니다. 백그라운드 서비스에서 실해중인 플레이어가 시스템의 다른 부분과 어떻게 상호작용 하는지에 대한 기대(expectations)가 있습니다. 만약 당신의 애플리케이션이 이러한 기대를 충족하지 못한다면 사용자에게 좋지 않은 경험을 줄 수 있습니다. 자세한 내용은 [Building an Audio App](https://developer.android.com/guide/topics/media-apps/audio-app/building-an-audio-app.html)을 참조하시면 됩니다.

이 섹션에서는 서비스 내부에서 MediaPlayer를 구현할 때 이를 관리하기 위한 지침에 대해 다룹니다. 

### 비동기로 실행하기(Running asynchronously)
우선, [Activity](https://developer.android.com/reference/android/app/Activity.html)처럼, [Service](https://developer.android.com/reference/android/app/Service.html)에서의 모든 작업은 기본적으로 싱글 스레드로 이뤄집니다. 즉, 동일한 애플리케이션 내에서 액티비티와 서비스를 실행한다면 디폴트로 동일한 스레드(메인 스레드)를 사용하게될 것입니다. 따라서 서비스는 들어오는 intent를 빠르게 처리해야하며, intent에 응답할 때 긴 계산(lengthy computations)을 수행하지 않아야 합니다. 무거운 작업이나 호출 차단(blocking calls)이 예상되는 경우에는 직접 구현하거나 프레임워크에서 제공하는 비동기 프로세스를 사용하여 비동기적으로 처리해야합니다.

예를 들어, `MediaPlayer`를 메인 스레드에서 사용하는 경우에는, 준비가 완료된 후 재생을 시작하기 위한 알림을 받기 위해서는 `prepare()` 대신 `prepareAsync()`를 사용해야하며, [`MediaPlayer.OnpreparedListener`](https://developer.android.com/reference/android/media/MediaPlayer.OnPreparedListener.html)를 구현해야만합니다.

아래는 예제 코드입니다.

```kotlin
private const val ACTION_PLAY: String = "com.example.action.PLAY"

class MyService: Service(), MediaPlayer.OnPreparedListener {

    private var mMediaPlayer: MediaPlayer? = null

    override fun onStartCommand(intent: Intent, flags: Int, startId: Int): Int {
        ...
        val action: String = intent.action
        when(action) {
            ACTION_PLAY -> {
                mMediaPlayer = ... // 여기서 MediaPlayer를 초기화합니다.
                mMediaPlayer?.apply {
                    setOnPreparedListener(this@MyService)
                    prepareAsync() // 메인스레드를 block 하지 않기 위해 비동기를 준비합니다.
                }
            }
        }
        ...
    }

    /** MediaPlayer가 준비되었을 때 호출됩니다.*/
    override fun onPrepared(mediaPlayer: MediaPlayer){
        mediaPlayer.start()
    }
}
```

### 비동기 오류 처리하기(Handling asynchronous errors)
동기로 작업할때에는 일반적으로 오류는 에러 코드로 알 수 있습니다. 하지만 비동기를 사용한다면 애플리케이션에서 에러를 적절히 알 수 있는지 확인해야만 합니다. `MediaPlayer`의 경우, [`MediaPlayer.OnErrorListener`](https://developer.android.com/reference/android/media/MediaPlayer.OnErrorListener.html)를 구현하고 `MediaPlayer` 인스턴스에 설정함으로써 이를 해결할 수 있습니다.

```kotlin
class MyService : Service(), MediaPlayer.OnErrorListener {
    private var mediaPlayer: MediaPlayer? = null

    fun iniMediaPlayer() {
        // 여기에서 MediaPlayer를 초기화합니다.
        mediaPlayer?.setOnErrorListener(this)
    }

    override fun onError(mp: MediaPlayer, wha t: Int, extra: Int): Boolean {
        // ... react appropriately ...
        //  MediaPlayer가 오류 상태로 접어들었음로 리셋해야합니다.
    }
}
```

오류가 발생했을 때, `MediaPlayer`가 *Error* 상태로 접어들었다는 것을 기억하는 것이 중요합니다([`MediaPlayer`](https://developer.android.com/reference/android/media/MediaPlayer.html) 클래스의 전체 상태 다이어그램을 참조하세요). 오류 상태가 되었다면 미디어 플레이어를 다시 사용하기 전에 꼭 리셋해주어야합니다.

### Using wake locks
백그라운드에서 미디어를 재생하는 애플리케이션을 설계할 때, 서비스를 실행하는 동안 기기(device)가 절전 모드(sleep)로 전환될 수 있습니다. 안드로이드 시스템은 배터리 사용량을 최소화하려 하기 때문에, 기기가 잠자고 있을 때에는 CPU와 와이파이 하드웨어를 포함하여 불필요한 기능들을 끄도록 되어있습니다. 그러나 음악 재생이나 스트리밍 서비스를 만들려면, 이러한 시스템이 재생을 방해하지 않도록 하고 싶을 것입니다.

위와 같은 상황에서 당신의 서비스가 계속 실행되도록 보장하려면 "wake locks"를 사용해야합니다. wake lock은 핸드폰이 idle 상태이더라도 애플리케이션에서 특정 기능을 사용할 수 있도록 시스템이 특정 신호를 보내는 방식입니다. 

> **주의:** wake lock은 기기의 배터리 수명을 상당히 단축시키기때문에, 정말 필요할때에만 최소한으로 사용하고 유지해야합니다.

CPU가 `MediaPlayer`를 재생하고 있는 동안에도 실행되고 있다는 것을 보장하려면, `MediaPlayer`를 초기화할 때 [`setWakeMode()`](https://developer.android.com/reference/android/media/MediaPlayer.html#setWakeMode(android.content.Context,%20int)) 메소드를 호출하면 됩니다. 이렇게 하면, `MediaPlayer`는 재생하는 동안 지정된 잠금을 유지하고 일시중지(pause) 중단(stop)되면 잠금을 해제합니다.

```kotlin
mediaPlayer = MediaPlayer().apply {
    // 여기에서 기타 다른 것들을 초기화합니다.
    setWakeMode(applicationContext, PowerManager.PARTIAL_WAKE_LOCK)
}
```

하지만, 위의 예제를 통해 얻어진 wake lock은 CPU가 꺠어있도록 하는 것만을 보장합니다. 만약 네트워크를 통해 미디어를 스트리밍하고 와이파이를 사용한다면, [`WifiLock`](https://developer.android.com/reference/android/net/wifi/WifiManager.WifiLock.html)을 유지하고 싶을 것입니다. 이 또한 직접 구현해야합니다. 따라서 원격 URL(remote URL)을 사용하여 `MediaPlayer`을 준비할때에는 와이파이 잠금을 create하고 acquire 해야합니다. 아래는 예제 코드입니다.

```kotlin
val wifiManager = getSystemService(Context.WIFI_SERVICE) as WifiManager
val wifiLock: WifiManager.WifiLock = 
    wifiManager.createWifiLock(WifiManager.WIFI_MODE_FULL, "mylock")

wifiLock.acquire()
```

미디어를 일시정지(pause) 혹은 중단(stop)하거나, 네트워크가 더 이상 필요하지 않을 때에는 아래와 같이 잠금을 해제해야합니다:

```kotlin
wifiLock.release()
```

### Performing cleanup
이전에도 언급했듯이, `MediaPlayer` 객체는 상당히 많은 양의 시스템 자원을 소모합니다. 그렇기때문에 딱 필요한 만큼만 유지하고 작업을 완료했으면 `release()`를 호출해야합니다. 가비지 컬렉터는 메모리에는 민감하지만 다른 미디어 관련 자원의 부족에는 민감하지 않기 때문에 가비지 컬렉터가 `MediaPlayer`를 회수하는 데에는 시간이 많이 걸릴 수 있습니다. 따라서 시스템상의 가비지 컬렉션에 의존하기보다는 명시적으로 위의 cleanup 메소드를 호출하는 것이 중요합니다. 따라서 이러한 상항에서는 서비스를 사용할 때 항상 [`onDestroy()`](https://developer.android.com/reference/android/app/Service.html#onDestroy()) 메소드를 오버라이딩하여 `MediaPlayer`를 release해야 합니다.

```kotlin
class MyService : Service() {

    private var mediaPlayer: MediaPlayer? = null
    // ...

    override fun onDestroy(){
        super.onDestroy()
        mediaPlayer?.release()
    }
}
```

서비스를 종료할 때에 `MediaPlayer`를 release하는 것 이외에도 항상 release를 할 수 있는 기회를 찾아야합니다. 예를 들자면, 미디어를 일정 시간 동안 재생할 수 없다고 기대되는 경우(예: 오디오 포커스를 잃은 경우)에는 현재 존재하는 `MediaPlayer`를 release하고 나중에 다시 새로 create해야 합니다. 반면, 만약 아주 짧은 시간동안만 재생을 중단할 것 같은 경우에는 다시 새로 create하고 prepare하는 오버헤드를 방지하기 위해 `MediaPlayer`를 계속 유지해야 합니다.


## Digital Rights Management (DRM)
이 부분은 추후에 추가하도록 하겠습니다.

## 암호화된 미디어 처리하기(Handling encrypted media)
안드로이드 8.0(API 레벨 26) 부터 `MediaPlayer`는 Common Encryption Scheme(CENC)와 HLS sample-level encrypted media(METHOD=SAMPLE-AES)를 기본 스트림 유형 H.264와 AAC로 decrypt할 수 있게 되었습니다. Full-segment encrypted media(METHOD=AES-128)는 이전에도 지원되었습니다.

## 콘텐트리졸버에서 미디어 가져오기(Retrieving media from a ContentResolver)
미디어 재생 애플리케이션에서 유용한 기능 중 하나는 사용자의 디바이스에 있는 음악을 가져올 수 있는 기능입니다. [`ContentResolver`](https://developer.android.com/reference/android/content/ContentResolver.html) 쿼리를 이용하여 외부의 미디어를 가져올 수 있습니다.

```kotlin
val resolver: ContentResolver = contentResolver
val uri = android.provider.MediaStore.Audio.Media.EXTERNAL_CONTENT_URI
val cursor: Cursor? = resolver.query(uri, null, null, null, null)
when {
    cursor == null -> {
        // 쿼리가 실패했을때. 여기에서 에러를 처리하세요.
    }
    !cursor.moveToFirst() -> {
        // 디바이스에 미디어가 없을 때.
    }
    else -> {
        val titleColumn: Int = cursor.getColumnIndex(android.provider.MediaStore.Audio.Media.TITLE)
        val idColumn: Int = cursor.getColumnIndex(android.provider.MediaStore.Audio.Media._ID)
        do {
            val thisId = cursor.getLong(idColumn)
            val thisTitle = cursor.getString(titleColumn)
            // ...process entry...
        } while (cursor.moveToNext())
    }
}
cursor?.close()
```

이를 `MediaPlayer`와 함께 사용하려면 아래의 코드처럼 할 수 있습니다.

```kotlin
val id: Long = /* retrieve it from somewhere */
val contentUri: Uri =
    ContentUris.withAppendedId(android.provider.MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, id )

mediaPlayer = MediaPlayer().apply {
    setAudioStreamType(AudioManager.STREAM_MUSIC)
    setDataSource(applicationContext, contentUri)
}

// ...prepare and start...
```

## 샘플 코드(Sample code)
[android-SimpleMediaPlayer](https://github.com/googlesamples/android-SimpleMediaPlayer/) 코드 샘플은 독립적인 플레이어를 만드는 방법을 보여줍니다. [BasicMediaDecoder](https://github.com/android/media-samples/tree/master/BasicMediaDecoder/)와 [DeviceOwner](https://github.com/android/enterprise/tree/master/DeviceOwner/) 샘플은 이 페이지에서 다루는 API 사용법을 보여줍니다.


### 참고문헌
- [안드로이드 공식문서 - MediaPlayer](https://developer.android.com/reference/android/media/MediaPlayer)
- [안드로이드 공식문서 - MediaPlayer 개요](https://developer.android.com/guide/topics/media/mediaplayer)