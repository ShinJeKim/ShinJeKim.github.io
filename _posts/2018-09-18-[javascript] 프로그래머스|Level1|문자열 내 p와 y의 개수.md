---
layout: post
title: "[javascript] 프로그래머스|Level1|문자열 내 p와 y의 개수"
date: 2018-09-18
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
**문제 설명**
대문자와 소문자가 섞여있는 문자열 s가 주어집니다. s에 'p'의 개수와 'y'의 개수를 비교해 같으면 True, 다르면 False를 return 하는 solution를 완성하세요. 'p', 'y' 모두 하나도 없는 경우는 항상 True를 리턴합니다. 단, 개수를 비교할 때 대문자와 소문자는 구별하지 않습니다.

예를들어 s가 pPoooyY면 true를 return하고 Pyy라면 false를 return합니다.

**제한 조건**
- 문자열 s의 길이 : 50 이하의 자연수
- 문자열 s는 알파벳으로만 이루어져 있습니다.

**입출력 예**
예를들어 str이 "pPoooyY"이면 'p'의 개수 2개, 'y'의 개수 2개로 같으므로 true를 return 합니다. "Pyy"이면 'p'의 개수 1개, 'y'의 개수 2개로 다르므로 false를 return 합니다.

**나의 풀이**
~~~
function solution(s){
    var count_p = 0;
    var count_y = 0;

    for(var i=0; i<s.length; i++){
        if(s[i] === 'p' || s[i] === 'P'){
            count_p++;
        }
        if(s[i] === 'y' || s[i] === 'Y'){
            count_y++;
        }
    }
    return (count_p === count_y) ? true : false;
}
~~~

**다른 사람의 풀이**
~~~
function solution(s){
    return s.toUpperCase().split("P").length === s.toUpperCase().split("Y").length;
}
~~~
toUpperCase 함수와 split 함수를 쓰면 이렇게 간단하게 해결이 되는 것을..
사용하지 못하면 아는 것이 아니다. 반복 연습해야지!