---
layout: post
title: '[JavaScript] 기초 문법 - 변수, 호이스팅, 형변환'
author: chanhee.kim
date: 2018-01-29 09:21
updated_at: 2026-02-16
tags: [javascript, variable, hoisting, type-conversion, fundamentals]
image: /files/covers/blog.jpg
description: "JavaScript 기초 문법인 변수 선언, 호이스팅, 형변환을 정리합니다."
---

##### 자바스크립트란?
- 1995년 Brendan Eich가 발명
- 1997년부터 ECMA가 표준화, 현재는 7까지 나옴
- 자바스크립트(JavaScript)는 웹을 위한 인터프리터 언어이자 스크립트 언어다.
- 컴파일이 필요하지 않다. 그냥 HTML 웹 페이지에 스크립트를 삽입하기만 하면 동작하며 최신 웹 브라우저에서 모두 동작한다.

##### 자료형
- tyepof 연산자로 변수나 리터럴 타입 확인가능
- false로 나오는 값 : 0, -0, "", null, false, NaN, undefined
- true로 나오는 값 : false가 아닌값 전부

<table>
  <tr>
    <td>숫자형</td>
    <td>number</td>
    <td>123, 0, -1, 3.14 등</td>
  </tr>
  <tr>
    <td>문자형</td>
    <td>String</td>
    <td>"aaa", "", "123", 'aaa', '1' 등</td>
  </tr>
  <tr>
    <td>논리형</td>
    <td>boolean</td>
    <td>false, true</td>
  </tr>
  <tr>
    <td>기타</td>
    <td>null<br>undefined</td>
    <td>null, undefined</td>
  </tr>
  <tr>
    <td rowspan="2">파생형</td>
    <td>object</td>
    <td rowspan="2">- 모든객체의 타입은 object <br>- 배열, 함수도 객체다<br> but 함수의 타입은 function</td>
  </tr>
  <tr>
    <td>function</td>
  </tr>
</table>

##### 동적 타입

- 자바스크립트에서는 변수의 타입은 동적으로 결정
- 값이 저장되지 않은 변수의 타입의 값은 undefined

```
var x; // x의 타입은 undefined, 값도 undefined
```

- 값이 저장되어야 변수의 타입이 결정된다.

```
x = 1; // x의 타입은 number, 값은 10
```

- 다른타입의 값이 저장되면, 변수의 타입이 바뀐다.

```
x = "aaa" // x의 타입은 string, 값은 "abc"
```

##### 변수의 범위(scope)
- 전역 변수
  - 함수의 외부에서 선언된 모든 변수는 전역 범위(global scope)를 가집니다. 브라우저에서, 전역 컨텍스트(또는 scope)는 window 객체를 가리킵니다.
- 지역 변수
  - 함수내에 정의된 변수는 지역 범위이며, 해당 함수와 내부함수에서만 접근가능하다. 예외로 클로저(closure)를 이용하면 외부에 접근이 가능하다.

##### ※ 호이스팅(hoisting)
- 호이스팅이란 변수를 선언하고 초기화했을 때 선언 부분이 최상단으로 끌어올려지는 현상을 의미합니다.

``` javascript
function showName() {
     console.log("First Name : " + name);
     var name = "Ford";
     console.log("Last Name : " + name);
}
showName();
// First Name : undefined
// Last Name : Ford
// First Name이 undefined인 이유는 지역변수 name이 호이스트 되었기 때문입니다.
```
이 코드는 자바스크립트 엔진에 의해 다음과 같이 해석됩니다.

``` javascript
function showName() {
     var name; // name 변수는 호이스트 되었습니다. 할당은 이후에 발생하기 때문에, 이 시점에 name의 값은 undefined 입니다.
     console.log("First name : " + name); // First Name : undefined
     name = "Ford"; // name에 값이 할당 되었습니다.
     console.log("Last Name : " + name); // Last Name : Ford
}
```
변수의 선언이 최상단으로 끌어올려졌기 때문이다.

``` javascript
sayWow();
sayYeah(); // 여기서 대입되기 전에 호출해서 에러
var sayYeah = function() {
  console.log('yeah');
}
function sayWow() { // 선언과 동시에 초기화(호이스팅)
  console.log('wow');
}
```
하지만 같은 함수여도 함수 표현식으로 선언한 경우에는 에러가 발생합니다.

##### 형변환
- 숫자변환 함수 : Number(), parseInt(), parseFLoat()
  - Number() 와 parseInt() 차이
  - 문자열이 숫자가 아닌 경우가 Number()와 조금 다른데 문자열이 숫자로 시작하는 경우에는 숫자가 끝날때 까지만 형변환을 하여 num에 저장됩니다. 시작이 숫자가 아니면 Number()와 마찬가지로 num에 NaN이 저장됩니다.
  ``` javascript
   var x = Number("123abc") // x=NaN
   var x = parseInt("123abc") // x=123
   var x = parseInt("abc123") // x=NaN
  ```
- 문자열변환 함수 : String()
- boolean변환 함수 : Boolean(), !!
