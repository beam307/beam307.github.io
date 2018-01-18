---
layout: post
title: 'Spring 기본적인 Controller 생성'
author: chanhee.kim
date: 2017-12-02 13:07
tags: [jsp-spring]
image: /files/covers/blog.jpg
---

#### 1. void 리턴 타입의 경우

``` java
@Controller
public class SampleController {

  @RequestMapping("/doA")
  public void doA() {

  }

  @RequestMapping("doB")
  public void doB() {

  }
}
```

현재 메소드의 리턴타입이 void 인 경우에는 스프링MVC는 현재 경로에 해당하는 JSP 파일 실행 <br>
위의 경우 /WEB-INF/views/doA.jsp, /WEB-INF/views/doB.jsp 를 실행하게 된다.

---

#### 2. String이 리턴 타입인 경우

``` java
@Controller
public class SampleController {

  @RequestMapping("/doC")
  public String doC() {
    return "result";
  }
}
```

리턴값 + .jsp 파일을찾아서 실행한다.
위의 경우 /WEB-INF/views/result.jsp 파일을 실행한다.

---

#### 3. 데이터 전달 하는 방법

``` java
@Controller
public class SampleController {

  @RequestMapping("/doD")
  public String doD() {

    UserVO userVO = new UserVO();

    model.addAttribute(userVO);

    return "result";
  }
}
```

model.addAttribute를 통해서 뷰까지 객체등을 전달 할 수 있다.
- addAttribute("이름", 값) : Map 형식으로 이름으로 값을 부를 수 있다.
- addAttribute(값) : 자동으로 이름 지정, 저장되는 객체의 클래스명 앞 글자를 소문자로 처리한 클래스명을 이름으로 간주

---

#### 4. 리다이렉트하는 경우

```java
@Controller
public class SampleController {

  @RequestMapping("/doE")
  public String doE(RedirectAttributes rttr) {

    rttr.addFlashAttribute("msg", "test");
    return "redirect:/doF";
  }

  @RequestMapping("/doF")
  public void doF(@ModelAttribute String msg) {
    System.out.println("doE로 넘어온 msg 출력 : " + msg);
  }
}
```

다시호출 하는 경우는 redirect: 을 이용하며 임시 파라미터는 RedirectAttributes를 사용가능하다<br> RedirectAttributes는 임시로 데이터를 추가하여 넘길 수 있다.

---

#### 5. JSON 데이터를 이용하는 경우

JSON 데이터를 사용할 경우에는 아래와 같이 pom.xml 에 jackson-databind 를 추가해야한다.
```
<!-- jackson-databind -->
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.8.4</version>
</dependency>
```

```java
@Controller
public class SampleController {

  @ResponseBody
  @RequestMapping("/doE")
  public UserVO doJSON {

    UserVO userVO = new UserVO();

    return userVO;
  }
}
```

@ResponseBody 애너테이션만 추가해주면 자동으로 JSON 처리를 한다. <br>
리턴되는 값은 View 를 통해서 출력되지 않고 HTTP Response Body 에 직접 쓰여지게 된다.<br>
이때 쓰여지기 전에 리턴되는 데이터타입에 따라 MessageConverter에 의해 변환이 이뤄지고 쓰여진다. <br>
주로 JSON 반환할때 사용된다.
