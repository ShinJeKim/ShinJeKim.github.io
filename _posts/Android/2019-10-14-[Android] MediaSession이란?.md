---
layout: post
title: "[Android] MediaSession이란?"
date: 2019-10-14
categories: Android
tags: Android
comments: true
---

이 글은 Android Developers의 Medium 글인 [Understanding MediaSession](https://medium.com/androiddevelopers/understanding-mediasession-part-1-3-e4d2725f18e4)을 공부하며 정리한 글입니다.

## MediaSession이란 무엇인가?
Android Developers Medium에서 정의된 바는 아래와 같습니다.
> MediaSession이란 `outside actors`가 애플리케이션의 미디어 플레이어를 제어할 수 있도록 해주는 미들웨어(middleware)입니다.

> `outside actors`란? 
> 애플리케이션의 외부에서 미디어를 제어하는 것들("things")을 "outside actors"라고 합니다.

MediaSession은 애플리케이션이 플레이의 상태(어떤 미디어가 load되었는지, 플레이어가 현재 중단되었는지 재생중인지 등)를 다른 interested parties나 애플리케이션의 내/외부로 report할 수 있도록 해줍니다.

**MediaSession의 역할**은 다음과 같습니다.

- **Playback Control**
  
  앱 내의 UI나 외부의 actors(예: 미디어 버튼, 구글 어시스턴트 등)가 제어할 수 있는 단일 인터페이스(single interface)를 제공합니다.

- **State Sync**
  
  현재의 재생 상태(playing, paused, stopped, etc)와 미디어 메타데이터(album art, song duration, song title, etc)를 외부의 actors와 애플리케이션 자체에 broadcast합니다.

사용자들은 애플리케이션의 UI를 사용하는 대신 구글 어시스턴트에 "음악 꺼줘"라고 말하며 음악을 끄고 싶을 수도 있습니다. 또한 앱의 UI를 사용하지 않고 헤드폰의 미디어키를 사용함으로써 음악을 재생/중단하고 싶을 수도 있습니다. 이를 구현하기 위해서는 outside actors(구글 어시스턴드, 헤드폰의 미디어키 등 애플리케이션의 내부 요소가 아닌 것들)가 애플리케이션 내부의 상태를 변화시켜야만 하는데, 이 때 MediaSession이 필요합니다.

여기서 주의해야 할 것은 *MediaSession은 MediaPlayer의 역할을 하지 않는다*는 것입니다. MediaSession은 앱의 MediaPlayer를 관리해주지 않습니다. 즉, MediaSession을 사용하더라도 MediaPlayer를 생성하고, load하고, 재생을 위해 prepare하는 등의 모든 작업을 (당연히) 해야 합니다.