---
layout: post
title: "[Kotlin] 함수(Functions)"
date: 2019-09-06
categories: Kotlin
tags: Kotlin
comments: true
---

## 함수 선언(Functions declarations)
코틀린에서의 함수 선언은 `fun` 키워드를 사용합니다.
```kotlin
fun double(x: Int): Int {
    retunr 2 * x
}
```

## 함수의 사용(Functions usage)
함수 호출은 기존의 방식과 동일합니다:
```kotlin
val result = double(2)
```

멤버 함수를 호출할때에는 dot(`.`) 표기를 사용합니다.
```kotlin
Stream().read)() // Stream 클래스의 인스턴스를 생성하고 read() 함수를 호출함
```

### 매개변수(Parameters)
함수의 매개변수는 파스칼 표기법(name: type)으로 표기합니다. 여러개의 매개변수는 comma(`,`)로 구분하며, 각각의 타입을 명시적으로 표기해주어야합니다.
```kotlin
fun powerOf(number: Int, exponent: Int) { /*...*/ }
```

### 디폴트 인자(Default arguments)
함수의 매개변수(parameters)는 기본값(default values)을 가질 수 있으며 함수의 인자(arguments)가 생략되었을 때 사용됩니다. 이로인해 코틀린에서는 다른 언어보다 적은 코드로 오버로딩을 표현할 수 있습니다.
```kotlin
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size) { /*...*/ }
```
기본값은 위와 같이 매개변수의 타입 뒤에 `=` 연산자를 사용하여 지정할 수 있습니다.

> 매개변수(parameter)와 인자(argument)는 비슷해보이지만 다른 개념입니다. 매개변수는 함수의 정의 부분에서 나열되는 변수를 의미합니다. 인자는 함수를 호출할 때 전달되는 실제 값을 의미합니다.

오버라이딩 되는 메소드는 기존의 메소드와 동일한 기본 매개변수 값(default parameter values)을 갖습니다. 디폴트 파라미터 값을 가지는 메소드를 오버라이딩 할 때에는 아래와 같이 디폴트 파라미터 값의 자리에 아무것도 지정할 수 없습니다.
```kotlin
open class A {
    open fun foo(i: Int = 10) { /*...*/ }
}

class B : A() {
    override fun foo(i: Int) { /*...*/ } // 여기서 디폴트 값을 지정할 수 없습니다.
}
```

디폴트 값이 있는 매개변수가 디폴트 값이 없는 매개 변수보다 앞에 오는 경우에는, 아래와 같이 [지명 인자(named arguments)](#지명-인자(Named-arguments))로 함수를 호출해야만 디폴트 값을 사용할 수 있습니다.
```kotlin
fun foo(bar: Int = 0, baz: Int) { /*...*/ }

foo(baz = 1) // 기본값인 bar = 0 이 사용됨
```

만약 디폴트 값이 있는 매개변수 뒤에 오는 마지막 인자가 람다라면 지명 인자를 사용하여 전달될 수도 있고 [소괄호 밖에서 전달](https://kotlinlang.org/docs/reference/lambdas.html#passing-a-lambda-to-the-last-parameter)될 수도 있습니다. 
```kotlin
fun foo(bar: Int = 0, baz: Int = 1, qux: () -> Unit) { /*...*/ }

// 기본값 baz = 1 이 사용됨
foo(1) { println("hello") } 

// 기본값 bar = 0 과 baz = 1 이 모두 사용됨
foo(qux = { println("hello") }) 

// 기본값 bar = 0 과 baz = 1 이 모두 사용됨
foo { println("hello") }
```

### 지명 인자(Named arguments)
함수를 호출할 때 매개변수의 이름을 지정할 수도 있습니다. 이는 함수에 매개변수가 많거나 기본값이 있는 매개변수가 있을 때 사용하기 편리합니다.

아래와 같은 함수가 있다고 가정해봅시다.

```kotlin
fun reformat(str: String,
            normalizeCase: Boolean = true,
            upperCaseFirstLetter: Boolean = true,
            divideByCamelHumps: Boolean = false,
            wordSeparator: Char = ' ') {
    / *...*/
}
```

이 때 아래와 같이 디폴트 인자(default arguments = default parameter values)를 사용하여 함수를 호출할 수 있습니다.

```kotlin
reformat(str)
```

하지만 디폴트 인자가 없는 매개변수를 호출할떄에는 아래와 같이 호출해야합니다.

```kotlin
reformat(str, true, true, false, '_')
```

지명 인자(named arguments)를 사용하면 훨씬 가독성있게 코드를 작성할 수 있습니다:

```kotlin
reformat(str,
        normalizeCase = true,
        upperCaseFirstLetter = true,
        divideByCamelHumps = false,
        wordSeparator = '_'
)
```

위와 같이 지명 인자를 사용하면 아래의 코드처럼 모든 인자를 지정할 필요 없이 함수를 호출할 수 있습니다.

```kotlin
reforamt(str, wordSeparator = '_')
```

순서대로 전달되는 인자(positional arguments)와 지명 인자(named arguments)를 둘 다 사용하여 함수를 호출할 떄, 모든 positional arguments는 가장 첫번째 지명 인자보다 앞에 위치해야만 합니다. 예를 들어 `f(1, y =2)`는 가능하지만, `f(x = 1, 2)`는 사용할 수 없습니다. 

가변 길이 인자를 사용하는 함수-호출 시 인자 개수가 달리질 수 있는 함수-(variable number of arguments: vararg)는 아래와 같이 `spread(*)` 연산자를 사용하여 전달합니다.
```kotlin
fun foo(vararg strings: String) { /*...*/ }

foo(strings = *arrayOf("a", "b", "c"))
```

> On the JVM: 지명 인자 문법은 자바 함수를 호출할 떄에는 사용할 수 없습니다. 자바의 바이트코드가 항상 매개변수의 이름을 보관하는 것은 아니기 때문입니다.

### Unit을 반환하는 함수(Unit-returning functions)
리턴값이 없는 함수의 리턴 타입은 `Unit`입니다. `Unit`은 오직 하나의 값을 가지는 함수이며, 그 값 또한 `Unit`입니다. 이 값은 명시적으로 작성할 필요는 없습니다.

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello ${name}")
    else
        println("Hi there!")
    // `return Unit` 혹은 `return`은 생략 가능합니다.
}
```

`Unit` 리턴 타입 역시 생략 가능합니다. 리턴 타입인 `Unit`을 생략하여 위의 예제 코드를 아래와 같이 표현할 수도 있습니다.
```kotlin
fun printHello(name: String?) { ... }
```

### 단일 표현 함수(Single-expression functions)
함수가 반환하는 식이 한 줄이라면, 중괄호를 생략하고 `=` 기호를 사용하여 표현할 수 있습니다.
```kotlin
fun double(x: Int): Int = x * 2
```

만약 컴파일러가 함수의 리턴 타입을 유추할 수 있다면 아래와 같이 리턴 타입을 생략해도 됩니다.
```kotlin
fnu double(x: Int) = x * 2
```

### 명시적 리턴 타입(Explicit return types)
블록 본문이 있는 함수는 `Unit`을 리턴(이 경우에는 생략 가능)하도록 의도되지 않은 한 리턴 타입을 명시적으로 지정해야합니다. 코틀린은 블록 본문이 있는 함수를 유추하지 않습니다. 블록 본문이 있는 함수는 복잡한 제어 흐름이 있을 수 있으며 이러한 함수의 리턴 타입은 때로는 컴파일러도 알기 어렵기 때문입니다.

### 가변 길이 인자(Variable number of arguments: varargs)
함수의 매개변수(일반적으로 가장 마지막 매개변수)는 `vararg` 변경자를 사용하여 표기할 수 있습니다:
```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts는 Array
        result.add(t)
    return result
}
```

위와 같이 `vararg` 변경자를 사용하면 가변 길이의 인자를 아래와 같이 전달할 수 있습니다.

```kotlin
val list = asList(1, 2, 3)
```

함수 내에서 `T` 타입의 `vararg`-parameter는 `T`의 배열로 나타납니다. 즉, 위의 예제코드의 `ts` 변수는 `Array<out T>`라는 타입을 가집니다.

오직 하나의 매개변수에만 `vararg` 변경자를 지정할 수 있습니다. `vararg` 매개변수가 마지막 매개변수가 아닌 경우에는 이름이 있는 인자 구문(named argument syntax)을 사용하여 전달할 수도 있고, 만약 함수 타입의 매개변수가 있다면 소괄호 밖에서 람다를 이용하여 전달할수도 있습니다.

`vararg`-function을 호출하면 인자를 하나씩 전달할 수 있습니다. 예를 들면 `asList(1, 2, 3)`과같은 배열이 있고, 이 내용을 함수에 전달하려면 `spread(*)` 연산자를 배열 앞에 붙입니다.

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

### 중위 함수 호출 구문(infix notation)
`infix` 키워드가 붙은 함수는 중위 함수 호출(infix notation)을 이용하여 호출할 수 있습니다. 중위 함수 호출이란 `dot(.)`과 소괄호를 생략하여 함수를 호출하는 것을 의미합니다. 중위 함수 호출을 하기 위해서는 아래의 사항을 지켜야합니다.
- 해당 함수가 멤버 함수이거나 확장 함수이어야만 합니다
- 매개변수가 하나(single)이어야만 합니다.
- 매개변수가 가변 인자를 받지 않으며 기본 값이 없어야 합니다.

```kotlin
infix fun Int.shl(x: Int): Int { ... }

// 중위 함수 호출(infix notation)을 이용.
1 shl 2

// 위의 코드는 아래의 코드와 같은 의미입니다.
1.shl(2)
```

> 중위 함수 호출은 산술 연산자(arithmetic operator), 타입 캐스트(type cast) 그리고 `rangeTo` 연산자(rangeTo operator)보다 우선순위가 낮습니다. 아래의 표현식들은 각각 같은 의미입니다.
> - `1 shl 2 + 3` 와 `1 shl (2 + 3)`
> - `0 until n * 2` 와 `0 until (n * 2)`
> - `xs union ys as Set<*>` 와 `xs union (ys as Set<*>)`
>
> 반면에 중위 함수 호출은 부울 연산자(boolean operators), && 연산자, || 연산자, is- 연산와 in-checks 등 일부 다른 연산자보다 높은 우선순위를 가지고 있습니다. 아래의 표현식들은 각각 같은 의미를 가집니다.
> - `a && b xor c` 와 `a && (b xor c)`
> - `a xor b in c` 와 `(a xor b) in c`
> 
> 이외의 전체 연산자 우선 순위 계층 구조는 [Grammar reference](https://kotlinlang.org/docs/reference/grammar.html#expressions)에서 볼 수 있습니다.

중위 함수 호출을 사용하기 위해서는 항상 수신자(receiver)와 매개 변수를 지정해야만 합니다. 중위 함수 호출 표기법을 사용하여 현재의 수신자에서 메소드를 호출할 때에는 `this`를 명시적으로 사용해야만 합니다. 명확한 구문 분석을 위해서입니다. 이 떄 `this`는 일반적으로 메소드를 호출할때처럼 생략할 수 없습니다.

```kotlin
class MyStringCollection {
    infix fun add(s: String) { /*...*/ }

    fun build() {
        this add "abc" // 맞는 문법입니다.
        add("abc") // 맞는 문법입니다.
        // add "abc" // 틀린 문법입니다. receiver가 명확하게 지정되어야만 합니다.
    }
}
```

## 함수의 범위(Function scope)
코틀린에서 함수는 파일에서 최상위에 선언될 수 있습니다. 즉, Java/C#/Scala와 같이 함수를 가지기 위해 클래스를 작성할 필요가 없습니다. 최상위 함수 이외에도, 지역(local) 선언으로 멤버 함수 및 확장 함수를 선언할 수 있습니다.

### 지역 함수(Local functions)
코틀린은 지역 함수(함수 안에 다른 함수를 선언하는 것)를 지원합니다.

```kotlin
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: MutableSet<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```

지역 함수는 외부 함수(outer functions)의 지역 변수(local variables), 즉 클로저(closure)에 접근할 수 있으므로 위의 코드에서는 `visited`가 지역 변수가 될 수 있습니다:

```kotlin
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v)
    }

    dfs(graph.verticesp[0])
}
```

### 멤버 함수(Member functions)
멤버 함수는 클래스나 객체 안에서 정의된 함수를 의미합니다:

```kotlin
class Sample() {
    fun foo() { print("Foo") }
}
```

멤버 함수는 구분자 `dot(.)` 를 사용하여 호출할 수 있습니다.
```kotlin
Sample().foo() // Sample 클래스의 인스턴스를 생성하고 foo 함수를 호출함.
```

[Classes](https://kotlinlang.org/docs/reference/classes.html)와 [Inheritance](https://kotlinlang.org/docs/reference/classes.html#inheritance) 부분에 클래스와 오버라이딩 멤버에 대한 내용이 나와있습니다.

## 제네릭 함수(Generic functions)
함수 이름 앞에 꺾쇠 괄호 `<>` 를 사용하여 제네릭 매개변수를 지정할 수 있습니다:

```kotlin
fun <T> sungletonList(item: T): List<T> { /*...*/ }
```

제네릭 함수에 대한 설명은 [제네릭(Generics)](https://kotlinlang.org/docs/reference/generics.html) 부분에서 볼 수 있습니다.

## 인라인 함수(Inline functions)
인라인 함수에 대한 설명은 [이곳](https://kotlinlang.org/docs/reference/inline-functions.html)에서 볼 수 있습니다.

## 확장 함수(Extension functions)
[확장 함수](https://kotlinlang.org/docs/reference/extensions.html) 부분에서 내용을 확인할 수 있습니다.

## 고차원 함수와 람다(Higher-order functions and lambdas)
[고차원 함수와 람다 포스팅](https://shinjekim.github.io/kotlin/2019/09/05/Kotlin-%EA%B3%A0%EC%B0%A8%EC%9B%90-%ED%95%A8%EC%88%98%EC%99%80-%EB%9E%8C%EB%8B%A4/)에 설명되어 있습니다.

## 꼬리 재귀 함수(Tail recursive functions)
코틀린은 [꼬리 재귀(tail recursion)](https://en.wikipedia.org/wiki/Tail_call)로 알려진 함수형 프로그래밍을 방식을 지원합니다. 이를 통해 스택 오버플로(stack overflow)의 위험 없이 재귀를 사용할 수 있습니다. 함수에 `tailrec` 변경자가 지정되고 필요한 조건을 충족하였다면, 컴파일러가 재귀를 최적화하여 빠르고 효율적인 루프 기반 버전으로 남겨줍니다. 

```kotlin
val eps = 1E-10 // "good enough", could be 10^-15 

tailrec fun findFixPoint(x: Double = 1.0): Double
    = if (Math.abs(x = Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```

이 코드는 수학적인 상수인 코사인(cosine)의 고정점(fixpoint)을 계산합니다. 결과가 더 이상 변하지 않을때까지 1.0부터 반복적으로 Math.cos 함수를 호출하여 지정된 `eps` 정밀도와 비교하여 0.7390851332151611라는 결괄르 도출합니다. 

```kotlin
val eps = 1E-10 // "good enough", could be 10^-15

private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (Math.abs(x - y) < eps) return x
        x = Math.cos(x)
    }
}
```

`tailrec` 변경자를 사용하기 위해서는 함수가 마지막으로 수행될 때 자기 자신을 호출해야만 합니다. 만약 재귀 호출 후에 실행될 코드가 있다면 꼬리재귀를 사용할 수 없습니다. 또한 꼬리재귀는 try/catch/finally 블록 내에서 사용할 수 없습니다. 현재는 JVM 기반의 코틀린과 Kotlin/Native에서 꼬리 재귀를 지원합니다.

### 참고 문헌
- [매개변수(parameter)와 전달인자(argument)](https://wayhome25.github.io/etc/2017/12/31/parameter-argument/)
- [코틀린 공식문서](https://kotlinlang.org/docs/reference)
- [코틀린 인 액션](http://acornpub.co.kr/book/kotlin-in-action)
