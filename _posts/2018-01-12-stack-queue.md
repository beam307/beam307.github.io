---
layout: post
title: '[java] 컬렉션 프레임워크 - Stack & Queue'
author: chanhee.kim
date: 2018-01-12 15:47
tags: [java]
image: /files/covers/blog.jpg
---

##### 스택과 큐

- 스택(Stack) : LIFO구조. 마지막에 저장된것을 제일먼저 꺼내게 된다.
  - 수식계산, 수식괄호검사, undo/redo, 뒤로/앞으로(웹브라우저)

- 큐(Queue) : FIFO구조. 제일 먼저 저장한 것을 제일 먼저 꺼내게 된다.
  - 최근 사용문서, 인쇄작업대기목록, 버퍼(buffer)

<img src="{{ site.baseurl }}/assets/images/stack_queue.png" alt="stack_queue">


##### Enumeration, Iterator, ListIterator
- 컬렉션 클래스에 저장된 데이터를 접근하는데 사용되는 인터페이스이다.
- Enumeration는 Iterator의 구버젼이다.
- Iterator의 접근성을 향상 시킨것이 ListIterator이다.(단방향 -> 양방향)

|메서드|설명|
|:---:|:---:|
|||
|||
|||
