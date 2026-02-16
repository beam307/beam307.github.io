---
layout: post
title: '[알고리즘] 정렬 알고리즘 종류와 시간복잡도 1부 (버블, 선택, 삽입)'
author: chanhee.kim
date: 2019-03-01 14:01
updated_at: 2026-02-16
tags: [algorithm, sorting, big-o, time-complexity, data-structure]
image: /files/covers/blog.jpg
description: "정렬 알고리즘의 종류와 시간복잡도(Big-O)를 정리합니다. 1부에서는 버블, 선택, 삽입 정렬을 다룹니다."
---

#### 정렬 알고리즘 종류
* 버블정렬
* 선택정렬
* 삽입정렬
* 병합정렬
* 퀵정렬
* 팀정렬
* 기수정렬

그 외 많이 있으나 이정도만 알면 될듯 Java의 Arrays.sort()나 Colletions.sort()는 병합정렬, 팀정렬, 듀얼-피벗 퀵정렬을 사용한다 <br><br>
클래스를 뜯어 본 결과 기본형은 듀얼-피벗 퀵정렬 / 참조형은 팀정렬, 병합정렬 을 사용하는듯?


#### 1. 버블정렬
버블정렬은 1번째와 2번째 원소를 비교하여 정렬하고, 2번째와 3번째, ..., n-1번째와 n번째를 정렬한 뒤 다시 처음으로 돌아가 이번에는 n-2번째와 n-1번째까지, ...해서 최대 n(n-1)/2​번 정렬한다.<br>
<strong class="red">만들기가 쉽고 직관적</strong>일 뿐이지 알고리즘적 관점에서 보면 비효율적인 정렬 방식
* 시간복잡도 : O(n<sup>2</sup>)
* 소스 생략

#### 2. 선택정렬
선택정렬은 1번째부터 끝까지 훑어서 가장 작은 게 1번째, 2번째부터 끝까지 훑어서 가장 작은 게 2번째……해서 (n-1)번 반복한다
<br> <strong class="red">사람이 사용하는 정렬 방식과 닮았다.</strong>
* 시간복잡도 : O(n<sup>2</sup>)
* 소스 생략

#### 3. 삽입정렬
삽입정렬은 n번째 원소를 1부터 n-1까지와 비교해 적절한 위치에 끼워넣고 그 뒤의 자료를 한 칸씩 뒤로 밀어내는 방식으로, <strong class="red">평균적으론 O(n<sup>2</sup>)중 빠른 편이나 최악의 경우는 느리다</strong>
* 시간복잡도 : O(n<sup>2</sup>)
* 소스 생략
<br>

위 3가지의 정렬은 누구나 쉽게 짤 수 있고 정보처리기사에도 나오고 기본정렬이므로 소스는 생략합니다.

---

#### 4. 병합정렬
병합정렬은 원소 개수가 1 또는 0이 될 때까지 두 부분으로 쪼개고 쪼개서 자른 순서의 역순으로 크기를 비교해 병합해 나간다. 성능은 아래의 퀵 정렬보다 전반적으로 뒤떨어지고, <strong class="red">데이터 크기만한 메모리가 더 필요하지만 stable sort(같은 값의 앞뒤가 순서가 바뀌지않음)이 최대 장점이다.</strong>
* 시간복잡도 : O(n*logN)
* 병합정렬 소스

``` java

import java.util.Arrays;

public class Main {

	public static void main(String[] args) {

		int[] arr = { 5, 2, 6, 8, 9, 4, 3, 7, 1 };
		System.out.println("---- 정렬전 -----");
		System.out.println(Arrays.toString(arr));
		System.out.println("---- 정렬후 -----");
		mergeSort(arr, 0, arr.length - 1);
		System.out.println(Arrays.toString(arr));
		// console
		// ---- 정렬전 -----
		// [5, 2, 6, 8, 9, 4, 3, 7, 1]
		// ---- 정렬후 -----
		// [1, 2, 3, 4, 5, 6, 7, 8, 9]
	}

	public static void mergeSort(int[] arr, int start, int end) {
		int middle;
		if (start < end) {
			middle = (start + end) / 2;
			mergeSort(arr, start, middle); // 앞부분 분할수행
			mergeSort(arr, middle + 1, end); // 뒷부분 분할수행
			merge(arr, start, middle, end); // arr[start...middle], arr[middle...end] 앞부분과 뒷부분 배열을 합친다.
		}
	}

	public static void merge(int[] arr, int start, int middle, int end) {
		int front_start = start; // 앞부분 시작점
		int back_start = middle + 1; // 뒷부분 시작점
		int index = start; // 정렬된 원소를 저장할 위치
		int[] temp = new int[arr.length];

		// 앞쪽배열이나 뒤쪽배열이 끝까지 갈때까지 반복문 돌린다.
		// 가면서 두 배열중 작은것을 차례대로 temp에 쌓는다.
		while (front_start <= middle && back_start <= end) {
			if (arr[front_start] < arr[back_start]) {
				temp[index++] = arr[front_start++];
			} else {
				temp[index++] = arr[back_start++];
			}
		}

		// 뒤쪽 배열이 먼저 끝났을 경우 앞쪽배열이 남아있으니 차례대로 temp에 넣어준다.
		while (front_start <= middle) {
			temp[index++] = arr[front_start++];
		}

		// 반대로 앞쪽 배열이 먼저 끝났을경우 뒤쪽배열이 남아있으니 차례대로 temp에 넣어준다.
		while (back_start <= end) {
			temp[index++] = arr[back_start++];
		}

		// 정렬된 temp를 다시 arr에 덮어쓴다.
		for (int i = start; i <= end; i++) {
			arr[i] = temp[i];
		}

	}

}


```

다음편에서는 힙정렬, 퀵정렬, 팀정렬, 기수정렬을 알아보겠다.
##### 다음편바로가기
<a href="https://beam307.github.io/2019/03/13/sort-algorithm2/">[알고리즘] 정렬알고리즘 종류와 시간복잡도(BigO) 2부</a>
