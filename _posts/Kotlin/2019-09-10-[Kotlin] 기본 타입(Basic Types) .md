---
layout: post
title: "[Kotlin] 컬렉션(Collections)"
date: 2019-09-10
categories: Kotlin
tags: Kotlin
comments: true
---

## 코틀린 컬렉션 개요(Kotlin Collections Overvies)
Kotlin Standard Library는 컬렉션을 관리하기 위한 기능을 제공합니다. 컬렉션은 대부분의 프로그래밍 언어에서 사용되는 일반적인 개념입니다. 예를 들어 Java 또는 Python 컬렉션에 익숙하다면 이 설명을 건너뛰고 다음 섹션을 보셔도 됩니다. 

컬렉션에는 일반적으로 같은 타입(type)의 여러 객체(0개일 수도 있습니다)가 포함됩니다. 컬렉션의 객체를 *elements* 혹은 *items*라고 합니다. 예를 들어보겠습니다. 부서에 속한 모든 학생들은 평균 연령을 계산한 사용될 수 있는 `컬렉션(collection)`을 구성합니다. 코틀린의 컬렉션 유형은 아래와 같습니다. 

- *List*는 순서가 있는 컬렉션으로, 각 요소(element)에 인덱스(index) 접근할 수 있습니다. 인덱스란 해당 요소의 위치를 나타내는 정수(integer)입니다. List에서 element는 중복이 가능하여 두 번 이상 나타날 수 있습니다. List의 예시는 문장입니다. 문장은 단어의 그룹이며, 각 단어의 순서가 중요하고 단어들은 반복될 수 있습니다. 
- *Set*은 고유한 element들의 컬렉션입니다. Set은 반복이 없는 객체 그룹인 집합(set)을 수학적으로 추상화한 것입니다. 예를 들자면, 알파벳은 문자의 집합입니다.
- *Map(혹은 dictionary)*은 키(key)와 값(value)의 쌍(pair)으로 이뤄진 집합입니다. 키는 고유(unique)하며 각 키는 정확히 하나의 값으로 매핑됩니다. 값은 중복될 수 있지만 키는 중복될 수 없습니다. Map은 직원의 ID와 직무간의 연결 같이 객체 간의 논리적 연결을 저장하는 데 유용합니다.

코틀린을 사용하면 컬렉션에 저장된 유형의 객체 타입을 사용하여 독립적으로 컬렉션을 다룰 수 있습니다. In other words, you add a String to a list of Strings the same way as you would do with Ints or a user-defined class. 따라서 코틀린 표준 라이브러리는 모든 타입의 컬렉션을 다루기 위해 알반적인 인터페이스(generic interfaces)와 클래스(classes), 그리고 함수(functions)를 제공합니다. 

컬렉션 인터페이스와 관련 함수는 `kotlin.collections` 패키지에 있습니다. 

## 컬렉션 타입(Collectino types)
Kotlin Standard Library는 sets, lists, maps와 같은 기본 컬렉션 유형에 대한 구현을 제공합니다. 한 쌍의 인터페이스는 각 컬렉션 유형을 나타냅니다:
- 컬렉션 요소에 접근하기 위한 연산(operations)을 제공하는 *읽기 전용(read-only)* 인터페이스입니다. 
- 요소(elements)를 추가(adding), 삭제(removing) 그리고 업데이트(updating)와 같은 쓰기(write) 연산(operations)으로 해당 읽기 전용 인터페이스를 확장한 *변경 가능한(mutable)* 인터페이스

변경 가능한 컬렉션을 변경한다고해서 [var](https://kotlinlang.org/docs/reference/basic-syntax.html#defining-variables)를 사용할 필요는 없습니다. 쓰기 연산(write operations)은 동일한 변경 가능한 컬렉션 객체를 수정하므로 참조는 변경되지 않습니다. `val` 컬렉션을 다시 할당하려고 하면 컴파일 오류가 발생합니다.

```kotlin
val numbers = mutableListOf("one", "two", "three", "four")
numbers.add("five")

// 아래는 컴파일 오류가 발생합니다.
// numbers = mutableListOf("six", "seven")
```

읽기 전용 컬렉션은 공변하는 속성(covariant)을 가지고 있습니다. 예를 들면 `Rectangle` 클래스가 `Shape`에서 상속되는 경우에는 `List<Shape>`이 필요한 모든 곳에서 `List<Rectangle>`을 사용할 수 있습니다. 즉, 컬렉션 타입은 element의 타입과 동일한 하위 유형 관계를 가집니다. Map은 값(value) 타입에 대해서는 공변하지만, 키(key) 타입에 대해서는 공변하지 않습니다.

결과적으로, 변경 가능한 컬렉션(mutable collections)은 공변하지 않습니다; 그렇지 않으면 런타임 오류가 발생합니다. `MutableList<Rectangle>`이 `MutableList<Shape>`의 하위 유형인 경우, 다른 `Shape` 상속자(예. `Circle`)를 삽입하여 `Rectangle` 타입의 인자를 위반(violating)할 수 있습니다.

아래는 코틀린 컬렉션 인터페이스의 다이어그램입니다.

![Kotlin collection interfaces](https://kotlinlang.org/assets/images/reference/collections-overview/collections-diagram.png)

각 인터페이스와 해당 인터페이스의 구현 방법을 살펴보겠습니다. 

## 컬렉션(Collection)
[`Collection<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/index.html)는 컬렉션 계층(collection hierarchy)의 root입니다. 이 인터페이스는 읽기 전용(read-only) 컬렉션의 일반적인 동작-크기 검색(retrieving size), 아이템 멤버십 확인(checking item membership) 등을 나타냅니다. `Collection`은 element의 반복 연산을 정의하는 `Iterable<T>` 인터페이스에서 상속됩니다. `Collection`을 다른 컬렉션 타입에 적용하는 함수의 매개변수(parameter)로 사용할 수도 있습니다. 보다 구체적인 용도로는 `Collection`의 상속자인 [List](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html)와 [Set](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html)을 사용하면 됩니다.

```kotlin
fun printAll(strings: Collection<String>) {
    for(s in strings) print("$s ")
    println()
}

fun main() {
    val stringList = listOf("one", "two", "one")
    printAll(stringList)

    val stringSet = setOf("one", "two", "three")
    printAll(stringSet)
}
```

[MutableCollection](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-collection/index.html)은 `add`와 `remove`같은 쓰기 연산(write operation)을 할 수 있는 `Collection`입니다.

```kotlin
fun List<String>.getShortWordsTo(shortWords: MutableList<String>, maxLength: Int) {
    this.filterTo(shortWords) { it.length <= maxLength}
    val articles = setOf("a", "A", "an", "An", "the", "The")
    shortWords -= articles
}

fun main() {
    val words = "A long time ago in a galaxy far far away".split(" ")
    val shortWords = mutableListOf<String>()
    words.getShortWordsTo(shortWords, 3)
    println(shortWords)
}
```

## List
[`List<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/index.html)는 지정된 순서로 element를 저장하고, 해당 element에 인덱스로 접근할 수 있도록 합니다. 인덱스는 0에서 시작하여 `(list.size-1)`인 `lastIndex`까지 이어집니다.

```kotlin
val numbers = listOf("one", "two", "three", "four")
println("Number of elements: ${numbers.size}")
println("Third element: ${numbers.get(2)}")
println("Fourth element: ${numbers[3]}")
println("Index of element \"two\" ${numbers.indexOf("two")}")
```

리스트의 요소들은 null을 포함하여 복제될 수 있습니다. 리스트는 개수의 제한 없이 동일한 개수의 객체 혹은 단일 객체를 포함할 수 있습니다. 만약 동일한 위치에 동일한 크기의 [구조적으로 동일한(structurally equal)](https://kotlinlang.org/docs/reference/equality.html#structural-equality) 요소가 있다면 두(two) 리스트는 동일하다고 할 수 있습니다.

```kotlin
val bob = Person("Bob", 31)
val people = listOf<Person>(Person("Adam", 20), bob, bob)
val people2 = listOf<Person>(Person("Adam", 20), Person("Bob", 31), bob)
println(people == people2)
bob.age = 32
println(people == people2)
```

[MutableList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/index.html)는 특정 위치에서의 add와 remove 연산과 같이 list-specific 쓰기 연산이 포함된 `List`입니다.

```kotlin
val numbers = mutableListOf(1, 2, 3, 4)
numbers.add(5)
numbers.removeAt(1)
numbers[0] = 0
numbers.shuffle()
println(numbers)
```

어떻게 보면 리스트는 배열(array)과 매우 유사합니다. 그러나 한 가지 중요한 차이점이 있습니다. 배열의 크기는 초기화 시점과 동시에 정의되며 절대 변경되지 않습니다. 하지만 리스트는 사전적으로 정의된 크기를 가지지 않습니다. 따라서 엘리먼트를 추가(add)하고, 업데이트(update)하고, 삭제(remove)하는 등의 쓰기 연산(write operation)을 통해 리스트의 크기를 변경할 수 있습니다. 코틀린에서 `List`의 기본 구현은 크기 조절이 가능한 배열이라고 볼 수 있는 [ArrayList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/index.html)입니다.

## Set
[`Set<T>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-set/index.html)은 순서와 관계없이 고유(unique)한 요소(element)를 저장합니다. `null` 엘리먼트 또한 Set에서는 고유한 요소이기에 Set에는 단 하나의 `null`만 존재할 수 있습니다. 크기가 같은 두개의 Set은 동일하다고 간주되며, 하나의 Set에 존재하는 각 요소는 다른 Set에 동일한 요소가 있습니다. 

```kotlin
val numbers = setOf(1, 2, 3, 4)
println("Number of elments: ${numbers.size}")
if (numbers.contains(1)) println("1 is in the set")

val numbersBackwards = setOf(4, 3, 2, 1)
println("The sets are equal: ${numbers == numberBackwards}")
```

[MutableSet](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/index.html)은 `MutableCollection`의 쓰기 연산(write operation)을 가진 `Set`입니다. 

`Set`의 기본 구현은 [LinkedHashSet](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-set/index.html)입니다. LinkedHashSet은 요소들의 삽입 순서를 저장합니다. 그러므로, `first()`와 `last()`같은 순서가 필요한 함수를 이용하여 결과를 반환합니다.

```kotlin
val numbers = setOf(1, 2, 3, 4)
val numbersBackwards = setOf(4, 3, 2, 1)

println(numbers.first() == numbersBackwards.first())
println(numbers.first() == numbersBackwards.last())
```

또 다른 구현 방식인 [HashSet](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-set/index.html)은 요소의 순서에 대해 아무것도 알려주지 않으므로, first/last와 같은 함수를 호출하면 예기치 않은 결과가 리턴되빈다. 반면, `HashSet`은 동일한 수의 요소를 저장할 때 더 적은 메모리를 필요로합니다.

## Map
[`Map<K, V>`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/index.html)은 `Collection` 인터페이스를 상속받지는 않지만 코틀린에서느 컬렉션 타입입니다. `Map`은 *키-값(key-value)*의 쌍(혹은 entries)을 저장합니다. 키는 유일하지만 다른 키를 동일한 값으로 짝지을 수 있습니다. `Map` 인터페이스는 키별로 값에 접근하거나 키와 값을 검색하는 등의 기능을 제공합니다.

```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)

println("All keys: ${numbersMap.keys}")
println("All values: ${numbersMap.values}")
if ("key2" in numbersMap) println("Value by key \"key2\": ${numbersMap["key2"]}")    

// 아래 두 코드는 동일한 결과를 냅니다.
if (1 in numbersMap.values) println("The value 1 is in the map")
if (numbersMap.containsValue(1)) println("The value 1 is in the map") 
```

동일한 쌍을 포함하는 두 개의 `Map`은 순서와 상관없이 동일하다고 간주됩니다.

```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)    
val anotherMap = mapOf("key2" to 2, "key1" to 1, "key4" to 1, "key3" to 3)

println("The maps are equal: ${numbersMap == anotherMap}")
```

[MutableMap](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/index.html)은 `Map`의 쓰기 연산(write operations)이 포함된 맵입니다. 에를 들어 새로운 키-값 쌍(key-value pair)을 추가하거나 지정된 키와 연관된 값을 추가할 수 있습니다.

```kotlin
val numbersMap = mutableMapOf("one" to 1, "two" to 2)
numbersMap.put("three", 3)
numbersMap["one"] = 11

println(numbersMap)
```

`Map`의 기본 구현인 [LinkedHashMap](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-linked-hash-map/index.html)은 맵을 반복할 때 요소들의 삽입 순서를 저장합니다. 또 다른 구현 방식인 [HashMap](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-map/index.html)은 요소들의 순서에 대해 아무것도 알려주지 않습니다.