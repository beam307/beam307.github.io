---
layout: post
title: '[Spring] @RestController와 REST API 설계 핵심 정리'
author: chanhee.kim
date: 2017-12-06 09:03
updated_at: 2026-02-16
tags: [spring, rest-api, restcontroller, json, http, api-design]
image: /files/covers/blog.jpg
description: "Spring의 @RestController로 REST API를 구현하는 방법을 정리하고, 2026 기준 ResponseEntity와 API 설계 권장 패턴을 함께 제공합니다."
---

## 1. REST란?
REST는'Representational State Transfer'의 약어로 하나의 URI는 하나의 고유한 리소스를 대표하도록 설계된다는 개념입니다.<br>

스프링4버전부터 지원되는 '@RestController' 애노테이션의경우 기존과 같이 JSP같은 뷰를 만드는것이아니라 REST방식의데이터 처리를 위해서 사용하는 애너테이션입니다.

## 2. RestController

### @RestController의 용도 <br>

JSP와 같은 뷰를 만들어 내지 않는 대신에 데이터 자체를 반환하는데, 이때주로 사용되는 것은 단순 문자열과 JSON, XML 등으로 나누어 볼 수 있습니다.

### 단순 문자열의 경우 <br>

문자열 데이터는 기본적으로 브라우저에는 'text/html' 타입으로 처리됩니다.

``` java
@RequestController
@RequestMapping("/sample")
public class SampleController {
    @RequestMapping("/hello")
    public String sayHello() {
      return "hello World";
    }
}

```

클래스에 @RestController 애너테이션을 쓰면 뷰처리가 JSP가 아니라는것을 의미한다. 모든 메소드는 @ResponseBody가 없어도 동일하게 작동 된다. ※ 리턴값이 JSP경로가 아닌 일반 문자열이다.

### 객체를 JSON으로 반환 하는경우

```java
public class UserVO {
  private Integer no;
  private String Name;
  private String Nick;

  ...이하 getter/setter, toString() 생략
}

@RequestMapping("/sendVO")
public UserVO sendVO() {
    UserVO vo = enw UserVO();
    vo.setNo(123);
    vo.setName("홍길동");
    vo.setNick("성남아방이");
}
```

pom.xml에 Jackson-databind 메이븐을 추가 하면 객체를 자동으로 JSON으로 변환한다.

```
<!-- jackson-databind -->
<dependency>
  	<groupId>com.fasterxml.jackson.core</groupId>
  	<artifactId>jackson-databind</artifactId>
  	<version>2.8.4</version>
</dependency>
```

### 컬렉션 타입의 객체를 반환하는 경우

``` java
@RequestMapping("sendList")
public List<UserVO> sendList() {
    List<UserVO> list new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        UserVO vo = new UserVO();
        vo.setNo(123);
        vo.setName("홍길동");
        vo.setNick("성남아방이");
        list.add(vo);
    }
    return list;
}

@RequestMapping("sendMap")
public Map<Integer, UserVO> sendMap() {
    Map<Integer, UserVO> map = new HashMap<>();

    for (int i = 0; i < 10; i++) {
        UserVO vo = new UserVO();
        vo.setNo(i);
        vo.setName("홍길동");
        vo.setNick("성남아방이");
        map.put(i, vo);
    }
    return map;
}
```
JSON의 문법상 리스트는 배열로 표현되기때문에 결과는 [{"no" : 0, "Name" : "홍길동", "Nick" : "성남아방이"} ... ] 으로 전달된다.

Map의 경우는 ["0" :{"no" : 0, "Name" : "홍길동", "Nick" : "성남아방이"}, ... ] 으로 전달된다.


### ResponseEntity 타입
스프링에서 제공하는 ResponseEntity 타입은 개발자가 직접 결과 데이터 + HTTP의 상태코드를 직접 제어 할 수 있는 클래스이다. 개발자는 HTTP 상태 코드를 전송하고 싶은 데이터와 함께 전송할 수 있기때문에 좀 더 세밀한 제어가 필요한 경우에 사용해 볼 수 있다.

---

## 3. 현재(2026) 기준 권장 방식

### ResponseEntity 활용 (권장)
```java
@RestController
@RequestMapping("/api/users")
public class UserApiController {
  
  @GetMapping("/{id}")
  public ResponseEntity<UserVO> getUser(@PathVariable Long id) {
    UserVO user = userService.findById(id);
    if (user == null) {
      return ResponseEntity.notFound().build();
    }
    return ResponseEntity.ok(user);
  }

  @PostMapping
  public ResponseEntity<UserVO> createUser(@RequestBody @Valid UserVO user) {
    UserVO created = userService.create(user);
    URI location = ServletUriComponentsBuilder
      .fromCurrentRequest()
      .path("/{id}")
      .buildAndExpand(created.getNo())
      .toUri();
    return ResponseEntity.created(location).body(created);
  }

  @PutMapping("/{id}")
  public ResponseEntity<UserVO> updateUser(@PathVariable Long id, @RequestBody @Valid UserVO user) {
    UserVO updated = userService.update(id, user);
    return ResponseEntity.ok(updated);
  }

  @DeleteMapping("/{id}")
  public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.delete(id);
    return ResponseEntity.noContent().build();
  }
}
```

### 통합 예외 처리
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
  
  @ExceptionHandler(ResourceNotFoundException.class)
  public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
    ErrorResponse error = new ErrorResponse("NOT_FOUND", ex.getMessage());
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
  }

  @ExceptionHandler(MethodArgumentNotValidException.class)
  public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
    ErrorResponse error = new ErrorResponse("VALIDATION_ERROR", ex.getMessage());
    return ResponseEntity.badRequest().body(error);
  }
}
```
