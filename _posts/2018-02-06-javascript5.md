---
layout: post
title: '[JavaScript] 필수 개념 정리 - 실행 컨텍스트, 스코프 체인'
author: chanhee.kim
date: 2018-02-06 14:38
updated_at: 2026-02-16
tags: [javascript, frontend]
image: /files/covers/blog.jpg
description: "JavaScript 실행 컨텍스트와 스코프 체인 등 필수 개념을 정리합니다."
keywords: "필수 개념 정리 - 실행 컨텍스트, 스코프 체인, 자바스크립트, 프론트엔드, 개발 블로그"
---

참고 블로그 : <a href="http://jinbroing.tistory.com/183?category=687108"> 블로그 이동</a>

1. 함수를 통해 생성된 객체는 __proto__ (부모 객체의 프로토 타입을 저장하는 프로퍼티가 존재함)
  - new String, new Object, new 사용자 함수 등
  - 부모 객체의 prototype 객체 프로퍼티에 추가하면 상속 받은 객체들이추가된 메서드나 변수를 사용할 수있음
  - 프로토타입 체인 : 해당 객체에 프로퍼티가 존재 하지않을 경우 부모 객체로 올라감

2. var키워드로 변수를 선언하면 함수를 기준으로 스코프가 정해짐 if(var i=0;)이 특정 function에 속해져 있지 않으면 i는 전역 변수
  - 함수 내부에 선언된 변수는 함수내 스코프에서만 유효, 변수선원 키워드를 생략한채 선언하면 전역변수가 됨
  - 의도치 않은 무한 루프문
    - 전역변수 남발 X : 전역변수를 하나의 객체에 모아서 사용하는 것도 한 방법
    - 즉시실행함수 사용 : 즉시실행함수의 경우 호출되고 바로 사라짐
    - 변수의 이름 명확하게
    - 변수 키워드 확실하게(스코프 생각하면서)
  - for문 돌려 i 찍는 명령문에 함수 넣고 돌리면 i는 반복문의 마지막 수가 나옴

  ```javascript
  var funcArr=[];
  for(var i = 0; i< 5; i ++ ) {
    funcArr.push(function() {console.log(i)});
  }
  funcArr[0](); // 5출력
  ```

   - 클로저 활용하던가, let 키워드 사용
   - let은 함수레벨스코프가아닌 블록레벨 스코프

3. 내부함수는 외부함수의 스코프를 저장함
  - 클로저 스코프 : 외부함수의 스코프 정보 저장
  - 전역 스코프 : window 객체의 스코프 정보 저장

4. 자바스크립트는 함수선언과 할당을 분리함 : 선언문을 맨위로 끌어올림 (호이스팅)
  - var a = function() {} -> var a = undefined -> a = function() {}

5. 함수가 호출될 때 매개변수로 전달되는 인자값 이외에 arguments 객체와 this를 암묵적으로 전달 받음

6. 자바스크립트의 this에 바인딩되는 객체는 한가지가 아니다
  - 객체의 메서드 : obj2.sayName() -> obj2 전달 -> this에 obj2바인딩
    - prototype 객체의 메서드 : this -> 생성자 함수로 생성된객체,메서드 -> this에 생성된 객체 바인딩
  - 전역함수 : 전역객체 window 바인딩
    - 위와 같음
    - 함수의 내부함수도 this는 window 바인딩
    - 객체메서드의 내부 함수는 windows를 바인딩 : 메서드의 내부함수를 사용할때 this 사용 위험
    ```javascript
    var obj = {
      x : 10,
      foo : function() {
        var that = this;
        function inner() {
          console.log(this) // windows
          console.log(that) // obj
        }
      }
    }
    // call, apply 메서드 사용으로 해결가능
    ```

  - 생성자함수 : 일반함수처럼 인자값을 this에 추가하면 window에 추가되고, new 키워드를 사용해서 호출하면 생성되는 객체에 바인딩

7. 자바스크립트는 비동기처리 : 완료될때 까지 기다리는것이 아니라 다음 실행될 코드를 처리함
   - 동기처리를 할 경우 현재 실행되고 있는 코드가 완료될 때까지 다음코드가 싱핼되지 않음
   - 대부분의 함수처리를 비동기로 처리  
