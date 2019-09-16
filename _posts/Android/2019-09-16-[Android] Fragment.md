---
layout: post
title: "[Android] Fragment와 라이프사이클"
date: 2019-09-16
categories: Android
tags: Android
comments: true
---

## Fragment
`Fragment`는 `FragmentActivity`내의 어떤 동작 또는 사용자 인터페이스의 일부를 나타냅니다. fragment는 자체적인 수명 주기(life cylce)를 가지며 자체 입력 이벤트를 수신하고, 액티비티 실행 중에 추가 및 삭제가 가능합니다(다른 액티비티에 재사용할 수 있는 "하위 액티비티"와 같은 개념).

## Fragment를 사용하는 이유
- 개발자가 뷰 계층에 복잡한 변경 내용을 관리하지 않아도 태블릿과 같은 큰 화면에서 역동적이고 유연한 UI 디자인을 구현할 수 있습니다.
- 액티비티의 레이아웃을 여러 fragment로 나누면 런타임에서 액티비티의 외관을 수정할 수 있습니다.
- 액티비티의 변경 내용을 액티비티가 관리하는 백 스택에 보존할 수 있습니다.
- 하나의 fragment를 여러 액티비티에 포함할 수 있습니다. 따라서 재사용을 염두에 두고 디자인해야 하며 하나의 fragment를 다른 fragment에서 직접 조작하는 것은 삼가야 합니다. 이 부분에 대한 예시는 [공식문서의 '디자인 철학' 부분](https://developer.android.com/images/fragment_lifecycle.png)에 잘 설명되어 있습니다.
- fragment의 조합을 여러가지 화면 크기에 맞춰 변경할 수 있습니다.

## Fragment 생성
![프래그먼트 수명 주기](https://developer.android.com/images/fragment_lifecycle.png)

fragment를 생성하기 위해서는 최소한 아래와 같은 라이프사이클 메서드를 구현해야만 합니다.
- `onCreate()`: fragment를 생성할 때 시스템에서 이 메소드를 호출합니다. fragment가 일시 정지(pause)되거나 중단(stop)되었다가 다시 시작(resume)될 때 유지하고자 하는 fragment의 필수 컴포넌트를 구현 내부에서 초기화해야합니다.
- `onCreateView()`: fragment가 시스템의 사용자 인터페이스를 처음으로 그릴 때, 시스템은 `onCreateView()`를 호출합니다. fragment에 맞는 UI를 그리려면 메서드에서 `View`를 반환해야합니다. 이 때 `onCreateView()`는 fragment 레이아웃의 루트(root)입니다. 만약 fragment가 UI를 제공하지 않으면 null을 리턴하면 됩니다.
- `onPause()`: 사용자가 fragment를 떠나고 있다는 첫번째 표시로 시스템은 onPause() 메소드를 호출합니다. 이는 fragment가 항상 파괴되고 있다는 의미는 아닙니다. 일반적으로 `onPause()` 메소드에서는 현재의 사용자 세션 이후에도 지속되어야 하는 변경 사항을 commit합니다(사용자가 다시 돌아오지 않을 수도 있기 때문입니다).
