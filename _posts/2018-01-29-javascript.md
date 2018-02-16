---
layout: post
title: '[javascript] 자바스크립트 개념'
author: chanhee.kim
date: 2018-01-29 09:21
tags: [javascript]
image: /files/covers/blog.jpg
---

###### 자바스크립트란?
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
