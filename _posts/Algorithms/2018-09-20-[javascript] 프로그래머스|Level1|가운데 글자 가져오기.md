---
layout: post
title: "[javascript] 프로그래머스|Level1|가운데 글자 가져오기"
date: 2018-09-20
categories: Algorithms
tags: JavaScript
comments: true
---
**문제 설명**
단어 s의 가운데 글자를 반환하는 함수, solution을 만들어 보세요. 단어의 길이가 짝수라면 가운데 두글자를 반환하면 됩니다.

**제한 조건**
s는 길이가 1 이상, 100이하인 스트링입니다.

**입출력 예**
예를들어 s가 "abcde"이면 "c"를 반환하고, "qwer"이면 "we"를 반환하면 됩니다.

**나의 풀이**
~~~
function solution(s) {
    var answer = '';
    var i = s.length/2;
    var j = s.length/2 - 0.5;

    if(s.length%2 === 0){
        answer += s[i-1]+s[i];
    }else{
        answer += s[j];
    }

    return answer;
}
~~~

**다른 사람의 풀이**
~~~
function solution(s) {
    return s.substr(Math.ceil(s.length / 2) - 1, s.length % 2 === 0 ? 2 : 1);
}
~~~
반올림 함수를 사용할 생각을 왜 안했을까.
파라미터 안에 삼항자를 넣어서 한줄로 표현할 수도 있다는 것을 알게 되었다.
