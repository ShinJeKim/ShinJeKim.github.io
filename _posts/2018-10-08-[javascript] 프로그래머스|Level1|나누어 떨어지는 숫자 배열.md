---
layout: post
title: "[javascript] 프로그래머스|Level1|나누어 떨어지는 숫자 배열"
date: 2018-10-08
categories: Algorithms
tags: JavaScript
---
**문제 설명**
array의 각 element 중 divisor로 나누어 떨어지는 값을 오름차순으로 정렬한 배열을 반환하는 함수, solution을 작성해주세요.
divisor로 나누어 떨어지는 element가 하나도 없다면 배열에 -1을 담아 반환하세요.

**제한 조건**
- arr은 자연수를 담은 배열입니다.
- 정수 i, j에 대해 i ≠ j 이면 arr[i] ≠ arr[j] 입니다.
- divisor는 자연수입니다.
- array는 길이 1 이상인 배열입니다.


**나의 풀이**
~~~
function solution(arr, divisor) {
    var answer = [];
    var count = 0;

    for(var i=0; i<arr.length; i++){
        if(arr[i]%divisor == 0){
            answer.push(arr[i]);
            count++;
        }
    }

    if(count == 0){
        answer = [-1];
    }

    answer.sort(function(a, b){
        return a-b;
    });

    return answer;
}
~~~

**다른 사람의 풀이**
~~~
function solution(arr, divisor) {
    var answer = [];
    arr.map((o) => {
        o % divisor === 0 && answer.push(o);
    })
    return answer.length ? answer.sort((a, b) => a - b) : [-1];

}
~~~

