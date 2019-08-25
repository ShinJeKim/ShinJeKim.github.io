---
layout: post
title: "[javascript] 프로그래머스|Level1|하샤드 수"
date: 2018-09-17
categories: Algorithms
tags: JavaScript
comments: true
---
**문제 설명**
양의 정수 x가 하샤드 수이려면 x의 자릿수의 합으로 x가 나누어져야 합니다. 예를 들어 18의 자릿수 합은 1+8=9이고, 18은 9로 나누어 떨어지므로 18은 하샤드 수입니다. 자연수 n을 입력받아 n이 하샤드 수인지 아닌지 검사하는 함수, solution을 완성해주세요.

**제한 조건**
x는 1 이상, 10000 이하인 정수입니다.

**나의 풀이**
~~~
function solution(x) {
    var sum = 0;
    var arr = String(x).split('');

    for(var i=0; i<arr.length; i++) {
        sum += Number(arr[i]);
    }

  return (x % sum == 0) ? true : false;
}
~~~

**다른 사람의 풀이**
~~~
function solution(x){
  return !(x % (x + "").split("").reduce((a, b) => +b + +a ));
}
~~~

