---
layout: post
title: "[Kotlin] 코틀린의 Scope 함수"
date: 2019-12-05
categories: Kotlin
tags: Kotlin
comments: true
---

# 코틀린의 Scope 함수
코틀린 표준 라이브러리는 객체의 컨텍스트 내에서 코드 블럭을 실행하기 위한 목적만을 가진 여러가지 함수를 제공합니다. 이런 함수들을 람다식으로 호출할 때, 이는 임시로 범위(scope)를 형성합니다. 이 범위 내에서는 객체의 이름이 없어도 객체에 접근할 수 있습니다. 이러한 함수를 *scope functions*라고 합니다. 이러한 scope 함수에는 `let`, `run`, `with`, `apply`, `also`의 5가지가 있습니다. 

기본적으로, 이 함수들은 동일한 역할을 합니다: 객체에 코드 블럭을 실행하는 것입니다. 다른점은, 이 객체를 어떤 방식으로 블럭 안에서 사용할 수 있는지, 그리고 전체 표현식(expression)의 결과가 어떻게 되는지입니다.

아래는 전형적인 scope function의 사용법입니다:
```kotlin
Person("Alice", 20, "Amsterdam").let {
    println(it)
    it.moveTo("London")
    it.incrementAge()
    println(it)
}
```

만약 `let` 없이 동일한 내용의 코드를 작성한다면, 아래의 코드처럼 새로운 변수를 만들고 그 변수를 사용할때마다 변수의 이름을 반복해서 작성해야할 것입니다. 
```kotlin
val alice = Person("Alice", 20, "Amsterdam")
println(alice)
alice.moveTo("London")
alice.incrementAge()
println(alice)
```

scope 함수는 어떠한 새로운 기술적인 능력을 가져다주는 것이 아닌, 코드를 좀 더 간결하고 읽기 쉽게 만들어주는 것입니다.

scope 함수의 비슷한 점들로 인해, 특정 상황에 딱 맞는 함수를 사용하는 것이 모호할 수 있습니다. 이 때 결정하는 기준은, 작성하고자 하는 코드의 의도와 프로젝트 내에서의 일관성입니다. 아래에서 scope 함수와 각각의 사용 관례의 차이점에 대해 상세히 살펴보겠습니다.

## 차이점
scope 함수는 본질이 비슷하기때문에, 각각의 차이점을 잘 아는 것이 중요합니다. 각 scope 함수에는 두 가지 큰 차이점이 있습니다.
- 객체의 컨텍스트를 참조하는 방식
- 리턴값

### 컨텍스트 객체(Context object): this 혹은 it
scope 함수의 람다식 내에서 컨텍스트 객체는 실제 이름 대신 짧은 참조 이름으로도 사용할 수 있습니다. 각각의 scope 함수는 컨텍스트 객체에 접근하기 위해 다음 두 가지 방법 중 하나를 사용합니다: 람다 수신자(`this`)를 사용하거나 람다 인자(`it`)를 사용합니다. 둘 다 동일한 역할을 하는데, 다른 상황 별로 각각의 장점과 단점에 대해 알아보고 권장하는 방식을 알아보겠습니다.
```kotlin
fun main() {
    val str = "Hello"
    // this
    str.run {
        println("The receiver string length: $length")
        // 아래 코드도 동일한 역할을 합니다
        // println("The receiver string length: ${this.length}")
    }

    // it
    str.let {
        println("The receiver string's length is ${it.length}")
    }
}
```

#### this
`run`, `with`, `apply`는 `this` 키워드로 람다 수신자로서의 컨텍스트 객체를 참조합니다. 그러므로, 이 람다에서는 객체는 일반 객체 함수로도 사용이 가능합니다. 대부분의 경우, 수신 객체의 멤버에 접근할 때 `this`를 생략함으로써 코드를 간략하게 만들 수 있습니다. 반면, 만약 `this`가 생략되면, 수신 객체 멤버와 외부의 객체 혹은 함수를 구분하기가 어려울 수도 있습니다. 따라서 수신자(`this`)로 컨텍스트 객체를 받는 것은 주로 객체의 함수를 호출하거나 프로퍼티를 할당하는 것과 같이 객체 멤버를 처리하는 람다에 사용하는 것을 권장합니다. 
```kotlin
val adam = Person("Adam").apply {
    age = 20        // 이는 this.age = 20 이나 adam.age = 20 과 동일합니다.
    city = "London"
}
```

#### it
반면, `let`과 `also`는 컨텍스트 객체를 람다 인자(argument)로 가집니다. 만약 인자의 이름이 정해지지 않았다면, 해당 객체는 암시적인 기본 이름인 `it`으로 접근할 수 있습니다. `it`은 `this`보다 짧으며, 주로 `it`을 사용한 표현식은 읽기 쉽습니다. 하지만, 객체 함수나 프로퍼티를 호출할 때  `this`와 같이 암시적인 객체가 없습니다. 그러므로, 객체가 함수 호출에서 주로 인자로 사용될 때에는 컨텍스트 객체를 `it`으로 가지는 것이 더 낫습니다. 또한 코드 블럭 내에서 여러개의 변수를 사용한다면 `it`을 사용하는 것이 더 좋습니다.
```kotlin
fun getRandomInt(): Int {
    return Random.nextInt(100).also {
        writeToLog("getRandomInt() generated value $it")
    }
}

val i = getRandomInt()
``` 

또한, 컨텍스트 객체를 인자로 전달할때에는, scope 내에서 컨텍스트 객체의 커스텀 이름을 설정할 수 있습니다. 
```kotlin
fun getRandomInt(): Int {
    return Random.nextInt(100).also { value ->
        writeToLog("getRandomInt() generated value $value")
    }
}

val i = getRandomInt()
```

### 리턴 값(return value)

이 scope 함수들은 리턴값이 다릅니다:
- `apply`와 `also`는 컨텍스트 객체(context object)를 리턴합니다.
- `let`, `run`, `with`는 람다식의 결과를 리턴합니다.

이 두 옵션은 다음에 어떤 코드를 작성할 것인지에 따라 적절한 함수를 선택할 수 있도록 합니다.

#### 컨텍스트 객체(Context object)
`apply`와 `also`의 리턴값은 컨텍스트 객체 그 자신입니다. 그러므로, `apply`와 `also`는 *side steps*으로 호출 체인에 포함될 수 있습니다. 즉, 동일한 객체에 대한 함수 호출을 계속 연결하여 작성할 수 있습니다. 
```kotlin
val numberList = mutableListOf<Double>()
numberList.also { println("Populating the list") }
    .apply{
        add(2.71)
        add(3.14)
        add(1.0)
    }
    .also { println("Sorting the list") }
    .sort()
```

또한, `apply`와 `also`는 컨텍스트 객체를 반환하는 함수의 반환식에서도 사용될 수 있습니다.
```kotlin
fun getRandomInt(): Int {
    return Random.nextInt(100).also {
        writeToLog("getRandomInt() generated value $it")
    }
}

val i = getRandomInt()
```

#### 람다 결과(Lambda result)
`let`, `run`, `with`는 람다 결과를 반환합니다. 따라서 이 scope 함수들은 변수에 연산 결과를 할당할 때나, 연산 결과에 연산을 연결할 때 등의 상황에서 사용할 수 있습니다. 
```kotlin
val numbers = mutableListOf("one", "two", "three")
val countEndsWithE = numbers.run {
    add("four")
    add("five")
    count { it.endsWith("e") }
}
println("There are $countEndsWithE elements that end with e.")
```

또한, 변수를 위한 임시 scope을 만들기 위해 리턴값을 무시하고 scope 함수를 사용할 수도 있습니다.
```kotlin
val numbers = mutableListOf("one", "two", "three")
with(numbers) {
    val firstItem = first()
    val lastItem = last()
    println("First item: $firstItem, last item: $lastItem")
}
```

## 함수(Functions)
상황에 맞는 scope 함수를 선택할 수 있도록 권장되는 방식의 사용 예시와 함께 조금 더 자세히 설명해드리겠습니다. 엄밀히 말하면 많은 상황에서 함수는 교체될 수 있습니다. 따라서 아래의 예시에서는 일반적인 사용 방식을 정의하였습니다.

### let
**컨텍스트 객체(context object)는** 인자(`it`)로서 사용 가능합니다. 이 컨텍스트 객체의 **리턴값(return value)은** 람다 결과입니다. 

`let`은 호출 체인의 결과로 하나 혹은 그 이상의 함수를 invoke할 때 사용됩니다. 예를 들어, 다음의 코드는 컬렉션의 두 연산의 결과를 출력합니다.
```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
val resultList = numbers.map { it.length }.filter { it > 3 }
println(resultList)
```

`let`으로 아래와 같이 다시 작성해볼 수 있습니다.
```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
numbers.map { it.length }.filter { it > 3 }.let {
    println(it)
    // 필요하다면 더 많은 함수를 호출할 수도 있습니다.
}
```

만약 코드 블럭에 `it`을 인자로 가지는 단일 함수가 있다면 람다 대신 메서드 참조(`::`)를 사용할 수도 있습니다. 
```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
numbers.map { it.length }.filter { it > 3 }.let(::println)
```

`let`은 종종 널이 아닌 값만을 가지는 코드 블럭을 실행시키는 데에 사용됩니다. 널이 아닌 객체에 동작을 수행하기 위해서는, 해당 객체에 안전한 호출 연산자(safe call operator)인 `?.`을 사용하고 람다의 action으로 `let`을 호출하면 됩니다.
```kotlin
val str: String? = "Hello"
// processNonNullString(str)        // 컴파일 에러: star can be null
val length = str?.let {
    println("let() called on $it")
    processNonNullString(it)        // OK: '?.let { }' 안에서는 'it'이 null이 아님
    it.length
}
```

`let`을 사용하는 또 다른 방법은 코드 가독성을 높이기 위해 제한된 scope 내에서의 지역 변수를 사용하는 것입니다. 컨텍스트 객체를 위해 새로운 변수를 정의하기 위해서는, 기본적으로 사용되는 `it` 대신에 람다의 인자로 변수 이름을 넣어 사용하면 됩니다.
```kotlin
val numbers = listOf("one", "two", "three", "four")
val modifiedFirstItem = numbers.first().let { firstItem ->
    println("The first item of the list is '$firstItem'")
    if (firstItem.length >= 5) firstItem else "!" + firstItem + "!"
}.toUpperCase()
println("First item after modifications: '$modifiedFirstItem'")
```

### with
비 확장함수(non-extension function): **컨텍스트 객체는** 인자로 전달되지만, 람다 안에서는 수신자(`this`)로 사용 가능합니다. **리턴값은** 람다 결과입니다.

`with`는 람다 결과 없이 컨텍스트 객체의 호출 함수에서 사용하는 것을 권장합니다. 아래 코드에서 `with`는 *"이 객체로, 다음을 수행하라(with this object, do the following.)."*로 읽힐 수 있습니다.
```kotlin
val numbers = mutableListOf("one", "two", "three")
with(numbers) {
    println("'with' is called with argument $this")
    println("It contains $size elements")
}
```

`with`의 또 다른 사용법은 값을 계산하는 데에 사용되는 헬퍼 객체(helper object)의 프로퍼티나 함수를 선언하는 데에 사용하는 것입니다.
```kotlin
val numbers = mutableListOf("oen", "two", "three")
val firstAndLast = with(numbers) {
    "The first element is ${first()}," +
    " the last element is ${last()}"
}
println(firstAndLast)
```

### run
**컨텍스트 객체는** 수신자(`this`)로 사용가능합니다. **리턴값은** 람다 결과입니다. 

`run`은 `with`와 같은 역할을 하지만, `let`처럼-컨텍스트 객체의 확장 함수처럼- 호출(invoke)됩니다.

`run`은 람다가 객체 초기화와 리턴 값 연산을 모두 포함하고 있을 때 유용합니다.

```kotlin
val service = MultiportService("https://example.kotlinlang.org", 80)

val result = service.run {
    port = 8080
    query(prepareRequest() + " to port $port")
}

// let() 함수로 쓰인 위와 동일한 코드:
val letResult = service.let {
    it.port = 8080
    it.query(it.prepareRequest() + " to port ${it.port}")
}
```

`run`은 수신 객체에서 호출하는 것 이외에, 비확장함수(non-extension function)로도 사용할 수 있습니다. 비확장(non-extension) `run`은 표현식이 필요한 곳에서 다수의 구문 블럭을 실행할 수 있도록 합니다. 
```kotlin
val hexNumberRegex = run {
    val digits = "0-9"
    val hexDigits = "A-Fa-f"
    val sign = "+-"

    Regex("[$sign]?[$digits$hexDigits]+")
}

for (match in hexNumberRegex.findALl("+1234 -FFFF not-a-number)) {
    println(match.value)
}
```

### apply
**컨텍스트 객체는** 수신자(`this`)로 사용가능합니다. **리턴값은** 객체 자기 자신입니다.

`apply`는 값을 반환하지 않고, 주로 수신 객체의 멤버를 연산하는 곳에 사용하면 됩니다. `apply`를 사용하는 가장 일반적인 경우는 객체 생성?(configuration)입니다. 이러한 호출은 *"다음의 지시를 객체에 적용하라(apply the following assignments to the object)."* 로 읽힐 수 있습니다. 
```kotlin
val adam = Person("Adam").apply {
    age = 32
    city = "London"
}    
```
수신자를 리턴값으로 가짐으로써, `apply`를 더 복잡한 프로세스를 위한 호출 체인에 쉽게 포함시킬 수 있습니다.

### also
**컨텍스트 객체는** 인자(`it`)로 사용가능합니다. **리턴값은** 객체 자기 자신입니다.

`also`는 컨텍스트 객체를 인자로 가지는 것과 같은 작업을 수행하는 데에 좋습니다. `also`는 로깅이나 디버그 정보를 출력하는 것과 같이 객체를 변화시키지 않는 부가적인 작업에 적합합니다. 대부분 프로그램의 로직을 파괴하지 않고도 호출 체인에서 `also`의 호출을 제거하는 것이 가능합니다.

`also`를 코드에서 보면, *"그리고 또한 다음을 수행하라(and also do the following)"*의 의미를 읽을 수 있습니다.
```kotlin
val numbers = mutableListOf("one", "two", "three")
numbers
    .also { println("The list elements before adding new one: $it") }
    .add("four")
```

## 함수 선택(Function selection)
아래의 표는 목적에 맞게 scope 함수를 선택하는 데에 도움이 될 수 있게 함수별로 주요 차이점을 정리한 표입니다.

| Function | Object reference | Return value   | Is extension function                       |
| -------- | ---------------- | -------------- | ------------------------------------------- |
| let      | it               | Lambda result  | Yes                                         |
| run      | this             | Lambda result  | Yes                                         |
| run      | -                | Lambda result  | No: called without the context object       |
| with     | this             | Lambda result  | No: takes the context object as an argument |
| apply    | this             | Context object | Yes                                         |
| also     | it               | Context object | Yes                                         |

아래는 의도한 목적에 따라 scope 함수를 선택할 수 있는 간략한 가이드입니다.
- 널이 아닌 객체에 람다를 실행할 때: `let`
- 지역(local) scope에서 표현식(expression)을 변수로 선언할 때: `let`
- 객체 선언?(configuration): `apply`
- 객체 선언?(configuration)과 결과를 계산할 때: `run`
- 표현식(expression)이 필요한 곳에 문(statements)을 실행할 때: 비 확장(non-extension) `run`
- 부가적인 실행: `also`
- 객체에 대한 그룹 함수 호출: `with`

각기 다른 함수의 사용 용도가 중복되기때문에, 프로젝트나 팀에서의 구체적인 컨벤션에 따라 어떤 함수를 사용할지 정하시면 됩니다.

scope 함수는 코드를 더 간결하게 만드는 방법이지만, 남용하는 것은 피해야합니다: 코드 가독성을 떨어트리고 에러를 발생시킬 수 있기때문입니다. scope 함수를 중첩하여 사용하는 것은 피해야하며, 연속(chaining)하여 사용할 때에는 주의하세요: `this` 혹은 `it`의 현재의 컨텍스트 객체와 값을 혼동하기 쉽습니다.

## takeIf와 takeUnless
scope 함수와 더불어, 표준 라이브러리에는 `takeIf`와 `takeUnless`가 있습니다. 이 함수들은 호출 체인(call chains)의 객체 상태 확인을 포함시킬 수 있습니다. 

predicate가 제공된 객체에서 `takeIf`가 호출될 때, predicate와 객체가 일치하면 `takeIf`는 predicate를 리턴하고, 그렇지 않으면 `null`을 리턴합니다. 따라서, `takeIf`는 단일 객체를 위한 필터링 함수입니다. 결과적으로, `takeUnless`는 객체가 predicate와 일치하면 객체를 리턴하고, 일치하지않으면 `null`을 리턴합니다. 이 객체는 람다 인자(`it`)로 사용가능합니다.
```kotlin
val number = Random.nextInt(100)

val evenOrNull = number.takeIf { it % 2 == 0 }
val oddOrNull = number.takeUnless { it % 2 == 0 }
println("even: $evenOrNull, odd: $oddOrNull")
```

`takeIf`와 `takeUnless` 다음에 다른 함수를 연결하는 경우에는 널체크 혹은 세이프 호출(`?.`)을 하는 것을 잊지 마세요. 이 함수들의 리턴 값은 nullable이기 때문입니다.
```kotlin
val str = "Hello"
val caps = str.takeIf { it.isNotEmpty() }?.toUpperCase()
// val caps = str.takeIf { it.isNotEmpty() }.toUpperCase() // 컴파일 에러
println(caps)
```

`takeIf`와 `takeUnless`는 scope 함수와 같이 쓸 때 특히 유용합니다. 좋은 사용 방식은, 주어진 predicate와 일치하는 객체에서 코드 블럭을 실행할 수 있도록 `takeIf`와 `takeUnless`를 `let`과 연결하는 것입니다. 이렇게 하기 위해서는, `takeIf`를 객체에 호출한 뒤, `let`을 세이프 호출(`?`)로 호출하는 것입니다. `takeIf`는 predicate와 매치되지 않는 객체에 대해서는 `null`을 리턴하며 `let`은 호출되지 않습니다.
```kotlin
fun displaySubstringPosition(input: String, sub: String) {
    input.indexOf(sub).takeIf { it >= 0 }?.let {
        println("The substring $sub is found in $input.")
        println("Its start position is $it.")
    }
}

displaySubstringPosition("010000011", "11")
displaySubstringPosition("010000011", "12")
```

아래는 표준 라이브러리 함수 없이 동일한 함수가 어떻게 작성되는지 보여줍니다.
```kotlin
fun displaySubstringPosition(input: String, sub: String) {
    val index = input.indexOf(sub)
    if (index >= 0) {
        println("The substring $sub is found in $input.")
        println("Its start position is $index.")
    }
}

displaySubstringPosition("010000011", "11")
displaySubstringPosition("010000011", "12")
```