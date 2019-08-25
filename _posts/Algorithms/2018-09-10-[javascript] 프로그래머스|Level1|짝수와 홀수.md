---
layout: post
title: "[JavaScript] 프로그래머스|Level1|짝수와 홀수"
date: 2018-09-10
categories: Algorithms
tags: JavaScript
comments: true
---
**문제 설명**
정수 num이 짝수일 경우 Even을 반환하고 홀수인 경우 Odd를 반환하는 함수, solution을 완성해주세요.

**제한 조건**
num은 int 범위의 정수이며 0은 짝수입니다.

**나의 풀이**
~~~
function solution(num) {
    var answer = '';
    return (num%2 === 0) ? answer = 'Even' : answer = 'Odd';
}
~~~

**다른 사람의 풀이**
~~~
function solution(num) {
    return num % 2 ? "Odd" : "Even";
}
~~~
<br>
0이 false라는 것을 활용하여 굳이 num%2 === 0 이라는 것을 기입하지 않고도 깔끔하게 풀어낸 코드가 있었다. 다음부턴 무작정 풀기 전에 어떻게 하면 불필요한 부분을 줄이고 가독성있는 알고리즘을 짤지에 대해 조금 더 고민해보아야겠다.
