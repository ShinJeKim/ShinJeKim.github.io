---
layout: post
title: "[Kotlin] 프로퍼티와 필드(Properties and Fields)"
date: 2019-09-02
categories: Kotlin
tags: Kotlin
comments: true
---

# 프로퍼티와 필드(Properties and Fields)
## 프로퍼티(properties)
코틀린에서는 두 가지 방법으로 프로퍼티를 선언할 수 있습니다. 변경이 가능한(mutable) 변수는 `var`로 선언합니다. 반면 읽을 수만 있고 값을 변경할 수 없는(immutable) 변수는 `val`로 선언합니다. 

```kotlin
class Address {
    var name: String = "Holmes, Sherlock"
    var street: String = "Baker"
    var city: String = "London"
    var state: String? = null
    var zip: String = "123456"
}
```

프로퍼티를 사용할 때에는 이름만 명시하면 됩니다.
```kotlin
fun copyAddress(address: Address): Address {
    val result = Address() // 코틀린에서는 'new' 키워드를 사용하지 않습니다.
    result.name = address.name // 접근자를 호출합니다.
    result.street = address.street
    // ...
    return result
}
```

## 인터페이스에 선언된 프로퍼티 구현하기
코틀린에서는 아래와 같이 인터페이스에 추상 프로퍼티 선언을 넣을 수 있습니다. 
```kotlin
interface User {
    val nickname: String
}
```
이는 User 인터페이스를 구현하는 클래스가 nickname의 값을 얻을 수 있는 방법을 제공해야 한다는 뜻입니다. 인터페이스는 상태를 포함할 수 없으므로 상태를 저장하기 위해서는 하위 클래스에서 상태 저장을 위한 프로퍼티 등을 만들어야 합니다. 아래의 예시를 통해 세 가지 방법으로 인터페이스를 구현해보겠습니다.
```kotlin
// 1번
class PrivateUser(override val nickname: String) : User

// 2번
class SubscribingUser(val email: String) : User {
    override val nickname: String
        get() = email.substringBefore('@')
}

// 3번 
class FacebookUser()
```