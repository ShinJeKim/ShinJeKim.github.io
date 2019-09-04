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

## 게터와 세터(Getters and Setters)
프로퍼티를 선언하는 full syntax는 아래와 같습니다. 프로퍼티란 필드와 accessor 메소드를 자동으로 생성해주는 문법을 의미합니다. 

```kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

getter와 setter를 사용하는 것은 옵션입니다. 만약 아래와 같이 초기화 값이나 getter/setter의 리턴 타입으로 프로퍼티의 타입을 유추할 수 있으면 프로퍼티의 타입은 생략해도 됩니다.

```kotlin
var allByDefault: Int? // 에러: 명시적인 초기화를 해주어야만 함. 디폴트 getter/setter가 포함됨.
var initialized = 1 // Int 타입이며, 디폴트 getter/setter를 가짐
```

변경할 수 없는 프로퍼티를 선언하는 방식은 변경 가능한 프로퍼티를 선언하는 방식과 다릅니다. 첫 번쨰로, 변경할 수 없는 프로퍼티 선언은 `val` 키워드로 해야합니다. 

```kotlin
val simple: Int? // Int 타입이며 디폴트 getter를 가짐. 생성자에서 초기화해주어야만 한다.
val inferredType = 1 // Int 타입이며 디폴트 getter를 가짐.
```

프로퍼티에 커스텀 접근자를 지정할수도 있습니다. getter는 프로퍼티에 접근할때마다 사용합니다. 아래는 커스텀 getter의 예시입니다.

```kotlin
val isEmpty: Boolean
    get() = this.size == 0
``` 

커스텀 setter를 지정할 수도 있습니다. setter는 프로퍼티에 값을 할당할때 사용합니다. 아래는 커스텀 setter의 예시입니다. 관례적으로 setter 파라미터의 이름은 `value`로 사용합니다. 원한다면 선호하는 다른 이름을 사용할수도 있습니다. 

```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(Vaule) // String을 parse하여 다른 프로퍼티에 값을 할당함.
    }
```

코틀린 1.1부터는 getter로부터 타입을 추론할 수 있다면 아래와 같이 타입 선언을 생략할 수 있게 되었습니다.

```kotlin
var isEmpty get() = this.size == 0 // Boolean 타입임을 추론할 수 있음.
```

접근자의 가시성이나 기본 구현을 변경할 필요가 없다면 아래와 같이 본문을 정의하지 않고 구현할 수도 있습니다. 

```kotlin
var setterVisibility: String = "abc"
    private set // setter는 private이고 디폴트 구현이 되어있음.
var setterWithAnnotation: Any? = null
    @Inject set // Inject로 setter를 선언함.
```

## Backing Fields(뒷받침하는 필드)
코틀린 클래스에서는 필드를 직접적으로 선언할 수 없습니다. 따라서 값을 저장하는 동시에 로직을 실행할 수 있게 하기 위해서는 접근자 안에서 프로퍼티를 뒷받침하는 필드(backing field)가 있어야 합니다. 접근자의 본문에서는 `field` 식별자를 이용하여 backing field에 접근할 수 있습니다. getter에서는 field값을 읽을수만 있고 setter에서는 field 값을 읽거나 쓸 수 있습니다. 

```kotlin
var counter = 0 // 이 initializer는 backing field를 직접 할당함.
    set(value) {
        if(value >= 0) field = value
    }
```

여기서 사용된 `field` 식별자는 이처럼 프로퍼티의 접근자에서만 사용될 수 있습니다.
backing field는 최소한 하나 이상의 접근자로 기본 구현을 사용하거나, 커스텀 접근자가 `field` 식별자를 이용해 backing field를 참조할 때 생성됩니다. 예를 들어 아래와 같은 예시에서는 backing field가 없습니다. 

```kotlin
// 예시 1.
val isEmpty: Boolean
    get() = this.size == 0

// 예시 2.
var name: String // get, set
    get() {
        return "User"
    }
```

## Backing Properties
만약 위에서 언급한 backing field의 scheme에 맞지 않는 작업을 하려고 한다면, backing field가 아닌 backing property가 됩니다. 

```kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // 타입 파라미터가 유추됨.
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

> JVM에서는 private 프로퍼티와 디폴트 getter/setter에 대한 접근이 최적화되어있으므로, 이 경우에는 함수 호출 오버헤드가 발생하지 않습니다. 

## 인터페이스에 선언된 프로퍼티 구현하기
코틀린에서는 아래와 같이 인터페이스에 추상 프로퍼티 선언을 넣을 수 있습니다. 

```kotlin
interface User {
    val nickname: String
}
```

이는 User 인터페이스를 구현하는 클래스가 nickname의 값을 얻을 수 있는 방법을 제공해야 한다는 뜻입니다. 인터페이스는 상태를 포함할 수 없으므로 상태를 저장하기 위해서는 하위 클래스에서 상태 저장을 위한 프로퍼티 등을 만들어야 합니다. 아래의 예시를 통해 세 가지 방법으로 인터페이스를 구현해보겠습니다.

```kotlin
// 1번. 주 생성자 안에 프로퍼티를 직접 선언함
class PrivateUser(override val nickname: String) : User

// 2번. 커스텀 게터로 프로퍼티를 설정함.
class SubscribingUser(val email: String) : User {
    override val nickname: String
        get() = email.substringBefore('@')
}

// 3번. 초기화 식으로 프로퍼티 값을 설정함.
class FacebookUser(val accountId: Int) : User {
    override val nickname = getFacebookName(accountId)
}
```

여기서 2번 SubsribingUser와 3번 FacebookUser를 구현하는 방법의 차이에 주의해야합니다. SubscribingUser의 nickname 프로퍼티는 매번 호출될때마다 substringBefore를 호출하여 계산하는 커스텀 게터를 활용합니다. 이와 다르게 FacebookUser의 nickname 프로퍼티는 객체를 초기화할 때 계산한 데이터를 뒷받침하는 필드(backing fields)에 저장했다가 불러오는 방식을 활용합니다. 

인터페이스에는 추상 프로퍼티뿐만 아니라 getter와 setter가 있는 프로퍼티를 선언할 수도 있습니다. 이 떄의 getter와 setter는 backing field를 참조할 수 없습니다. 왜냐하면 backing field가 있다면 인터페이스에 상태를 추가하는 셈인데, 인터페이스는 상태를 저장할 수 없기 때문입니다. 인터페이스에 선언된 프로퍼티와는 달리, 클래스에 구현된 프로퍼티는 backing field를 원하는 대로 사용할 수 있습니다. 

### 참고 문헌
[코틀린 공식문서](https://kotlinlang.org/docs/reference)
[코틀린 인 액션](http://acornpub.co.kr/book/kotlin-in-action)