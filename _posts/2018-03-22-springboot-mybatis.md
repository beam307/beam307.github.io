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
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis1.PNG" width="606" alt="mybatis1"><br>

2. Next를 눌러서 JDBC, Lombok, MyBatis, MySQL, Web을 메이븐을 추가한다. <br>
<img src="{{ site.baseurl }}/assets/images/mybatis/mybatis2.PNG" width="606" alt="mybatis2"><br>

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
