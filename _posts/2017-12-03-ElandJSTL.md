---
layout: post
title: '[JSP] EL(표현 언어)와 JSTL(표준 태그 라이브러리) 핵심 정리'
author: chanhee.kim
date: 2017-12-03 15:11
updated_at: 2026-02-16
tags: [java, jsp, frontend]
image: /files/covers/blog.jpg
description: "JSP의 EL과 JSTL을 정리하고, 2026 기준 최신 템플릿 엔진과 비교합니다. 코드 간소화와 가독성 향상 팁을 제공합니다."
keywords: "EL(표현 언어)와 JSTL(표준 태그 라이브러리) 핵심 정리, java, jsp, 프론트엔드, 개발 블로그"
---

JSP파일에 자바코드를 사용 할 경우 코드가 지저분할뿐만 아니라 후에 퍼블리셔들과 작업할 때 편리하다. <br>
EL 은 Expression Language 의 약자로 표현언어이며, <br>
JSTL은 Jsp Standard Tag Library 의 약자로 표준 태그라이브러리 이다. <br>


## 1. EL (Expression Language)

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

## 2. JSTL (JSP Standard Tag Library)

 - JSTL이 제공하는 태그 정리 <br>

   | 라이브러리 | 주요기능 | 접두어 | URI |
   |:---:|:---:|:---:|:---:|
   |코어|변수지원, 흐름제어, URL처리|prefix : c| http://java.sun.com/jsp/jstl/core |
   |XML|XML 처리|prefix : x| http://java.sun.com/jsp/jstl/xml |
   |국제화|다국어, 숫자및 날짜 형식| prefix : fmt| http://java.sun.com/jsp/jstl/fmt |
   |함수|컬렉션, String 처리|prefix : fn| http://java.sun.com/jsp/jstl/functions |
   |데이터베이스|SQL|prefix : sql| http://java.sun.com/jsp/jstl/sql |

 - 코어 태그
   - \<c:set var="num" value="100"\> : 변수 설정
   - \<c:if test="조건식" \/\> : 단일 if문
   - \<c:choose\> \<c:when\> \<c:otherwise\> : if, else if, else 문
   - \<c:foreach var="변수" items="배열,컬렉션" \/\> : 반복문
   - \<c:out value="출력값"\> : 출력
   - \<c:url value="URL"\> \<c:param name="이름" value="값" \/\> : URL 생성해주며 파라미터 전달이 가능하다
   <br>

   사실 이외에도 엄청 많으나 코어태그 외에는 가끔 쓰이기때문에 그때 찾아보면 될 것 같다.

---

## 3. 현재(2026) 기준 참고 사항

### 최신 템플릿 엔진 비교

#### Thymeleaf (Spring 표준)
```html
<p th:text="${user.name}">이름</p>
<ul>
  <li th:each="item : ${items}" th:text="${item}"></li>
</ul>
<span th:if="${user.isActive}">활성</span>
```

#### JSX (React)
```jsx
<p>{user.name}</p>
<ul>
  {items.map(item => <li key={item.id}>{item}</li>)}
</ul>
{user.isActive && <span>활성</span>}
```

### JSP/EL/JSTL vs 최신 방식
- JSP: 서버 렌더링, Java EE 환경 종속, 디자이너 협업 어려움
- Thymeleaf: 자연스러운 HTML, Spring Boot 통합, 정적 프로토타입 가능
- React/JSX: 컴포넌트 재사용, 타입 안정성(TypeScript), 클라이언트/서버 하이브리드
- TODO: 프로젝트 특성(레거시 유지/신규 개발)에 맞춘 선택 기준 수립 필요
