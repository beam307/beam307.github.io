---
layout: post
title: '[javascript] 자바스크립트 개념'
author: chanhee.kim
date: 2018-01-29 09:21
tags: [javascript]
image: /files/covers/blog.jpg
---

##### HashSet - 중복허용X, 순서유지X
- Set인터페이스를 구현한 대표적인 컬렉션클래스(중복허용x, 순서유지x)
- 순서를 유지하고자 한다면 LinkedHashSet클래스를 사용해야 한다.

##### HashSet - boolean add(Object o)
- HashSet에 객체를 저장할 때 boolean add(Object o)를 사용하며, 기존에 저장된 객체와 중복된 객체를 저장하면 false를 반환한다.
- boolean add(Object o)는 저장할 객체의 equals()와 hashCode()를 호출하므로 저장할 객체의 equals()와 hashCode()가 적절히 오버라이딩 되어 있어야 한다. 그렇지 않으면 중복된 객체를 중복된 것으로 간주하지 않을 수 있다.

```java
class Person {
  String name;
  int age;

  Person(String name, int age) {
    this.name = name;
    this.age = age;
  }
  public String toString() {
    return name + ":" age;
  }
}
//아래와 같이 변경
class Person {
  String name;
  int age;

  Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  public boolean equals(Object obj) {
    if(obj instanceof Person) {
      Person tmp = (Person)obj;
      return name.equals(tmp.name) && age == temp.age;
    }
    return false;
  }

  public int hasCode() {
    return name.hashCode() + age;
  }

  public String toString() {
    return name + ":" age;
  }
}
```

##### HashSet - int hashCode()
- 동일 객체에 대해 hashCode()를 여러 번 호출해도 동일한 값을 반환해야 한다.
- equals()로 비교해서 true를 얻은 두 객체의 hashCode()값은 일치해야 한다.
- euqals()로 비교해서 false를 얻은 두 객체의 hashCode()값은 서로 다른 것이 보통이지만, 같아도 문제없다.
- 그러나 성능 향상을 위해 가능하면 서로 다른 값을 반환하도록 오버라이딩 해야한다.

##### TreeSet - 검색과 정렬에 유리
- Set인터페이스를 구현한 컬렉션 클래스.(중복허용X, 순서유지X, 정렬저장O)
- 이진검색트리의 구조로 되어있다.
- 링크드리스트와같이 각 요소(node)가 나무(tree)형태로 연결된 구조
- 모든트리는 하나의 루트(root node)를 가지며, 서로 연결된 두 요소를 '부모-자식관계'에 있다하고, 하나의 부모에 최대 두개의 자식을 갖는다.
- 왼쪽 자식의 값은 부모의 값보다 작은 값을, 오른쪽 자식의 값은 부모보다 큰 값을 저장한다.
- 검색과 정렬에 유리하지만, HashSet보다 데이터 추가, 삭제시간이 더 걸린다.

<img src="{{ site.baseurl }}/assets/images/treeset.png" alt="treeset">

##### Comparator와 Comparable
- 객체를 정렬하는데 필요한 메서드를 정의한 인터페이스이다.
  - Comparable - 기본정렬기준의 구현하는데 사용
  - Comparator - 기본 정렬기준 외에 다른 기준으로 정렬하고자할 때 사용

- 이 들에 정의된 compare(), compareTo()를 구현함으로써 정렬이 필요한 경우, 예를 들면 TreeSet이나 sort()를 사용할 때, 정렬기준을 제시하게 된다.

- compare()와 compareTo()는 이름과 매개변수의 수만 다를뿐, 두객체를 비교해서 같으면 0을 작으면 음수, 크면 양수를 반환한다는 것은 같다.**그리고 이 반환값을 통해 두객체의 정렬순서가 결정 된다.**
