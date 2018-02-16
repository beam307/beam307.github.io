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

##### 함수 - 호출과 arguments 객체
- arguments객체로 함수 호출시 지정한 인자를 알 수 있다.

``` javascript
function findMax(a, b){
  var max = -Infinity // 음의 무한대

  console.log(arguments);

  for(var i = 0; i < arguments.length; i++ )
    if(arguments[i] > max)
      max = arguments[i]; // 배열처럼 접근

  return max;
}

var result = findMax(3,5);

console.log(result); // 5
```

- 함수에 선언된 매개변수의 수와 호출시 지정된 인자의 수가 달라도 된다.
- 함수이름.length로 함수에 선언된 매개변수의 매수를 알 수 있다.

##### 함수의 오버로딩
- arguments객체를 이용하면, 자바의 오버로딩을 구현할 수있다.

```javascript
function subArr(arr, start, end) {
  if(arguments.length == 1)
    return arr;
  if(arguments.length == 2)
    return subArr(arr, start, arr.length)
  if(arguments.length == 3)
    return arr.slice(start, end);
}

var arr = [0,1,2,3,4,5,6,7];
console.log(subArr(arr)); // [0, 1, 2, 3, 4, 5, 6, 7]
console.log(subArr(arr, 3)); // [3, 4, 5, 6, 7]
console.log(subArr(arr, 3, 5)); // [3, 4]
```

##### ※ 클로저(closure)
- 클로저는 외부함수(포함하고 있는)의 변수에 접근할 수 있는 내부 함수를 일컫습니다. 스코프 체인(scope chain)으로 표현되기도 합니다. 클로저는 세가지 스코프 체인을 가집니다: 클로저 자신에 대한 접근(자신의 블럭내에 정의된 변수), 외부 함수의 변수에 대한 접근, 그리고 전역 변수에 대한 접근. 이렇게 3단계로 구분할 수 있습니다.

- 내부 함수는 외부 함수의 변수뿐만 아니라 파라미터에도 접근할 수 있습니다. 단, 내부 함수는 외부 함수의 arguments 객체를 호출할 수는 없습니다. (하지만, 외부 함수의 파라미터는 직접 호출할 수 있습니다.)

###### 기본적인 클로저 예제

``` javascript
function showName(firstName, lastName) {
    var nameIntro = "Your name is ";
    // 이 내부 함수는 외부함수의 변수뿐만 아니라 파라미터 까지 사용할 수 있습니다.
    function makeFullName() {
        return nameIntro + firstName + " " + lastName;
    }
    return makeFullName();
}
var result = showName("Michael", "Jackson");

console.log(result); // Your name is Michael Jackson
```
- 클로저는 Node.js의 비동기, 논-블록킹 아키텍처의 핵심기능으로 활용되고 있습니다. 클로저는 jQuery에서도 빈번히 사용되며, 거의 모든 자바스크립트 코드에서 볼 수 있습니다.

###### jQuery의 전형적인 클로저 사용예

``` javascript
$(function() {
    var selections = [];
    $(".niners").click(function() { // 이 클로저는 selections 변수에 접근합니다.
        selections.push(this.prop("name")); // 외부 함수의 selections 변수를 갱신함
    });
});
```

###### 클로저 규칙과 부수 효과

- 클로저는 외부함수가 리턴된 이후에도 외부함수의 변수에 접근할수 있습니다.
- 클로저를 사용하면서 가장 헷갈리는것 중의 하나는 외부함수가 리턴된 이후에도 여전히 내부함수가 외부함수의 변수에 접근하고 있다는 것입니다. (네. 당신이 바로 읽은것 맞습니다.-.-) 자바스크립트의 함수가 실행되었을때, 함수는 자신이 생성되었을때와 동일한 스코프 체인을 사용합니다. 그러므로, 당신은 내부 함수를 나중에 호출할 수 있습니다.

``` javascript
function celebrityName(firstName) {
    var nameIntro = "This is celebrity is ";
    // 이 내부 함수는 외부함수의 변수와 파라미터에 접근할 수 있습니다.
    function lastName(theLastName) {
        return nameIntro + firstName + " " + theLastName;
    }
    return lastName;
}
var mjName = celebrityName("Michael"); // 여기서 celebrityName 외부함수가 리턴됩니다.
// 외부함수가 위에서 리턴된 후에, 클로저(lastName)가 호출됩니다.
// 아직, 클로저는 외부함수의 변수와 파라미터에 접근 가능합니다.
console.log(mjName("Jackson")); // This celebrity is Michael Jackson
```

###### 클로저 비꼬기
- 클로저가 갱신된 외부함수의 변수에 접근함으로써, 외부 함수의 변수가 for문에 의해 변경될 경우 의도치 않은 버그가 발생할 수 있습니다.

```javascript
for(var i = 0; i < 3; i++)
  setTimeout(function() {alert(i);}, 0);
// 1,2,3이 아니라 3,3,3이 출력된다.
```

- 이럴때 콜백함수를 클로져로 변경하면 올바른 결과를 얻는다.

```javascript
for(var i = 0; i < 3; i++) {
  (function(x) {
    setTimeout(function() {alert(x);}, 0);
  })(i);
}
```
- 가장 중요한 것은 클로저를 이용할 때 외부함수의 지역변수 값이여야함 : 외부함수 값을 참조하기때문에 외부함수 라이프사이클이 죽지않음)
