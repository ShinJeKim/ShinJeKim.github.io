---
layout: post
title: "[javascript] 프로그래머스|Level1|약수의 합"
date: 2018-09-11
categories: Algorithms
tags: JavaScript
comments: true
---
**문제 설명**
자연수 n을 입력받아 n의 약수를 모두 더한 값을 리턴하는 함수, solution을 완성해주세요.

**제한 조건**
n은 0 이상 3000이하인 자연수입니다.

**나의 풀이**
~~~
function solution(n) {
    var answer = 0;

    if(n<0 || n>3001){
        return false;
    }

    for(var i=0; i<=n; i++){
        if(n%i === 0){
            answer += i;
        }
    }
    return answer;
}
~~~

n이 0 미만이거나 3000 초과일 경우 false를 반환하도록 했다. 그리고 어떠한 수를 약수로 나누었을때 그 나머지가 0이라는 것을 활용하여 풀었다.

