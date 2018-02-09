---
layout: post
title: '[jsp] 페이지 모듈화'
author: chanhee.kim
date: 2017-12-01 10:37
tags: [jsp]
image: /files/covers/blog.jpg
---

#### 1. <jsp:include> 액션태그

```
<jsp:inlude page="페이지" flush="true" />
```

page : 포함할 JSP페이지 경로 지정 <br>
flush : 지정한 JSP페이지 실행하기전에 출력버퍼를 플러시할지 여부 지정 true이면 출력버퍼를 플러시하고 false 는 플러시하지않는다. 기본값은 false 이다.

주로 화면 레이아웃 구성할때 주로 사용된다. (header, footer, side 등)

---

#### 2. include 디렉티브

```
<%@include file="페이지" %>
```

file : 포함할 파일 경로 지정 <br>

include 디렉티브를 사용하면, JSP파일을 자바파일로 변환하기 전에 include 디렉티브에서 지정한 파일의 내용을 해당위치에 삽입하고 그 결과로 생긴 자바 파일을 컴파일 한다. <br>

주로 아래 두가지 목적으로 사용된다.
 - 모든 JSP 페이지에서 사용하는 변수 지정
 - 저작권 표시와 같이 모든페이지에서 중복되는 간단한 문장

---

#### 3. <jsp:include> 액션태그 와 include 디렉티브의 비교

 | 비교항목 | <jsp:include> 액션태그 | include 디렉티브|
 |:---:|:---:|:---:|
 |처리시간|요청시간에 처리|JSP파일을 자바소스에 변환할때 처리|
 |기능|별도의파일로 요청처리 흐름 이동|현재 파일에 삽입 시킴|
 |데이터 전달방법|request 기본객체나 <jsp:param>을 <br> 이용한 파라미터 전달| 페이지내의 변수를 선언한 후, 변수에 값 저장|
 |용도|화면의 레이아웃|공통으로 사용되는 변수를 지정하는 코드나 <br>저작권 같은 문장을 포함|
