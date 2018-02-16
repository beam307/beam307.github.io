---
layout: post
title: '[javascript] 자바스크립트 - 함수, 클로져(closure)'
author: chanhee.kim
date: 2018-01-31 15:43
tags: [javascript]
image: /files/covers/blog.jpg
---

##### 함수
- 함수의 선언과 호출.함수는 항상 값을 반환

``` javascript
var result = add(3,5); // 함수 선언전 호출 OK

function add(a, b) {
  return a + b;
}

function log(a) {
  console.log(a);
} // return undefined;

var result = add(3,5); // result = 8;
var result2 = log("abc"); //result2 = undefined
```

- 함수 표현식

``` javascript
var result = add(3,5) // 함수의 호출.에러

var add = function(a, b) { //익명함수. 이름을 적어도되지만 거의 생략
  return a + b;
}

var result = add(3,5); // 함수의 호출. OK
```
