---
layout: post
title: "[Kotlin] 자바의 try-with-resource 구문과 코틀린의 use 함수"
date: 2019-11-01
categories: Kotlin
tags: Kotlin
comments: true
---

## 자바의 try with resources문
자원 입출력을 수행할 때 메모리 누수를 방지하기 위해 아래와 같이 try-finally 구문을 사용할 수 있습니다. 

```java
static readFirstLine(String path) throws IOException{
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine;
    }finally {
        br.close();
    }
}
```

try-finally 구문에는 문제점이 있습니다. 우선, 자원을 둘 이상 사용하게 되는 경우 try문이 중첩되어야 한다는 것입니다. 

또 다른 문제점은 위와 같은 코드에서는 try블록과 finally 블록 둘 다에서 예외가 발생하는 경우 문제의 원인을 찾기가 힘들게 됩니다. 만약 기기에서 물리적인 문제가 생기면 try 블록의 readLine과 finally 블록의 close 모두에서 예외가 발생할 것입니다. 이렇게 되면 두 번째 예외가 첫 번째 예외를 덮어버려서 스택 추적 내의 첫 번째 예외에 관한 정보가 남지 않게 된다고 합니다. 

이러한 문제들을 해결하기 위해 자바 7에서 `try-with-resources`라는 것이 나왔습니다. 위의 try-finally 구문을 try-with-resources로 변경하면 아래와 같습니다. 

```java
static readFirstLine(String path) throws IOException{
    try(BufferedReader br = new BufferedReader(new FileReader(path))){
        return br.readLine();
    }
}
```

코드가 더 짧고 명확해졌습니다. [이펙티브 자바의 아이템 9](#참고-자료)에서는 꼭 회수해야 하는 자원을 다룰 때에는 try-finally말고, try-with-resources를 사용하도록 해야 한다고 권합니다.

## 코틀린의 use 함수
그렇다면 자바의 try-with-resources를 코틀린에서는 어떻게 사용할까요? 
코틀린에서는 이를 언어 구성 요소로 제공하지는 않습니다. 대신 자바의 try-with-resources와 같은 기능을 제공하는 use라는 함수를 제공합니다.
위의 try-with-resource는 코틀린의 use를 사용하여 작성하면 아래와 같습니다.

```kotlin
fun readFirstLine(path: String): String {
    BufferedReader(FileReader(path)).use { br ->
        return br.readLine()
    }
}
```

이 때 주의할점은 람다의 본문 안에서 사용한 return은 비지역 반환이라는 것입니다. 따라서 이 return문은 람다가 아니라 readFirstLine 함수를 끝내면서 값을 반환하게 됩니다. 

### 참고 자료
- [이펙티브 자바 - 아이템 9. try-finally보다는 try-with-resources를 사용하라](https://blog.insightbook.co.kr/2018/10/24/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-3%ED%8C%90effective-java-3-e/)
- [코틀린 인 액션](http://acornpub.co.kr/book/kotlin-in-action)
- [[블로그] 코끼리를 냉장고에 넣는 방법 - 자바 향상된 예외 처리 try-with-resources 문](https://dololak.tistory.com/67)
