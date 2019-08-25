---
layout: post
title: "[javascript] 프로그래머스|Level1|평균 구하기"
date: 2018-09-1
categories: Algorithms
tags: JavaScript
comments: true
---
**문제 설명**
정수를 담고 있는 배열 arr의 평균값을 return하는 함수, solution을 완성해보세요.

**제한 조건**
- arr은 길이 1 이상, 100 이하인 배열입니다.
- arr의 원소는 -10,000 이상 10,000 이하인 정수입니다.

**나의 풀이**
~~~
function solution(arr) {
    var answer = 0;
    for(var i=0; i<arr.length; i++){
        answer += arr[i];
    }
    return answer/arr.length;
}
~~~

**다른 사람의 풀이 1.**
~~~
function solution(arr) {
    var answer = 0;
    for(var value of arr){
        answer += value;
    }
    return answer/arr.length;
}
~~~

**다른 사람의 풀이 2.**
~~~
function solution(arr) {
    return arr.reduce((a, b) => a + b) / arr.length;
}
~~~
<br>
새롭게 알게된 것
- for ... of 의 사용법
- reduce 함수
