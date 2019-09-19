---
layout: post
title: "[Kotlin] 2019-09-16"
date: 2019-09-16
categories: Kotlin
tags: Kotlin
comments: true
---

# 코틀린의 널 안전성(Null Safety)

## 널이 될 수 있는 타입과 널이 될 수 없는 타입(Nullable types and Non-Null Types)
코틀린의 타입(type) 시스템은 [Billion Dollar Mistake](http://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)라고도 알려진 null 참조 코드의 위험성을 없애기 위한 것입니다.

Java를 포함한 많은 프로그래밍 언어에서 가장 일반적인 함정 중 하나는, null 참조의 멤버에 접근하면 null 참조 예외(null reference exception)가 발생한다는 것입니다. Java에서는 이것을 `NullPointerException` 또는 `NPE`라고 합니다.

코틀린의 타입(type) 시스템은 코드에서 `NullPointerExeption`을 제거하기 위한 것입니다. NPE의 유일한 원인은 아래와 같습니다.

- `throw NullPointerException`을 명시적으로 호출하는 것;
- `!!` 


---

NullPointerException ()을 던지기 위해 밝혀진;
사용 !! 저에 대한 설명 연산자;
다음과 같은 초기화 및 관련 내용
생성자에서 가장 효과적인 방법 ( "누설").
수퍼 클래스 생성자가 제공하는 클래스를 구현할 수 있습니다.
자바 상호 운영 :
플랫폼 유형의 널 참조에서 멤버에 액세스 할 수 있습니다.
여러 nullable과 같은 Java 상호 작동 및 사용 가능 일반 형식 (예 : Java 코드가 Kotlin MutableList <String>에 null을 추가 할 수있게되었습니다.
외부 Java 코드로 인한 기타 문제