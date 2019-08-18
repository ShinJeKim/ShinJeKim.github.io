---
layout: post
title: "[JavaScript] 기본 내용 정리-(2)"
date: 2018-09-08
categories: JavaScript
tags: JavaScript 
---

### 배열
값을 저장할 수 있는 엘리먼트(변수)의 연속된 공간을 의미한다. 주소(index)를 이용해 각 엘리먼트에 접근 가능함.

### 배열 정의 방법
- 빈 배열 &nbsp;&nbsp; ex)var arr = [];
- 초기화 &nbsp;&nbsp; ex)var arr = [1, 2, 3];
- 어떤 자료형이든 저장이 가능함
- 배열 길이 반환 &nbsp;&nbsp; arr.length
- 접근 &nbsp;&nbsp; arr[index]

### 배열 사용하기
1. 배열에 엘리먼트 추가하기
    - .push(element): 배열 뒤에 element 추가
    - .pop(): 배열 뒤에서 엘리먼트 삭제 후 리턴
    - .shift(): 배열 앞에서 엘리먼트 삭제 후 리턴
    - .unshift(element): 배열 앞에 엘리먼트 추가

2. 배열 붙이기, 검색하기
    - arr1.concat(arr2): arr1과 arr2를 붙임
    - arr.indexOf(element): arr에서 element가 있는 첫 위치를 검색함
    - arr.lastIndexOf(element): arr에서 element가 있는 마지막 위치를 검색함

3. 문자열 split 함수
문자열을 구분자(separator)로 나눠서 각각을 담은 배열을 반환하는 함수

### 변수의 scope
- 선언한 변수가 유효한 지역을 의미.
- function scope
	- 선언된 변수는 선언된 함수 안에서만 접근이 가능함
	- 선언된 함수 안에서 선언된 함수(nested function)에서도 접근이 가능함	

### 변수의 shadowing
1. 함수 밖에서 선언된 같은 이름의 변수를 함수 안에서도 사용할 때
    - 함수 밖의 변수는 잠시 가려짐
    - 함수 안에서는 해당 함수에서의 변수를 사용함(함수 밖 변수의 값은 변하지 않음)
    - 함수에서 빠져나오면 다시 해당 변수에 접근할 수 있음

2. 함수 안에서만 값이 유지되어야 하는 경우
	- 함수 안에서 var 키워드를 사용해서 선언한 뒤 사용

3. 여러 함수에서 값이 유지되고 사용되어야 할 때
	- 전역 변수를 사용
