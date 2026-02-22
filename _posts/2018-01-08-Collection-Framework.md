---
layout: post
title: '[Java] 컬렉션 프레임워크: List/Set/Map 핵심 개념'
author: chanhee.kim
date: 2018-01-08 09:03
updated_at: 2026-02-16
tags: [java, data-structure]
image: /files/covers/cover-dev-10.jpg
description: "Java 컬렉션 프레임워크의 List/Set/Map 인터페이스와 구현 클래스를 정리하고, 2026 기준 Stream API 활용법을 함께 설명합니다."
keywords: "컬렉션 프레임워크: List/Set/Map 핵심 개념, java, 자료구조, 개발 블로그"
---

##### 컬렉션프레임워크란??

- 데이터 군을 저장하는 클래스들을 표준화한 설계
- 다수의 데이터를 쉽게 처리할 수 있는 방법을 제공하는 클래스들로 구성
- JDK 1.2부터 제공

##### 컬렉션 프레임워크의 핵심 인터페이스

<img src="{{ site.baseurl }}/assets/images/collection/collection.png" alt="collection">

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

##### 컬렉션 프레임워크의 동기화(synchronization)

- 멀티쓰레드프로그래밍에서는 컬렉션 클래스에 동기화 처리가 필요하다.
- Vector와 같은 구버젼 클래스들은 자체적으로 동기화 처리가 되어 있다.
- ArrayList와 같은 신버젼 클래스들은 별도의 동기화 처리가 필요하다.
