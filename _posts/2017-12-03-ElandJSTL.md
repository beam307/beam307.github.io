---
layout: post
title: '[jsp] EL, JSTL 정리'
author: chanhee.kim
date: 2017-12-03 15:11
tags: [jsp-spring]
image: /files/covers/blog.jpg
---

JSP파일에 자바코드를 사용 할 경우 코드가 지저분할뿐만 아니라 후에 퍼블리셔들과 작업할 때 편리하다. <br>
EL 은 Expression Language 의 약자로 표현언어이며, <br>
JSTL은 Jsp Standard Tag Library 의 약자로 표준 태그라이브러리 이다. <br>


#### 1. EL
 - 사용목적 <br>
<%= %> 같은 자바코드를 jsp파일에서 사용하지않고 간단히 출력하는 하기위해 사용

 - 문법 <br>
  ex) <%= userVO.getName() %> == ${userVO.getName} <br>
  ex) <%= request.getAttribute("name") %> == ${requestScope.name} or ${name} <br>

  ※ 범위가 생략됐을 경우  attribute을 찾을때 page -> request -> session -> application 순으로 찾습니다.

  - EL 기본객체 <br>
    - pageScope : 페이지범위에서 접근
    - requestScope : request 범위에서 접근
    - sessionScope : 세션 범위에서 접근
    - applicationScope : 어플리케이션 범위에서 접근
    - param : 파라미터값 가져올때
    - paramValues : 파라미터값 배열로 받아올때
    - header : 헤더값 가져올때
    - headerValues : 헤더값 배열로 가져올때
    - Cookie : 쿠키값 조회
    - initParam : 초기 파라미터 조회
<br><br>
EL안에서는 비교연산자, 논리 연산자 , 수치 연산자, boolean, empty(null 체크) 등을 사용 가능하다
---

#### 2. JSTL

 - JSTL이 제공하는 태그 정리
   | 라이브러리 | 주요기능 | 접두어 | URI |
   |:---:|:---:|:---:|:---:|
   |코어|변수지원, 흐름제어, URL처리|prefix : c| http://java.sun.com/jsp/jstl/core |
   |XML|XML 처리|prefix : x| http://java.sun.com/jsp/jstl/xml |
   |국제화|다국어, 숫자및 날짜 형식| prefix : fmt| http://java.sun.com/jsp/jstl/fmt |
   |함수|컬렉션, String 처리|prefix : fn| http://java.sun.com/jsp/jstl/functions |
   |데이터베이스|SQL|prefix : sql| http://java.sun.com/jsp/jstl/sql |

 - 코어 태그
   - <c:set var="num" value="100"> : 변수 설정
   - <c:if test="조건식" /> : 단일 if문
   - <c:choose> <c:when> <c:otherwise> : if, else if, else 문
   - <c:foreach var="변수" items="배열,컬렉션" /> : 반복문
   - <c:out value="출력값"> : 출력
   - <c:url value="URL"> <c:param name="이름" value="값" /> : URL 생성해주며 파라미터 전달이 가능하다

   <br>

   사실 이외에도 엄청 많으나 코어태그 외에는 가끔 쓰이기때문에 그때 찾아보면 될 것 같다.
