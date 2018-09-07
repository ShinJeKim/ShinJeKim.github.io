---
layout: post
date: 2018-09-07
title: [javascript] 기본 내용 정리-(1)
categories: javascript
tags: javascript 자바스크립트
---

##### object 객체 속성 접근 방법
var human = {age: 20, nationality: korea}
1. 객체이름.속성이름
    => human.age = 20

2. 객체이름["속성이름"]
	=> human["age"] = 20

##### Math 관련 명령어
- Math.pow(A, b): A의 b승을 구함
- Math.sqrt(A): A의 제곱근을 구함
- Math.random(): 0~1 사이의 임의의 난수 발생

##### 문자열 다루기
1. 문자열 길이 알아내기
=> string.length

2. 문자열 붙이기
- concat
ex) str1.concat(str2)

- (+) 연산자
ex) str1 + str2

3. 특정 위치 문자열 알아내기
- .chatAt
첫문자 가져올때: str.charAt(0)
마지막 문자 가져올때: str.charAt(str.length-1)

- 대괄호([])
첫문자 가져올때: str[0]
마지막 문자 가져올때: str[str.length - 1]

4. 부분 문자열 구하기(대상 문자열의 연속된 일부 가져오기)
- .substring(position1, position2)
position1 ~ position2 까지의 문자열을 반환하며, position2 생략시 문자열 끝까지 반환

- .substr(position, length)
position에서부터 length 길이만큼의 문자열을 반환함. position이 음수인 경우, str.length - position 으로 동작함.

5. 문자열 검색
- indexOf(str)
- lastIndexOf(str)

