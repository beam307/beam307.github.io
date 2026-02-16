---
layout: post
title: '[JavaScript] 객체, Prototype, this, 프로토타입 상속'
author: chanhee.kim
date: 2018-02-05 16:11
updated_at: 2026-02-16
tags: [javascript, object, prototype, this, inheritance, oop]
image: /files/covers/blog.jpg
description: "JavaScript 객체, 프로토타입, this 키워드, 프로토타입 상속을 정리합니다."
---

##### 객체 생성 방법
- 빈 객체를 생성한 후, 멤버(속성, 함수)를 추가

```javascript
var person = {}; // ver person = new Object(); 과 동일
person.name = "kimchanhee";
person.age = 28;
person.region = "sungnam";
```

- 객체의 생성과 동시에 멤버를 추가

```javascript
var myObject = {
  name : "kimcanhee",
  age : 28,
  introduce : function () {
    return this.name + " , " + this.age;
  }
}
```

<br>

##### instanceof와 typeof
- instanceof를 통해 객체가 어떤 생성자에 의해 생성되었는지 확인 가능

```javascript
function Person(name,age) {
  this.name = name;
  this.age = age;
}

var obj = new Object(); // var obj = {};
var p = new Person("chanhee", 28);
var s = new String();
var f = new Function(); //var f = function() {}; 와 동일

console.log(typeof obj); // object
console.log(typeof p); // object
console.log(typeof s); // object
console.log(typeof f); // function

console.log(obj instanceof Object); // true
console.log(p instanceof Object); // true
console.log(s instanceof Object); // true
console.log(f instanceof Object); // true

console.log(obj instanceof String); // false
console.log(p instanceof Person); // true
console.log(s instanceof String); // true
console.log(f instanceof Function); // true
```

<br>

##### 같은 타입의 객체라도 함수 공유X
- 같은 생성자로 생성한 객체라도 서로 다른 함수를 가질 수 있다.

``` javascript
function Person(name,age) {
  this.name = name;
  this.age = age;
}

var obj = new Person("aaa", 10);
var obj2 = new Person("bbb", 20);

obj.getName = function() {
  return this.name;
}

console.log(obj.getName()); // "aaa"
//console.log(obj2.getName()); //에러
```

<br>

##### prototype, __proto__, constructor

- 객체는 자신을 생성한 생성자의 prototype에 대한 참조인 __proto__와 constructor속성을 자동으로 갖는다.

``` javascript
function Person(name,age) {
  this.name = name;
  this.age = age;
}
var obj = new Object();

var p = new Person("aaa", 10);
var s = new String();
var f = new Function();

console.log(Object.prototype === obj.__proto__); // true
console.log(Person.prototype === p.__proto__); // true
console.log(String.prototype === s.__proto__); // true
console.log(Function.prototype === f.__proto__); // true

console.log(obj.constructor == Object); // true
console.log(p.constructor == Person); // true
```

<br>

##### 생성자의 prototype을 이용한함수 공유
- 생성자의 prototype에 함수를 추가하면 객체간 함수 공유 가능

``` javascript
function Person(name,age) {
  this.name = name;
  this.age = age;
}

var obj = new Person("aaa", 10);
var obj2 = new Person("bbb", 20);

Person.prototype.getName = function() { // 생성자의 prototype에 함수 getName() 추가
    return this.name;
}

console.log(obj.getName()); // "aaa"
console.log(obj2.getName()); // "bbb"
```

<br>

##### 객체를 의미하는 this
- this는 기본적으로 함수를 호출하는 객체를 의미.상황에 따라 달라짐
- 전역범위에서 this는 window(브라우저)를 가리킨다.
<br>

- 함수 실행에서는 this는 전역객체

```javascript
function sum(){
     console.log(this == window)
}
sum(); // true
```
<br>

- 메서드는 참조하고 있는 객체가 this, 실행컨텍스트가 생성될 때 this는 바인딩된다(메서드 호출)

```javascript
var person = {
     lang: "kor",
     walk: function(){
          console.log(this == person);
     }
}

person.walk(); // true

var a = {
     name: "chanhee"
}

var b = {
     name: "test",
     foo: function() {
          console.log(this.name);
     }
}
a.foo = b.foo;
a.foo(); // chanhee, 메서드는 이때 호출되면서 this가 바인딩됨
b.foo(); // test
```
<br>
- 자기호출함수, 함수표현식은 this가 전역객체 : 컨텍스트(실행 환경) 상 참조 객체 this 바인딩

```javascript
(function(){
     console.log(this === window)
})(); // true, 자기호출함수는 생성하자마자 호출되는 함수를 말함

var var1 = 10;

var foo = function(){
     var var1 = 30;

     console.log(this === window);
     console.log(this.var1);
}

foo(); // true 10
```

<br>

##### call(), apply()로 this 대상 변경

-  call 함수 : 함수의 인수로 객체를 전달, 전달한 객체가 this로 설정됨, 그 뒤 인수는 함수에 필요한 파라미터

```javascript
function add(c, d){
     return this.a + this.b + c + d;
}

var obj = {
     a: 1,
     b: 2
}

console.log(add.call(obj, 3, 4)); // 10
```
- 인수로 전달받은 객체를 this로 사용할 수 있음
- 원래라면 위의 add 함수의 this는 전역객체지만, call 함수의 인수로 전달받은 객체를 명시적으로 this에 바인딩 후 함수 콜

<br>

- apply 함수 : call 함수와 인자로 전달하는 객체가 this 되는 것은 마찬가지, 그 뒤 apply 인수에는 배열을 넘김(인수가 많을 때)

```javascript
function foo(){
     console.log(this);
     console.log(foo.arguments);
}

var obj = {
     var1: "1",
     var2: "2"
};

foo.apply(obj, [1,2,3,4,5,6]);
// Object { var1: "1", var2: "2" }    
// [1, 2, 3, 4, 5, 6, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```
- this로 바인딩할 객체 뒤 배열은 함수의 arguments(유사 배열)로 전달 및 저장 : arguments로 활용가능

<br>

##### 객체의 속성에 접근제한하기
- 생성자의 변수를 지역변수로 선언하며 접근을 제한, 대신 getter를 제공

``` javascript
function Person(name, age) {
  var name = name; // 지역변수로 선언
  var age = age; // 지역 변수로 선언

  this.getName = function() {
    return name;
  }
  this.getAge = function () {
    return age;
  }
}
  var p = new Person("aaa", 10);

  console.log(p.getName()); // "aaa"
  console.log(p.getAge()); // 10
  console.log(p.name); // undefined. 지역변수 접근X
  console.log(p.age); // undefined. 지역변수 접근 X
}
```

##### 상속
- Person객체와 Manager객체를 조상자손관계로 맺어주는 방법

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

function Manager(dept, name, age) {
  Person.call(this, name, age); // 조상 생성자를 호출
  this.dept = dept;
}

var m = new Manager('Sales', 'aaa', 12);

console.log(m.age); // 12
console.log(m instanceof Person); // false

//Manager.prototype = Person.prototype;
Manager.prototype.__proto__ = Person.prototype;

var m2 = new Manager('Sales', 'bbb', 12);
console.log(m2 instanceof Person); // true
```
