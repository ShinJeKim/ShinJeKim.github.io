---
layout: post
title: "[javascript] 프로그래머스|Level1|두 정수 사이의 합"
date: 2018-09-27
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
**문제 설명**
두 정수 a, b가 주어졌을 때 a와 b 사이에 속한 모든 정수의 합을 리턴하는 함수, solution을 완성하세요.
예를 들어 a = 3, b = 5인 경우, 3 + 4 + 5 = 12이므로 12를 리턴합니다.

**제한 조건**
- a와 b가 같은 경우는 둘 중 아무 수나 리턴하세요.
- a와 b는 -10,000,000 이상 10,000,000 이하인 정수입니다.
- a와 b의 대소관계는 정해져있지 않습니다.

**나의 풀이**
~~~
function solution(a, b) {
    var answer = 0;

    if(a>b){
        var temp = a;
        a = b;
        b = temp;
    }

    for(var i=a; i<=b; i++){
        answer += i;
    }

    return answer;
}
~~~

**다른 사람의 풀이**
~~~
function solution(x){
  return (a+b)*(Math.abs(b-a)+1)/2;
}
~~~
절대값을 반환하는 함수와 숫자의 합을 구하는 공식으로 이렇게 간단하게 구현할 수 있는 것을..ㅜ


