---
layout: post
title: 'spring 애너테이션 정리'
author: chanhee.kim
date: 2017-11-25 09:37
tags: [jsp-spring]
image: /files/covers/blog.jpg
---

#### 애너테이션정리

```
- @Controller : 스프링MVC의 컨트롤 객체를 가르키는 애너테이션(클래스)
- @RequestMapping : 특정URI에 매칭되는 클래스나 메소드를 명시(클래스,메서드)
- @RequestParam : request에서 특정한 파라미터 값을 찾아낼때 사용(파라미터)
- @RequestHeader : request에서 특정 HTTPHeader정보 추출 할때 사용(파라미터)
- @PathVariable : 현재의 URI에서 넘겨진 파라미터값 추출할때 사용(파라미터)
- @CookieValue : 쿠키가 존재할 경우 쿠키이름을 이용해 쿠키값 출력(파라미터)
- @ModelAttribute : 자동으로 객체를 뷰까지 전달하도록 만듦(메서드,파라미터)
- @SessionAttribute : 세션상에서 모델의정보를 유지하고 싶은 경우에 사용(클래스)
- @InitBinder : 파라미터를 수집해서 객체를 만들 경우에 커스터마이징(메소드)
- @ResponseBody : 리턴타입이Http의 응답메시지로 전송 (메소드,리턴타입)
- @RequestBody : request문자열이 그대로 파라미터로 전달(파라미터)
- @Repository : DAO 객체(클래스)
- @Service : 서비스객체(클래스)
```
