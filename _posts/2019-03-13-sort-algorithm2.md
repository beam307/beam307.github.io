---
layout: post
title: '[알고리즘] 정렬 알고리즘 종류와 시간복잡도 2부 (병합, 퀵, 힙 정렬)'
author: chanhee.kim
date: 2019-03-13 17:12
updated_at: 2026-02-16
tags: [algorithm]
image: /files/covers/blog.jpg
description: "고급 정렬 알고리즘(병합, 퀵, 힙 정렬)의 시간복잡도와 구현 방법을 정리합니다."
keywords: "정렬 알고리즘 종류와 시간복잡도 2부 (병합, 퀵, 힙 정렬), 알고리즘, 개발 블로그"
---

##### 전편바로가기
<a href="https://beam307.github.io/2019/03/01/sort-algorithm/">[알고리즘] 정렬알고리즘 종류와 시간복잡도(BigO) 1부</a>
<br>
※ 모든소스는 java로 짜겠습니다.

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

#### 6. 팀정렬
팀정렬은 <strong class="red">병합+삽입 정렬. 변형된 병합 정렬</strong>이다. 병합 정렬은 원소의 개수가 적을 때 오버헤드가 발생하기 때문에 파티션 크기가 특정 값 이하(보통 16 또는 32)가 되면 삽입 정렬을 사용한다. 병합 정렬과 비슷한 특징을 가지며 대부분의 경우 더 빠르다.
* 시간복잡도 : O(n*logN) // 병합정렬보다 빠름
* 팀정렬은 java내에서 사용하는 sort이므로 소스 생략


#### 7. 기수정렬
기수정렬은 낮은 자리수부터 비교하여 정렬해 간다는 것을 기본 개념으로 하는 정렬 알고리즘입니다. <strong class="red">기수정렬은 비교 연산을 하지 않으며 정렬 속도가 빠르지만</strong> 데이터 전체 크기에 기수 테이블의 크기만한 메모리가 더 필요합니다.
* 시간복잡도 : O(k*n)(k는 데이터의 자릿수)
* 기수정렬 소스

``` java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Iterator;
import java.util.List;

public class Main {

	public static void main(String[] args) {

		int[] arr = { 5, 2, 6, 8, 9, 4, 3, 7, 1 };
		System.out.println("---- 정렬전 -----");
		System.out.println(Arrays.toString(arr));
		System.out.println("---- 정렬후 -----");
		radixSort(arr); // 10진법기준
		System.out.println(Arrays.toString(arr));
		// console
		// ---- 정렬전 -----
		// [5, 2, 6, 8, 9, 4, 3, 7, 1]
		// ---- 정렬후 -----
		// [1, 2, 3, 4, 5, 6, 7, 8, 9]

	}

	public static void radixSort(int[] arr) {
		List<Integer>[] count = new ArrayList[10];
		for (int i = 0; i < count.length; i++) {
			count[i] = new ArrayList<Integer>();
		}
		// count[0] = new ArrayList<Integer>();
		// count[1] = new ArrayList<Integer>();
		// ...
		// count[n] = new ArrayList<Integer>();

		int max = max(arr); // 최대값
		int size = arr.length;
		for (int i = 1; i <= max; i *= 10) { // 1의자리, 10의자리 ... 10^n의자리
			for (int j = 0; j < size; j++) {
				int bucket = (arr[j] / i) % 10;
				count[bucket].add(arr[j]); // 해당자릿수에 값을 버킷에 넣기
			}
			int index = 0;
			for (int j = 0; j < 10; j++) {
				Iterator<Integer> it = count[j].iterator();
				while (it.hasNext()) {
					arr[index++] = it.next(); // 순서대로 꺼내서 차례대로 넣기
					it.remove(); // 넣은 수 지우기
				}
			}
		}
	}

	public static int max(int[] arr) { // 최대값구하는 메서드
		int length = arr.length;
		int result = arr[0];
		for (int i = 1; i < length; i++) {
			if (result < arr[i])
				result = arr[i];
		}
		return result;
	}

}
```

기수정렬 소스는 인터넷에 더 최적화되고 클린한 소스가 있으나 직관적인 소스를 위해 다시 짰습니다.
<br><br>
지금까지 정렬 알고리즘 7가지를 알아보았는데 더 많은 정렬 알고리즘이 있으나 중요한것만 알아보았습니다. 개인적으로는 원리와 직접소스를 만들어보는 용도로만 하고 실무에서는 기본 sort()메서드를 쓰는게 나은거 같습니다.(어차피 java에 최적화되서 구현 되어있더군요.)
