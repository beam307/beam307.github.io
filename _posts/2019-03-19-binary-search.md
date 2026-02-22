---
layout: post
title: '[알고리즘] 이진 탐색 (Binary Search) 원리와 구현'
author: chanhee.kim
date: 2019-03-19 14:26
updated_at: 2026-02-16
tags: [algorithm, data-structure]
image: /files/covers/cover-dev-10.jpg
description: "이진 탐색(Binary Search) 알고리즘의 원리와 구현 방법을 정리합니다."
keywords: "이진 탐색 (Binary Search) 원리와 구현, 알고리즘, 자료구조, 개발 블로그"
---


#### 1. 이진탐색이란??

이진탐색알고리즘(binary search algorithm)은 정렬된 리스트에서 특정한 값을 찾는 알고리즘이다. 처음 중간의 값을 선택하여 그 값과 찾고하는 값과 비교하는 방식이다.이진검색은 분할정복 알고리즘 중 하나이다.

* 시간복잡도 : O(n*logN)
* 이진탐색 소스

``` java
public class Main {

	public static void main(String[] args) {
		// 정렬된 배열
		int[] arr = { 3, 4, 7, 13, 16, 29, 33, 35, 44, 50 };

		// console (3번째 인덱스 출력)
		// 2
		System.out.println(binarySearch(arr, 0, arr.length, 7));
	}

	public static int binarySearch(int[] arr, int left, int right, int key) {
		if (left > right) // 값이 없으면 -1 출력
			return -1;

		int mid = (left + right) / 2; // 중간 인덱스
		if (key < arr[mid]) // 키가 중간값보다 작으면 왼쪽부분 다시 재검색
			return binarySearch(arr, left, mid - 1, key);
		else if (key > arr[mid]) // 키가 중간값보다 크면 오른쪽부분 다시 재검색
			return binarySearch(arr, mid + 1, right, key);
		else // 일치하면 인덱스 출력
			return mid;

	}

}
```
