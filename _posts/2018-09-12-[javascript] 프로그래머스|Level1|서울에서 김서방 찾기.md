---
layout: post
title: "[javascript] 프로그래머스|Level1|서울에서 김서방 찾기"
date: 2018-09-12
categories: Algorithms
tags: JavaScript
---
**문제 설명**
String형 배열 seoul의 element중 "Kim"의 위치 x를 찾아, 김서방은 x에 있다는 String을 반환하는 함수, solution을 완성하세요. seoul에 "Kim"은 오직 한 번만 나타나며 잘못된 값이 입력되는 경우는 없습니다.

**제한 조건**
- seoul은 길이 1 이상, 1000 이하인 배열입니다.
- seoul의 원소는 길이 1 이상, 20 이하인 문자열입니다.
- "Kim은" 반드시 seoul 안에 포함되어 있습니다.

**나의 풀이**
~~~
function solution(seoul) {
    var answer = '';

    if(seoul.length<1 || seoul.length>1000){
        return false;
    }

    for(var x=0; x<seoul.length; x++){
        if(seoul[x]<1 || seoul[x]>20){
            return false;
        }

        if(seoul[x] === 'Kim'){
            answer = "김서방은 "+x+"에 있다";
            break;
        }
    }
    return answer;
}
~~~

**다른 사람의 풀이**
~~~
function solution(seoul) {
  	var x = seoul.indexOf('Kim');
  	return "김서방은 " + x + "에 있다";
}
~~~

for문으로 돌리는 것밖에는 생각하지 못했었는데 indexOf로 깔끔하게 작성한 풀이를 보았다. 다른 사람의 풀이를 볼 때마다 느끼는 것은, 모르는 것이 아닌데도 막상 문제를 받으면 늘 하던 방식인 단순한 if문이나 for문밖에 생각이 나지 않는다는 것이다. 의도적으로 다른 방식을 찾아보려는 연습을 해야겠다.
