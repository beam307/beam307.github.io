---
layout: post
title: '[디자인패턴1] singleton(싱글톤), strategy(전략), Factory Method(팩토리 메소드) 패턴'
author: chanhee.kim
date: 2020-05-19 13:06
tags: [etc]
image: /files/covers/blog.jpg
---

#### Singleton Pattern(싱글톤 패턴) 이란?

: 특정 객체 하나만 존재 해야할때 쓰는 전략

- 싱글톤 패턴을 사용하는 이유
  - new 를 통해 객체 를 한번만 생성하므로 메모리 낭비 방지
  - 다른 클래스의 인스턴스들이 데이터 공유하기 쉽다.
  - DBCP처럼 공통된 객체를 여러개 생성해서 사용하는 경우 사용

- 주로 쓰는 곳
  - 커넥션풀, 쓰레드풀, 캐시, 로그 객체 등

- 문제점
  - 멀티쓰레드 환경에서 동기화처리해야함
  - 너무 싱글톤에 의존하여 많이 사용하면 코드가 복잡해질수록 유지보수가 힘들어짐

```java
public class Singleton implements Serializable {

  private static Singleton instance;

  private Singleton() {
  }

  public static Singleton getInstance() {
    if (instance == null) {
      instance = new Singleton();
    }
    return instance;
  }

}
```

#### Strategy Pattern(전략 패턴) 이란?

: 여러 알고리즘을 하나의 추상적인 접근점(인터페이스)을 만들어 접근점에서 알고리즘이 서로 교환 가능하도록 하는 패턴

- 전략패턴은 언제 사용해야 되는가?
  - 행동들이 조금씩 다르고 개념적으로 관련된 많은 클래스들이 존재할 때
  - 하나의 클래스가 많은 행동을 정의하고 이런 행동들이 클래스 안에서 다중 조건문의 모습을 취할 때

- 장점
  - 코드 중복 방지
  - 쉽게 확장 가능 (OCP 원칙 준수)
  - 독립적이므로 결합도가 느슨해진다.

- 단점
  - 객체가 무한정 늘어나 복잡
  - 오히려 코드가 늘어나 익숙하지 않으면 필요성 못느낌

#### 전략 패턴이 적용되지 않은 코드
 - 할인정책이 추가될경우 if-else 분기를 계속 추가해줘야함
 - 이는 하나의 메소드에서 할인정책 확인, 물건 가격 확인 등 여러가지 일을 하게 된다.

```java
public class Calculator {

  public double calculate(boolean isFirstGuest, boolean isLastGuest, List<Item> items) {
    double sum = 0;
    for (Item item : items) {
      if (isFirstGuest) {
        sum += item.getPrice() * 0.9;
      } else if (!item.isFresh()) {
        sum += item.getPrice() * 0.8;
      } else if (isFirstGuest) {
        sum += item.getPrice() * 0.8;
      } else {
        sum += item.getPrice();
      }
    }
    return sum;
  }
}

public class Item {
  private final String name;
  private final int price;

  public Item(String name, int price) {
    this.name = name;
    this.price = price;
  }

  public int getPrice() {
    return price;
  }

  public int getName() {
    return name;
  }
  
  public boolean isFresh() {
    return true;
  }
}
```

#### 전략패턴이 적용된 코드
 - 할인전략을 DiscountPolicy 인터페이스로 분리

```java
    public interface DiscountPolicy {
  double calculateWithDisCountRate(Item item);
}

public class FirstCustomerDiscount implements DiscountPolicy {
  @Override
  public double calculateWithDisCountRate(Item item) {
    return item.getPrice() * 0.9;
  }
}

public class LastCustomerDiscount implements DiscountPolicy {
  @Override
  public double calculateWithDisCountRate(Item item) {
    return item.getPrice() * 0.8;
  }
}

public class UnFreshFruitDiscount implements DiscountPolicy {
  @Override
  public double calculateWithDisCountRate(Item item) {
    return item.getPrice() * 0.8;
  }
}
```

- 그리고 Calculator 클래스에서 필요한 할인정책을 주입받아 사용

```java
public class Calculator {

  private final DiscountPolicy discountPolicy;

  public Calculator(DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
  }

  public double calculate(List<Item> items) {
    double sum = 0;
    for (Item item : items) {
      sum += discountPolicy.calculateWithDisCountRate(item);
    }
    return sum;
  }
}
```

- 위의 Calculator 객체에 해당하는 할인전략 객체를 주입하여 처리

```java
    public class FruitController {
  public static void main(String[] args) {
    Calculator calculator = new Calculator(new FirstCustomerDiscount());
    calculator.calculate(Arrays.asList(
            new Item("Apple", 3000),
            new Item("Banana", 3000),
            new Item("Orange", 2000),
            new Item("Pitch", 4000)
    ));
  }
}
```
#### Factory Method(팩토리 메소드 패턴) 이란?

: 객체를 만드는 부분을 Sub class에 맡기는 패턴.

- 장점
  - 객체 간의 결합도를 낮춰 유지보수가 용이
  - 객체들을 한곳에서 관리

- 단점
  - 불필요하게 많은 클래스를 정의
  - 더 복잡해질 수 있음

- UI컴포넌트 라는 객체 를 상속받은 Button, Switch, Dropdown 객체가 있음

```java
abstract class Component {
  protected abstract String getCompName ();
  public Component () {
    System.out.println(this.getCompName() + " 생성");
  }
}

class Button extends Component {
  @Override
  protected String getCompName() { return "버튼"; }
}
class Switch extends Component {
  @Override
  protected String getCompName() { return "스위치"; }
}
class Dropdown extends Component {
  @Override
  protected String getCompName() { return "드랍다운"; }
}
```

- 사용 용도에 따라 객체 생성하는 팩토리객체

```java
class CompFactory {
  public Component getComp (Usage usage) {
    if (usage == Usage.PRESS) {
      return new Button();
    } else if (usage == Usage.TOGGLE) {
      return new Switch();
    } else {
      return new Dropdown();
    }
  }
}
```

- 미 적용시 component객체가 변경시 변경된곳을 일일히 찾아 서 수정해줘야함 하지만 팩토리패턴 적용시 CompFactory객체만 수정하여 적용 가능

```java
class Console {

  private CompFactory compFactory = new CompFactory();

  Component comp1;
  Component comp2;
  Component comp3;

  // 팩토리패턴 미적용
  void withoutFactory () {
    comp1 = new Button();
    comp2 = new Switch();
    comp3 = new Dropdown();
  }

  // 팩토리패턴 적용
  void withFactory () {
    comp1 = compFactory.getComp(Usage.PRESS);
    comp2 = compFactory.getComp(Usage.TOGGLE);
    comp3 = compFactory.getComp(Usage.EXPAND);
  }
}

enum Usage {
  PRESS, TOGGLE, EXPAND
}
```


##### 참고
- [블로그1](https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80#%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4-%EC%A0%81%EC%9A%A9%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%80-%EC%BD%94%EB%93%9C) 참고
- [블로그2](https://scorpio-mercury.tistory.com/18) 참고
- [얄팍한 코딩사전](https://www.yalco.kr/29_oodp_1/) 참고
