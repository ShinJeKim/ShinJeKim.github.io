---
layout: post
title: "[javascript] 프로그래머스|Level1|핸드폰 번호 가리기"
date: 2018-09-27
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
**문제 설명**
프로그래머스 모바일은 개인정보 보호를 위해 고지서를 보낼 때 고객들의 전화번호의 일부를 가립니다.
전화번호가 문자열 phone_number로 주어졌을 때, 전화번호의 뒷 4자리를 제외한 나머지 숫자를 전부 *으로 가린 문자열을 리턴하는 함수, solution을 완성해주세요.

**제한 조건**
s는 길이 4 이상, 20이하인 문자열입니다.

**나의 풀이**
~~~
function solution(phone_number) {
    return phone_number.replace((phone_number.substring(0, phone_number.length-4)), '*'.repeat(phone_number.length-4));
}
~~~

**다른 사람의 풀이 1**
~~~
function solution(phone_number) {
  return phone_number.replace(/\d(?=\d{4})/g, "*");
}
~~~
**다른 사람의 풀이 2**
~~~
function solution(phone_number){
  var result = "*".repeat(phone_number.length - 4) + phone_number.slice(-4);

  return result;
}
~~~

replace와 repeat 함수를 써서 코드양을 줄이기는 한 것 같은데 실행시간이 생각보다 너무 오래 걸렸다. 정규식으로 풀어내는 방법과 slice 함수를 쓰는 풀이도 있었다. 정규식은 정말 다양한 문제를 간단하게 해결해주는 것 같다.
