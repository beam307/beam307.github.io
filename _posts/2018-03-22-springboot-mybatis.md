---
layout: post
title: '[spring] spring boot - mybatis 연동'
author: chanhee.kim
date: 2018-03-22 14:33
tags: [spring]
image: /files/covers/blog.jpg
---

##### mybatis 연동하기

1. 이클립스(STS)에서 New -> Spring Starter Project 를 클릭 한 후 아래와 같이 창을뜨면 Name을 설정한다. <br>
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis1.PNG" width="606" alt="mybatis1"><br><br>

2. Next를 눌러서 JDBC, Lombok, MyBatis, MySQL, Web을 메이븐을 추가한다. <br>
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis2.PNG" width="606" alt="mybatis2"><br><br>

3. 설정이 완료되면 pom.xml에 들어가 아래와 같은 디펜던시 추가 <br>
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis3.PNG" width="606" alt="mybatis3"><br>
 - jstl : jsp를 쓰기 위한 라이브러리
 - mybatis : mybatis 라이브러리
 - mybatis-spring : spring jdbc연결과 관련된 라이브러리

 4. application.properties 수정

 ```
 spring.mvc.view.prefix=/WEB-INF/view/
 spring.mvc.view.suffix=.jsp
 spring.datasource.driver-class-name= com.mysql.jdbc.Driver
 spring.datasource.url=jdbc:mysql://localhost:3306/DB명?autoReconnect=true&useSSL=false&useUnicode=true&characterEncoding=utf8
 spring.datasource.username=아이디
 spring.datasource.password=비밀번호
 ```
 
<br>
5. Mysql DB 테이블 생성, 칼럼 추가 및 데이터 삽입<br>
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis4.PNG" alt="mybatis4"><br>

6. TestVO 생성

``` java
@Data
public class TestVO {
	String id;
	String name;
	String phone;
}
```

7. src/main/resources 에 mapper폴더 추가 및 Mapper.xml 파일 생성

``` xml
<mapper namespace="com.example.mapper.Mapper">
	<select id="test" resultType="java.util.HashMap">
		select * from user
	</select>
</mapper>
```

8. Mapper.java 생성

```java
public interface Mapper {
	public List test() throws Exception;
}
```

9. Test(처음설정한 이름명)Application.java 수정

```java
@SpringBootApplication
@ComponentScan(basePackages = "com.example.controller") //컨트롤러패키지안 파일들 빈등록 설정
@MapperScan(value={"com.example.mapper"}) // 매퍼 인터페이스 파일이 있는 가장 상위 패키지를 지정하면 된다.
                                         // 세미콜론이나 콤마를 구분자로 사용해서 한개 이상의 패키지를 셋팅할 수 있다. 매퍼는 지정된 패키지에서 재귀적으로 하위 패키지를 모두 검색할 것이다.
public class TestApplication {

	public static void main(String[] args) {
		SpringApplication.run(TestApplication.class, args);
	}

	/*
	 * sqlSessionFactory 설정
	 */
	@Bean
	public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
		SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
		sessionFactory.setDataSource(dataSource);

		Resource[] res = new PathMatchingResourcePatternResolver().getResources("classpath:mapper/*Mapper.xml"); // 매퍼xml 클래스패스 지정
		sessionFactory.setMapperLocations(res);

		return sessionFactory.getObject();
	}
}
```

10. Testcontroller.java 파일 생성

```java
@RestController
public class TestController {

	@Autowired // 매퍼 빈 자동 주입
	Mapper mapper;

  @RequestMapping("/")
	public ModelAndView mainView() throws Exception {
		ModelAndView mav = new ModelAndView("main"); // 뷰이름

		mav.addObject("resultList", mapper.test());
		System.out.println(mapper.test());
		return mav;

	}
}
```

11. amin.jsp 생성

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
mainpage입니다 <br>
${resultList}
</body>
</html>
```

12. Package Explorer <br>
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis5.PNG" alt="mybatis5"><br>
