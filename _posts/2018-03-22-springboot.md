---
layout: post
title: '[spring] spring boot'
author: chanhee.kim
date: 2018-03-22 13:26
tags: [spring]
image: /files/covers/blog.jpg
---

##### Spring Boot VS Spring(SpringMVC)
- Spring Boot는 spring프레임웤을 사용하는 프로젝트를 간편하게 셋업할 수 있는 스프링 프레임웤의 서브 프로젝트이다.
- 독립컨테이너에서 동작할 수 있기에 java만 설치되어 있으면 개발하기 한결 수월하다
- 또한 빌드 후에 jar파일이 생성되고, 별도의 서버 설치 없이 embeded tomcat이 자동으로 실행 된다.
- 프로젝트 생성시 복합한 설정이 아닌 통합된 설정파일인 application.yml으로 쉽게 간단히 사용할 수 있다.

##### 언제 Spring boot를 써야하나?
- Web기반인 어플리케이션은 tomcat이든 WAS든 WebContainer가 설치되어야 한다.
- 비교적 규모가 작은 어플리케이션을 실행시키기위해 그보다 큰 WAS를 따로 설치하기엔 효율적이지 않다.
- 이런경우 Spring Boot를 쓰는게 적당하다
- 규모가 큰 웹사이트경우 이런구조로 만드는 것보단 Spring MVC형태로 만들어 WAS에 배포하는 스타일이 낫다.
