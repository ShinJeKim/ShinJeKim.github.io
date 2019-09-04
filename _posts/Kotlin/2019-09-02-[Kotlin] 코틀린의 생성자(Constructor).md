---
layout: post
title: "[Kotlin] 코틀린의 생성자(Constructor)"
date: 2019-09-02
categories: Kotlin
tags: Kotlin
comments: true
---

<!-- # Kotlin의 클래스 종류와 계층
- [open/final/abstract 클래스](#open/final/abstract-클래스)
- [public/internal/protected/private 가시성 접근자](#public/internal/protected/private-가시성-접근자)
- [봉인된(sealed) 클래스](#봉인된(sealed)-클래스) -->

# 코틀린의 생성자(Constructor)

코틀린에서는 하나의 주(primary) 생성자와 여러 개의 부(secondary) 생성자를 사용할 수 있습니다. 주 생성자는 클래스의 헤더로써 클래스의 이름과 동일한 이름을 사용합니다. 만약 주 생성자에 어노테이션이나 가시성 변경자(visibility modifiers)가 없다면 생성자 키워드는 생략할 수 있습니다.

```kotlin
// constructor 키워드 사용
class Person constructor(firstName: String) {/*...*/}

// constructor 키워드 생략
class Person(firstName: String) {/*...*/}
```

보통 주 생성자는 클래스를 초기화할 떄 주로 사용하는 간략한 생성자로 클래스 본문 밖에서 정의하며 부 생성자는 클래스 본문 안에서 정의합니다. 또한 코틀린에서는 초기화 블록(initializer block)을 통해 초기화 로직을 추가할 수 있습니다. 

## 주 생성자(primary constructor)

코틀린에서는 클래스 이름 뒤에 오는 괄호로 둘러싸인 코드를 `주 생성자`라고 합니다. 주 생성자는 두 가지 목적으로 쓰입니다. 하나는 생성자 파라미터를 지정하는 것이고, 다른 하나는 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 것입니다.
아래의 1번, 2번, 3번은 모두 같은 클래스의 생성자를 정의하는 것입니다. 

```kotlin
// 1번
class User constructor(_nickname: String){
    val nickname: String
    init {
        nickname = _nickname
    }
}

// 2번
class User(_nickname: String){
    val nickname = _nickname
}

// 3번
class User(val nickname: String)
```

이 중 3번의 방식이 가장 간단합니다. 함수 파라미터처럼 생성자 파라미터에도 아래와 같이 디폴트 값을 정의할 수 있습니다.

```kotlin
class User(val nickname: String, val isSubscribed: Boolean = true)
```

클래스의 인스턴스를 만들떄에는 자바와는 달리 new 키워드 없이 생성자를 직접 호출하면 됩니다.

```kotlin
val hyun = User("유저")
```

## 부 생성자(secondary constructor)
위에서 언급했듯이 부 생성자는 클래스 본문 안에서 정의되는 생성자입니다. 아래와 같이 생성자가 2개인 View 클래스가 있다고 가정하겠습니다.

```kotlin
open class View {
    constructor(ctx: Context){ // 부 생성자
        // 코드
    }
    constructor(ctx: Context, attr: AttributeSet){ // 부 생성자
        // 코드
    }
}
```

이 클래스를 확장하면서 아래와 같이 두 가지 방식으로 부 생성자를 정의할 수 있습니다.

```kotlin
// 1번. super()를 통해 상위 클래스의 생성자를 호출함.
class MyButton : View {
    constructor(ctx: Context)
        : super(ctx){
            // ...
    }
    constructor(ctx: Context, attr: AttributeSet)
        : super(ctx, attr){

    }
}

// 2번. this()를 통해 자기 자신인 클래스의 다른 생성자를 호출함.
class MyButton : View {
    constructor(ctx: Context) : this(ctx, MY_STYLE){
        // ...
    }
    constructor(ctx: Context, attr: AttributeSet) : super(ctx, attr){
        // ...
    }
}
```

클래스에 주 생성자가 없다면 부 생성자는 반드시 상위 클래스를 초기화하거나 다른 생성자에게 생성을 위임해야 합니다. 부 생성자가 필요한 이유는 자바와의 상호 운용성과 팩토리 메소드에서 필요하기 때문입니다. 

### 참고 문헌
[코틀린 공식문서](https://kotlinlang.org/docs/reference)
[코틀린 인 액션](http://acornpub.co.kr/book/kotlin-in-action)