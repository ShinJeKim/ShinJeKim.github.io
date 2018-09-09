---
layout: post
title: "[javascript] 프로그래머스|Level1|수박수박수박수박수박수?"
date: 2018-09-08
categories: algorithm
tags: javascript 자바스크립트 알고리즘
---
<strong>문제 설명</strong>
길이가 n이고, 수박수박수박수....와 같은 패턴을 유지하는 문자열을 리턴하는 함수, solution을 완성하세요. 예를들어 n이 4이면 수박수박을 리턴하고 3이라면 수박수를 리턴하면 됩니다.
<br>
<strong>제한 조건</strong>
n은 길이 10,000이하인 자연수입니다.
<br>
<strong>입출력 예</strong>
n | return
---- | ----
3 | 수박수
4 | 수박수박
<br>
<strong>나의 풀이</strong>
~~~
function solution(n) {
    if(n>10000) {
        return false;
    }else{
      var answer = '';
        for(var i=1; i<=n; i++){
            (i%2 ==0) ? answer += '박' : answer+= '수';
        }
        return answer;
    }
}
~~~
<br>
<strong>다른 사람의 풀이</strong>
~~~
const solution = n => {
    return '수박'.repeat(n/2) + (n%2 === 1 ? '수' : '');
}
~~~
<br>
나는 단순하게 생각하여 홀수이면 '수'를 짝수이면 '박'을 결과 스트링에 붙이는 방법으로 풀었다. 그런데 제한 조건을 반영하려면 길이 10,000을 초과하는 수는 들어오지 못하게 막아야 할 것 같은데 다른 사람들의 풀이를 보니 그런 부분을 작성한 코드는 없었다. 내가 잘못 이해한건가..?ㅜ
대부분 repeat 함수를 사용하였는데, 내가 거의 10줄로 표현한 코드를 단 한줄로 표현한 코드가 있었다. 나도 익혀서 내것으로 만들도록 노력해야겠다.
