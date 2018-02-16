---
layout: post
title: '[javascript] 자바스크립트 - 연산자, 배열'
author: chanhee.kim
date: 2018-01-30 11:59
tags: [javascript]
image: /files/covers/blog.jpg
---

##### 연산자
- 대부분 java의 연산자와 동일 하지만 아래는 코드들은 예외

``` javascript
7 + "5"; // "75"
7 - "5" // 2
"aaa" - 3; // Nan

10 ** 3 // 1000 10의 3제곱

0.1 * 10 // 1 , 1.0이 아님

"2" > "12" // true, string은 사전순서로 비교
"2" > 12 // false

1="1" // true
0=="0" // true

undefined==0 // flase
undefined==null // true
undefined===null // false
```

- ==는 값만 비교하지만, ===는 타입까지 비교한다.
- ===와 달리 ==는 피연산자의 타입을 일치시킨 후 비교한다.

##### 배열
- java와 달리 배열의 크기는 동적으로 변경가능하다.
- 하나의 배열에 여러 타입의 값을 저장할 수 있다.

```javascript
var arr = [1,2,3,4,5];
console.log(arr[0]); // 1

arr[6] = 100; // 7번째 요소에 100을 저장

console.log(arr); // [1, 2, 3, 4, 5, undefined, 100]
console.log(arr[5]); // undefined
console.log(arr.length); // 7
```

- 배열도 객체이며, new 연산자로 생성할 수 있다.
``` javascript
var arr = new Array(1,2,3,4,5); // var arr = [1,2,3,4,5];
var arr2 = new Array(40); // 40개의 undefined요소들이 저장된 배열
```

##### 배열 - 메서드
- 배열 만들기
```javascript
var fruits = ['사과', '바나나'];
```

- 인덱스로 배열 접근
```javascript
console.log(fruits[0]);// 사과
console.log(fruits[fruits.length - 1]); // 바나나
```

- 배열 순환 - foreach()
```javascript
fruits.forEach(function (item, index, array) {
  console.log(item, index);
});
// 사과 0
// 바나나 1
```

- 배열 끝에 항목추가 - push()
```javascript
fruits.push('오렌지');
console.log(fruits); // ["사과", "바나나", "오렌지"]
```

- 배열 끝에 항목 제거 - pop()
```javascript
fruits.pop(); // 끝에있던 '오렌지'를 제거
console.log(fruits); // ["사과", "바나나"];
```

- 배열 앞에 항목 제거 - shift()
```javascript
fruits.shift(); // 제일 앞의 '사과'를 제거
console.log(fruits); // ["바나나"];
```

- 배열 앞에 항목 추가 - unshift()
```javascript
fruits.unshift('딸기') // 앞에 추가
console.log(fruits); // ["딸기", "바나나"];
```

- 배열 안 항목 인덱스 찾기 - indexOf()
``` javascript
var pos = fruits.indexOf("바나나");
console.log(pos); // 1
```

- 인덱스 위치에 있는 항목 추가 및 제거 - splice()
``` javascript
fruits.splice(1, 1); // 항목을 제거하는 방법
//splice(start, deleteCount, item1, item2, ...)
console.log(fruits); // ["딸기"]

fruits.splice(1, 0, "망고") // 두번째 인자가 0 일경우 삭제X
console.log(fruits); // ["딸기", "망고"]
```

- 배열안에 값을 하나 반환 - find()
``` javascript
var array1 = [5, 12, 8, 130, 44];

var found = array1.find(function(element) {
  return element > 10;
});

console.log(found); // 12
```

- 배열안에 값을 여러개반환 - filter()
  - 실제로는 새로운 배열을 만드는 것이다. (API 참조)
``` javascript
var array1 = [5, 12, 8, 130, 44];

var filter = array1.filter(function(element) {
  return element > 10;
});

console.log(filter); // [12, 130, 44]
```

- 배열을 문자열로 반환(쉼표(,)로 구분) - toString()
```javascript
var monthNames = ['Jan', 'Feb', 'Mar', 'Apr'];
console.log(monthNames.toString()); // 'Jan,Feb,Mar,Apr'
```

- 배열의 모든 요소를 연결해 하나의 문자열로 만듦 - join()
```javascript
var monthNames = ['Jan', 'Feb', 'Mar', 'Apr'];
console.log(monthNames.join(" - ")); // 'Jan - Feb - Mar - Apr'
```

- 배열 합치기 - concat()
``` javascript
var string = ['a', 'b', 'c'],
    number = [1, 2, 3];

var concat = string.concat(number);

console.log(concat); // ['a', 'b', 'c', 1, 2, 3]
```
