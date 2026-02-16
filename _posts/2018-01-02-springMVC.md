---
layout: post
title: '[Spring] MVC 패턴과 아키텍처 핵심 개념 정리'
author: chanhee.kim
date: 2018-01-02 10:54
updated_at: 2026-02-16
tags: [spring, spring-mvc, mvc, architecture, ioc, di, design-pattern]
image: /files/covers/blog.jpg
description: "Spring MVC의 등장 배경과 IoC/DI 개념, DAO/Service/Controller 계층 구조를 정리하고, 2026 기준 Spring Boot 아키텍처와 비교합니다."
---

##### Spring의 등장 배경
Java는 처음 Enterprise용 소프트웨어를 목적으로 개발되었다. 그래서 자바로 만들어지는 소프트웨어들은 개인이 만드는 것 보다 크고 복잡했으며 또한 무거웠다. 이러한 자바 프로그램의 복잡도를 줄이고자 Spring 프레임워크가 등장하였다.

##### Spring
Spring은 Java 객체를 담고 있는 경량 컨테이너이다. 이들 Java 객체의 생성, 소멸과 같은 라이프 사이클을 관리하고 Spring으로 부터 필요한 객체를 가져와 사용 할 수 있다.

|기능|설명|
|:---:|:---:|
|IOC(Inversion of Control)|‘제어의 역전’으로서 기존에 어플리케이션이 프레임워크의 서비스를 콜하는 대신에 프레임워크가 어플리케이션의 각 컴포넌트들을 부르는 형태이다. 어플리케이션 그 자체에서 프레임워크로 넘어갔다고 하여 제어의 역전이라 한다.|
|DI(Dependency Injection)|IoC를 실제로 구현하는 방법으로서 의존성있는 커포넌트들 간의 관계를 개발자가 직접 코드로 명시하지 않고 컨테이너인 Spring이 런타임에 찾아서 연결해주게 하는 것이다.|

##### Spring MVC
Spring 의 여러 module 중에서 웹에도 기존 소프트웨어에서 사용되는 MVC 패턴을 적용해 웹 어플리케이션을 만들고자 등장한게 Spring MVC module 이다.
<br><br>
기존에 servlet 원래의 spec에 맞춰 servlet을 개발하려고 하면, doGet(), doPost() 등의 함수를 생성해 주는 등의 공통적이고 반복적인 작업을 많이 해야했다.
<br><br>
그리하여 개발자가 직접 모든 웹 컴포넌트를 부르는 대신 그것보다 상위의 framework 가 있어 이러한 컴포넌트들을 자동으로 불러주고, 반복적인 작업을 줄여주기위해 Spring MVC Module이 등장하였다. Spring MVC Module 로 만들어진 프로그램들은 그 Spring MVC Module 내부에 Servlet(Dispatcher Servlet)을 포함하고 개발시 이전에는 일일이 해야했던 공통된 부분을 따로 하지 않아도 자동으로 되었다. 즉, Spring MVC을 통해 만들어진 프로그램은 그 내부에 Servlet을 포함하고 그 자체가 Tomcat과 같은 Servlet Container 위에 올라가 동작하는 Servlet 역할을 한다.

##### MVC 코드 예제

``` java
public interface UserDAO {

	public void create(UserVO vo) throws Exception;

}
```

- DAO를 interface로 만드는 이유는 DB마다 공통된 인터페이스를 적용하고 유지 보수를 쉽게 하기 위해서이다.
- ###### DAO는 단일 데이터를 접근/갱신만 처리를 한다.(CRUD)

```java
@Repository
public class UserDAOImpl implements UserDAO {

	@Override
	public void create(UserVO vo) throws Exception { // 회원가입
		// 코드작성
  }

}
```

- @Repository : 이 클래스는 DAO를 구현한 클래스임을 나타내는 Annotation

```java
public interface UserService {

	public void create(UserVO vo) throws Exception;

}
```

- service를 interface로 만드는이유는 위와 동일

```java
@Service
public class UserServiceImpl implements UserService {

	@Inject
	private UserDAO userDAO;

	@Override
	public void create(UserVO vo) throws Exception { // 회원가입
    // 코드 작성
	}
}
```

- @Inject는 JSR에 실려있는 자바 기존의 어노테이션입니다. 반면 @Autowired의 경우에는 스프링에서 등장한 어노테이션입니다.
- ###### Service 는 여러 DAO를 사용자에 맞게 가공한다. 즉 Service 는 트랜잭션 단위이다.
- 결국 dao와 service는 비슷해보이지만 유지보수등을 편하기 위해 쓴다.

```java
@Controller
public class UserController {

	@Autowired
	private UserService userService;

  @RequestMapping(value = "/register", method = RequestMethod.POST) public String regUserPost() throws Exception { // 회원가입
    // 코드작성
  }
}
```
- @Autowired : 특정 클래스를 명시하지 않아도 Spring이 자동으로 NoticeDAO를 구현한 클래스를 연결해 준다.
- @RequestMapping : 통해 분기문 없이 특정 URL의 요청이 왔을 때 Spring이 자동으로 이 메소드를 호출해준다. 통상적으로 한 URL에 대해 RequestMapping된 메소드는 한 개.
- @Controller : 이클래스가 컨트롤러 임을 나타내는 Annotation

##### spring MVC 구조

<img src="{{ site.baseurl }}/assets/images/spring/mvc.jpg" alt="spring_mvc_module">
