---
layout: post
title: '[알고리즘] 정렬알고리즘 종류와 시간복잡도(BigO) 2부'
author: chanhee.kim
date: 2019-03-13 17:12
tags: [algorithm]
image: /files/covers/blog.jpg
---

##### 전편바로가기
<a href="https://beam307.github.io/2019/03/01/sort-algorithm/">[알고리즘] 정렬알고리즘 종류와 시간복잡도(BigO) 1부</a>


#### 5. 퀵정렬
퀵정렬은 적절한 원소 하나를 기준(피벗, pivot)으로 삼아 그보다 작은 것을 앞으로 빼내고 그 뒤에 피벗을 옮겨 피벗보다 작은 것, 큰 것으로 나눈뒤 나누어진 각각에서 다시 피벗을 잡고 정렬해서 각각의 크기가 0이나 1이 될 때까지 정렬한다.<br>
<strong class="red">구현된 정렬 알고리즘 중 가장 고성능인 알고리즘이 바로 이 퀵정렬이다. 최악의 경우에는 시간복잡도가 O(n<sup>2</sup>)가 되는데, 피벗을 최솟값이나 최댓값으로 계속해서 잡게 되는 경우에 그렇다</strong>
* 시간복잡도 : O(n*logN) // 최악은 O(n<sup>2</sup>)
* 퀵정렬 소스

``` java
import java.util.Arrays;

public class Main {

	public static void main(String[] args) {

		int[] arr = { 5, 2, 6, 8, 9, 4, 3, 7, 1 };
		System.out.println("---- 정렬전 -----");
		System.out.println(Arrays.toString(arr));
		System.out.println("---- 정렬후 -----");
		quickSort(arr, 0, arr.length - 1);
		System.out.println(Arrays.toString(arr));
		// console
		// ---- 정렬전 -----
		// [5, 2, 6, 8, 9, 4, 3, 7, 1]
		// ---- 정렬후 -----
		// [1, 2, 3, 4, 5, 6, 7, 8, 9]

	}

	public static void quickSort(int[] arr, int s, int e) {
		int start = s; // 시작점 임시변수
		int end = e; // 끝점 임시변수
		int pivot = arr[(s + e) / 2]; // 피벗 변수

		while (start <= end) { // 시작점이 끝점보다 작거나 같을때까지 반복
			while (arr[start] < pivot) // 앞쪽 배열값이 피벗보다 작으면 뒤로 인덱스 이동
				start++;
			while (arr[end] > pivot) // 뒤쪽 배열값이 피벗보다 크면 앞으로 인덱스 이동
				end--;

			if (start <= end) { // 예외로 한번 확인
				int temp = arr[start]; // 시작점, 끝점 값 swap해주기
				arr[start] = arr[end];
				arr[end] = temp;

				start++; //
				end--;
			}
		}

		// while문이 다돌면 피벗위치가 남는다.

		if (s < start - 1) { // 피벗위치 앞부분 정렬
			quickSort(arr, s, start - 1);
		}
		if (start < e) { // 피벗위치포함 뒷부분 다시 정렬
			quickSort(arr, start, e);
		}
	}

}
```
