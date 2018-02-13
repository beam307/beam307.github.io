---
layout: post
title: '[java] 컬렉션 프레임워크'
author: chanhee.kim
date: 2018-01-02 10:54
tags: [java]
image: /files/covers/blog.jpg
---

##### 컬렉션프레임워크란??

- 데이터 군을 저장하는 클래스들을 표준화한 설계
- 다수의 데이터를 쉽게 처리할 수 있는 방법을 제공하는 클래스들로 구성
- JDK 1.2부터 제공

##### 컬렉션 프레임워크의 핵심 인터페이스

<table>
	<tr>
		<td>인터페이스</td>
		<td>특징</td>
	</tr>
	<tr>
		<td rowspan="2">List</td>
		<td>순서가 있는 데이터 집합. 데이터 중복 허용</td>
	</tr>
	<tr>
		<td>구현클래스 : ArrayList, LinkedList, Stack, Vector 등</td>
	</tr>
	<tr>
		<td rowspan="2">Set</td>
		<td>순서를 유지하지않는 데이터 집합, 데이터중복 허용 X</td>
	</tr>
	<tr>
		<td>HashSet, TreeSet 등</td>
	</tr>
	<tr>
		<td rowspan="2">Map</td>
		<td>키(key)와 값(value)의 쌍으로 이루어진 데이터 집합</td>
	</tr>
	<tr>
		<td>구현클래스 : HashMap, TreeMap 등</td>
	</tr>
</table>
