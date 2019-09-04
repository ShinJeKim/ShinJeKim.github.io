---
layout: post
title: "[Kotlin] Object 키워드, 식(Expressions), 선언(Declarations)"
date: 2019-09-04
categories: Kotlin
tags: Kotlin
comments: true
---

## Object 키워드
`object` 키워드를 선언하는 상황은 아래와 같습니다.
- 객체 선언(object declaration): 싱글턴을 정의하는 방법 중 하나
- 동반 객체(companion object): 인스턴스 메소드는 아니지만 어떤 클래스와 관련 있는 메소드, 그리고 팩토리 메소드를 담을 떄 쓰임.  동반 객체 메소드에 접근할 때는 동반 객체가 포함된 클래스의 이름을 사용할 수 있음.
- 객체 식은 자바의 무명 내부 클래스(anonymous inner class) 대신 쓰임.

때로는 하위 클래스를 새로 선언하지 않고 일부 클래스를 약간 수정한 객체를 만들어야 할 때가 있습니다. 코틀린은 이러한 상황을 객체 표현식 및 객체 선언으로 처리합니다.

## Object Expressions(Object 표현식)
특정 타입으로 상속되는 익명 클래스의 객체를 생성하기 위해서는 아래와 같은 코드를 작성하게됩니다.

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```

만약 supertype에 생성자가 있다면 적절한 생성자 파라미터가 전달됩니다. supertype이 많다면 콜론(:) 뒤에 콤마(,)로 구분되어 표현됩니다.

```kotlin
open class A(x: Int){
    public open val y: Int = x
}

interface B { /*...*/}

val ab: A = object : A(1). B {
    override val y = 15
}
```

만약 supertype이 없는 그냥 객체 자체를 원한다면 아래와 같이 표현할 수 있습니다. 

```kotlin
fun foo() {
    val adHoc = object{
        var x: Int = 0
        var y: Int = 0
    }
    print(adHoc.x + adHoc.y)
}
```

익명 객체는 지역(local) 혹은 프라이빗(private) 일때만 타입으로 사용될 수 있습니다. 만약 익명 객체를 public 함수의 리턴 타입으로 사용하거나 public 프로퍼티의 타입으로 사용한다면, 해당 함수/프로퍼티의 실제 타입은 익명 객체의 수퍼타입으로 선언된 타입이며 수퍼타입을 선언하지 않았을 경우에는 `Any`입니다. 익명 객체에 추가된 멤버는 접근할 수 없습니다. 

```kotlin
class C {
    // private 함수: 리턴 타입은 익명 객체의 타입입니다.
    private fun foo() = object {
        val x: String = "x"
    }

    // public 함수: 리턴 타입은 Any입니다.
    fun publicFoo() = object {
        val x: String = "x"
    }

    fun bar() {
        val x1 = foo().x // 제대로 동작함.
        val x2 = publicFoo().x // 에러: Unresolved reference 'x'.
    }
}
```

객체 표현식의 코드는 해당 영역 안(enclosing scope)의 변수에 접근할 수 있습니다. 

```kotlin
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ...
}
```

## Object declarations(객체 선언)
코틀린에서는 싱글턴(singleton)을 쉽게 선언할 수 있도록 지원해줍니다. 

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

위의 예시와 같은 코드를 객체 선언(object declarations)이라고 하며, 항상 `object` 키워드 뒤에 이름이 있습니다. 변수 선언과 마찬가지로 객체 선언은 표현식이 아니며 대입문(assignment statement)의 우변(right side)에 쓰일 수 없습니다. 

객체 선언의 초기화는 스레드에 안전합니다. 객체를 참조하려면 해당 객체의 이름을 바로 사용하면 됩니다. 

```kotlin
DataProviderManager.registerDataProvider(...)
```

이러한 객체는 supertype을 가질 수 있습니다. 

```kotlin
object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { ... }
    override fun mouseEntered(e: MouseEvent) { ... }
}
```

**주의**: 객체 선언은 지역적으로 수행될 수 없으며(함수 내에 중첩될 수는 있습니다), 다른 객체 선언이나 내부 클래스가 아닌 곳에 중첩될 수 있습니다. 

## Companion Objects(동반 객체)
클래스 내부의 객체 선언은 아래와 같이 `companion` 키워드로 표현할 수 있습니다.

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

아래와 같이 클래스 이름을 qualifier로 사용하여 간단하게 동반 객체의 멤버를 호출할 수 있습니다. 

```kotlin
val instance = MyClass.create()
```

동반 객체의 이름은 생략될 수 있으며 이 때에는 `Companion`이라는 명칭이 대신 사용됩니다. 

```kotlin
class MyClass {
    companion object { }
}

val x = MyClass.Companion
```

다른 이름의 qualifier가 아닌, 해당 클래스 자체적으로 사용되는 클래스 이름은 이름 지정 여부에 관계없이 동반 객체가 참조하게 됩니다.

```kotlin
class MyClass1 {
    companion object Named { }
}

val x = MyClass1

class MyClass2 {
    companion object { }
}

val y = MyClass2
```

동반 객체의 멤버가 다른 언어에서는 static 멤버처럼 보이지만, 런타임시에는 실제 객체의 인스턴스 멤버이며 인터페이스를 구현하는 등의 역할을 할 수 있습니다. 

```kotlin
interface Factory<T> {
    fun create(): T
}

class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass()
    }
}

val f: Factory<MyClass> = MyClass
```

하지만 JVM에서는 @JvmStatic 어노테이션을 사용한다면 실제 static 메소드와 필드로 생성된 동반 객체의 멤버를 가질 수 있습니다. 이는 [Java interoperability](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#static-fields)에 자세하게 나와있습니다. 

### 객체 표현식과 객체 선언의 차이점(Semantic difference between object expressions and declarations)
> 객체 표현식과 객체 선언 사이에는 중요한 차이가 있습니다.
- 객체 표현식은 사용되는 곳에서 **즉시** 실행되고 초기화됩니다.
- 처음으로 접근할 때에는 객체 선언이 **느리게** 초기화됩니다.
- 동반 객체는 해당 클래스가 로드될 때 초기화되어 자바의 static 초기화 프로그램의 의미와 일치합니다. 

### 참고 문헌
- [코틀린 공식문서](https://kotlinlang.org/docs/reference)
- [코틀린 인 액션](http://acornpub.co.kr/book/kotlin-in-action)
