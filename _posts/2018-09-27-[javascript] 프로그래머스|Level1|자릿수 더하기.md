---
layout: post
title: "[javascript] 프로그래머스|Level1|자릿수 더하기"
date: 2018-09-27
categories: Algorithms
tags: JavaScript
---
**문제 설명**
자연수 N이 주어지면, N의 각 자릿수의 합을 구해서 return 하는 solution 함수를 만들어 주세요.
예를들어 N = 123이면 1 + 2 + 3 = 6을 return 하면 됩니다.

**제한 조건**
N의 범위 : 100,000,000 이하의 자연수

**나의 풀이**
~~~
function solution(n)
{
    var answer = 0;
    var str = n.toString();
    var str_split = str.split('');

    for(var i=0; i<str_split.length; i++){
        answer += parseInt(str_split[i]);
    }

    return answer;
}
~~~

**다른 사람의 풀이**
~~~
function solution(n)
{
    var answer = 0;
    var m=String(n);

    for(var i=0; i<m.length; i++){
        answer+=parseInt(m[i]);
    }
    return answer;
}
~~~
굳이 split 함수를 쓰지 않아도 되는 것이었다는 것을 다른 사람의 풀이를 보고 알게 되다니..ㅠ 
능숙해지는 방법은 연습 연습 연습뿐이다.

