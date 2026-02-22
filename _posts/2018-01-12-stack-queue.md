---
layout: post
title: '[Java] 컬렉션 프레임워크 - Stack & Queue'
author: chanhee.kim
date: 2018-01-12 15:47
updated_at: 2026-02-16
tags: [java, data-structure]
image: /files/covers/cover-dev-10.jpg
description: "Java Stack과 Queue 자료구조의 특징과 사용법을 정리합니다."
keywords: "컬렉션 프레임워크 - Stack & Queue, java, 자료구조, 개발 블로그"
---

##### 스택과 큐

- 스택(Stack) : LIFO구조. 마지막에 저장된것을 제일먼저 꺼내게 된다.
  - 수식계산, 수식괄호검사, undo/redo, 뒤로/앞으로(웹브라우저)

- 큐(Queue) : FIFO구조. 제일 먼저 저장한 것을 제일 먼저 꺼내게 된다.
  - 최근 사용문서, 인쇄작업대기목록, 버퍼(buffer)

<img src="{{ site.baseurl }}/assets/images/collection/stack_queue.png" alt="stack_queue">


##### Enumeration, Iterator, ListIterator
- 컬렉션 클래스에 저장된 데이터를 접근하는데 사용되는 인터페이스이다.
- Enumeration는 Iterator의 구버젼이다.
- Iterator의 접근성을 향상 시킨것이 ListIterator이다.(단방향 -> 양방향)

Iterator인터페이스의 메서드

|메서드|설명|
|:---:|:---:|
|boolean hasNext()|읽어 올 요소가 남아있는지 확인한다. 있으면 true, 없으면 false 반환|
|Object next()|다음 요소를 읽어 온다. next()를 호출하기 전에 haxNext()를 호출해서 읽어 올 요소가 있는지 확인하는 것이 안전하다.|
|void remove()|next()로 읽어 온 요소를 삭제한다. next()를 호출한 다음에 remove()를 호출해야한다.(선택적 기능)|

- 컬렉션 클래스에 저장된 요소들을 나열하는 방법을 제공
- 컬렉션 클래스의 iterator()를 호출해서 Iterator를 구현한 객체를 얻는다.

``` java
List list = new ArrayList();

Iterator it = list.iterator();
while(it.haxNext()) {
  System.out.println(it.next());
}
```

```java
Map map = new HashMap();

Iterator it = map.ketSet().iterator();
// 위 코드를 풀면 아래와 같다
// Set eSet = map.entrySet();
// Iterator List = eSet.iterator();
```
