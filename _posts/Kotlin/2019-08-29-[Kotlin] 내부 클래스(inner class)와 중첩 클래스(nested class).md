---
layout: post
title: "[Kotlin] 내부 클래스(inner class)와 중첩 클래스(nested class)"
date: 2019-08-29
categories: Kotlin
tags: Kotlin
comments: true
---

## 내부(inner) 클래스와 중첩(nested) 클래스

코틀린 [공식문서](https://kotlinlang.org/docs/reference/nested-classes.html)와 `코틀린 인 액션` 책을 참고하여 작성하였습니다. 

자바에서는 A 클래스 안에 B 클래스를 정의하면 B 클래스는 자동으로 내부 클래스가 되었습니다. 하지만 코틀린에서는 반대입니다. 한 클래스안에 다른 클래스를 정의하면 기본적으로는 중첩 클래스가 되고, 내부 클래스로 만들고 싶다면 `inner` 키워드로 클래스를 선언해야 합니다. 내부 클래스는 기본적으로 외부 클래스를 참조하게되지만 중첩 클래스는 그렇지 않습니다. 아래의 예시를 보겠습니다.

```kotlin
// nested class 중첩 클래스
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

위와 같이 중첩 클래스에서는 외부 클래스를 참조하지 않기 때문에 Outer.Nested().foo()의 값이 2가 됩니다. 

```kotlin
// inner class 내부 클래스
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

반면, 내부 클래스에서는 외부 클래스를 항상 참조하고 있기 때문에 Outer().Inner().foo()의 값이 1이 됩니다. 객체를 항상 참조하고 있다는 것은 어떤 의미를 가질까요? *(자바에서)* 객체가 삭제되는 시점은 객체가 더 이상 사용되지 않을 때입니다. 그런데 내부 클래스를 사용하면 항상 외부 클래스의 객체를 참조하기때문에 객체가 적절한 시점에 삭제되지 못하고 메모리 누수가 발생합니다. 

이러한 누수가 위험한 이유는 명시적(컴파일 시 오류가 발생하는 등 명식적으로 알 수 있는 것)인 것이 아니라 암묵적(프로그래머가 발견하기 전까지는 알 수 없는 것)인 것이기 때문입니다. 따라서 특별한 경우가 아니라면 내부 클래스 사용을 지양하고 중첩 클래스를 사용하는 것이 권장된다고 합니다. 

*~~기존의 자바와 다르게 inner 키워드를 선언해야만 내부 클래스를 사용할 수 있도록 설계한 이유와도 관련이 있을것 같다는 생각이 드는데.. 아시는 분은 알려주시면 감사하겠습니다.~~*


<!-- # Kotlin의 클래스 종류와 계층
- [open/final/abstract 클래스](#open/final/abstract-클래스)
- [public/internal/protected/private 가시성 접근자](#public/internal/protected/private-가시성-접근자)
- [봉인된(sealed) 클래스](#봉인된(sealed)-클래스) -->

### 참고 문헌
- [코틀린 공식문서](https://kotlinlang.org/docs/reference)
- [코틀린 인 액션](http://acornpub.co.kr/book/kotlin-in-action)
