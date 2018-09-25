---
layout: post
title: "[javascript] 프로그래머스|Level1|문자열 다루기"
date: 2018-09-25
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
**문제 설명**
문자열 s의 길이가 4혹은 6이고, 숫자로만 구성되있는지 확인해주는 함수, solution을 완성하세요. 예를들어 s가 "a234"이면 False를 리턴하고 "1234"라면 True를 리턴하면 됩니다.

**제한 조건**
s는 길이 1 이상, 길이 8 이하인 문자열입니다.

**나의 풀이**
~~~
function solution(s) {
    var regex = /^[0-9]*$/;
    return ((s.length == 4 || s.length == 6) && (regex.test(s))) ? true : false;
}
~~~

**다른 사람의 풀이**
~~~
function solution(s){
  var regex = /^\d{6}$|^\d{4}$/;
  return regex.test(s);
}
~~~

전체를 정규식으로 할 생각은 못했는데, 정규식으로 하니 엄청 간단해졌다.
정규식을 더 연습해야지.

