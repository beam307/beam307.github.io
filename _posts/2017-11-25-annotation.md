---
layout: post
title: '[Spring] MVC 애너테이션 핵심 정리와 최신 사용 패턴'
author: chanhee.kim
date: 2017-11-25 09:37
updated_at: 2026-02-16
tags: [java, spring, backend]
image: /files/covers/cover-code-02.jpg
description: "Spring MVC에서 자주 쓰는 애너테이션을 목적별로 정리하고, 2026 기준 권장 사용 패턴과 주의점을 함께 정리합니다."
keywords: "MVC 애너테이션 핵심 정리와 최신 사용 패턴, java, spring, 백엔드, 개발 블로그"
---

## 1. 컨트롤러와 매핑

```text
- @Controller : 스프링 MVC 컨트롤러(클래스)
- @RestController : @Controller + @ResponseBody 조합(클래스)
- @RequestMapping : 특정 URI/메서드 매핑(클래스, 메서드)
- @GetMapping/@PostMapping/@PutMapping/@DeleteMapping : HTTP 메서드 전용 매핑(메서드)
```

## 2. 요청 파라미터/헤더/경로

```text
- @RequestParam : 쿼리/폼 파라미터 바인딩(파라미터)
- @RequestHeader : HTTP 헤더 추출(파라미터)
- @PathVariable : 경로 변수 추출(파라미터)
- @CookieValue : 쿠키 값 추출(파라미터)
```

## 3. 모델/세션/바인딩

```text
- @ModelAttribute : 모델 바인딩 및 뷰 전달(메서드, 파라미터)
- @SessionAttributes : 세션 모델 유지(클래스)
- @InitBinder : 바인딩 커스터마이징(메서드)
```

## 4. 본문/응답

```text
- @RequestBody : 요청 본문(JSON 등) 바인딩(파라미터)
- @ResponseBody : 응답 본문으로 직렬화(메서드)
```

## 5. 계층 구분

```text
- @Repository : DAO/레포지토리 계층(클래스)
- @Service : 서비스 계층(클래스)
```
