---
layout: post
title: '[Spring Boot] 기본 개념과 Spring MVC 비교'
author: chanhee.kim
date: 2018-03-21 13:26
updated_at: 2026-02-16
tags: [spring, spring-boot, configuration, bean, ioc, di, annotation]
image: /files/covers/blog.jpg
description: "Spring Boot의 기본 개념과 Bean/IoC/DI를 정리하고, 2026 기준 Spring Boot 3.x 설정과 권장 사항을 함께 설명합니다."
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

##### Bean이란?
- Spring Container에서 관리되는 객체

##### @SpringBootApplication
- @Configuration, @enableAutoConfiguration, @ComponentScan을 디폴트 속성으로 함께 사용하는것과 같다

##### @Configuration
- 스프링에서 Bean 설정을 위한 객체로 인식하게 되고, 그내부에 @Bean으로 Bean객체를 정의한다.

##### @bean
- Method에 정의하는데 메소드가 반환하는 객체가 바로 Bean 객체이다. spring Container에 딱 한 번 등록되어 사용한다. (싱글톤)

##### @Configuration, @Component 차이점
- @Configuration은 Bean을 생성 할때 싱클톤으로 한번만 생성하고 @Compoent는 Bean을 생성 할때 java에서 new 생성하듯이 생성한다.

##### 다음 4가지 Annotation으로 Bean형식을 설정한다.
- @Component : @CompnentScan을 사용하여 package를 지정해주면 그 하위에 존재하는 @Component로 정의된 클래스들을 검색하여 Bean으로 등록시킨다.
- 다음 3가지 Annotation은 더 세부적인 관리를 위해 약간의 기능을 추가하여 @Component를 확한한 것이다.
- @Controller, @Service, @Repository

##### @Autowired vs @Resource
 - @Autowired : 비표준, 스프링용, Type우선으로 Bean검색, @Qualifier("")을 사용하여 지정 가능
 - @Resource : Java표준, ID우선으로 Bean검색, Name속성으로 지정가능
