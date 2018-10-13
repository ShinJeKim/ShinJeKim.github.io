---
layout: post
title: "[javascript] 프로그래머스|Level1|자연수 뒤집어 배열로 만들기"
date: 2018-10-13
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
**문제 설명**
자연수 n을 뒤집어 각 자리 숫자를 원소로 가지는 배열 형태로 리턴해주세요. 예를들어 n이 12345이면 [5,4,3,2,1]을 리턴합니다.

**제한 조건**
n은 10,000,000,000이하인 자연수입니다.

**나의 풀이**
~~~
function solution(n) {
    var arr = n.toString().split('');
    var answer = [];

    for(var i=arr.length-1; i>=0; i--){
        answer.push(Number(arr[i]));
    }

    return answer;
}
~~~

**다른 사람의 풀이 1**
~~~
function solution(n) {
    return n.toString().split('').reverse().map(o => o = parseInt(o));
}
~~~
