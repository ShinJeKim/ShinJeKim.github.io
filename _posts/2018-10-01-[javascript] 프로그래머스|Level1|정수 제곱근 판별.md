---
layout: post
title: "[javascript] 프로그래머스|Level1|정수 제곱근 판별"
date: 2018-10-01
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
**문제 설명**
임의의 정수 n에 대해, n이 어떤 정수 x의 제곱인지 아닌지 판단하려 합니다.
n이 정수 x의 제곱이라면 x+1의 제곱을 리턴하고, n이 정수 x의 제곱이 아니라면 -1을 리턴하는 함수를 완성하세요.

**제한 조건**
n은 1이상, 50000000000000 이하인 정수입니다.

**입출력 예**
- 121은 정수 11의 제곱이므로, (11+1)를 제곱한 144를 리턴합니다.
- 3은 정수의 제곱이 아니므로, -1을 리턴합니다.

**나의 풀이**
~~~
function solution(n) {
    return (Number.isInteger(Math.sqrt(n))) ? Math.pow((Math.sqrt(n)+1), 2) : -1;
}
~~~

**다른 사람의 풀이**
~~~
function solution(n){
  var result = 0;
  var x = 0;
  while (x*x < n){
    x++;
  }
  if (x*x == n){
    x++;
    result = x*x;
  }else{
    result = -1;
  }

  return result;
}
~~~
테스트 케이스에 따라 실행시간이 1~8ms 더 오래 걸리는 방법이기는 하지만 이렇게도 풀이를 할 수 있다는 것이 신기했다. 똑같은 문제에 대해서도 사람마다 다양한 방식으로 답을 찾아갈 수 있다는 것이 프로그래밍의 매력이라고 생각한다.


