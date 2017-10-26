---
title: 알고리즘 스터디 - 03(Recursion - 재귀)
date: 2017-09-23 13:36:19
tags: Algorithm
categories: 
- CS
- Algorithm
- Study
---

## 매일 하려고 하는 알고리즘 스터디 - Recursion

![algorithm](/images/algorithm.png)

### Day 3
## Recursion(재귀) 함수 예제 - 3

- ### Recursion을 이용한 순차 탐색 구현하기

매개변수를 명시화 하는 것은 Recursion을 작성하기 쉽게 만든다.

암시적 매개변수를, 명시적 매개변수로 바꾸어서 재귀함수를 구현하자.

```java
public static void main(String[] args) {
	int arr[] = {1,2,3,4,5,6,7,8,9};
	System.out.println(sequentialSort(arr, 0 , 5));
}
public static int sequentialSort(int arr[], int begin , int target) {
	if(begin > arr.length - 1) {
		return -1;
	}else if(arr[begin] == target) {
		return begin;
	}else {
		return sequentialSort(arr, begin+1, target);
	}
}
```
위의 코드는, 제일 아래에서 부터 올라가는
바텀 업으로 구현되어 있고

아래의 코드처럼 end에서부터 시작해서 탑 다운으로 구현할 수도 있다.
```java
int arr[] = {1,2,3,4,5,6,7,8,9};
System.out.println(sequentialSort2(arr, 8, 5));

public static int sequentialSort2(int arr[], int end, int target) {
	if(0 > end) {
		return -1;
	}else if(arr[end] == target) {
		return end;
	}else {
		return sequentialSort2(arr , end - 1, target);
	}
}
```

아니면 아래처럼 가운데를 기준으로 나누어 탐색할 수도 있다.
```java
public static int sequentialSort3(int arr[], int begin, int end, int target) {
	if (begin > end) {
		return -1;
	} else {
		int middle = (begin + end) / 2;
		if (arr[middle] == target) {
			return end;
		}
		int index = sequentialSort3(arr, begin, middle - 1, target);
		if (index != -1) {
			return index;
		}else {
			return sequentialSort3(arr, middle + 1, end, target);
		}
	}
}
```

- ### Recursion을 이용한 배열의 최대값 구하기

최대값은 매번 반복문으로 구현했었는데
아래와 같은 코드로 재귀로 구현 할 수 있다.
재귀함수를 반복 호출하고, begin==end인 경우 호출이 종료되는데, 이때는 가장 마지막 인덱스의 값이 리턴되고 이 리턴된 값은, 이전 인덱스가 가지는 값과 비교된다. 비교를 통해 둘중 큰 값이 다시 리턴되고 재귀호출에 의해 가장 큰 값이 최종적으로 리턴되게 된다.

```java
public static int findMax(int arr[], int begin, int end) {
	if(begin == end) {
		return arr[begin];
	}else {
		return Math.max(arr[begin], findMax(arr, begin + 1, end));
	}
}
```

- ### Recursion을 이용해 이진 탐색 알고리즘 구현하기

재귀함수의 가장 유명한 예라고 할 수 있다.

정렬된 리스트에서, 찾고자 하는 값이 작다면 가운데를 기준으로 왼쪽을 범위로 잡아 다시 함수를 호출하고, 크다면 오른쪽을 전체 범위로 잡아 다시 함수를 호출하는 식으로 재귀를 반복한다.

반복하는 중, 가운데 값이 찾고자 하는 값이면 그 값의 인덱스를 리턴하고, 만약 찾지 못한다면 -1을 리턴한다.
```java
String sortedList[] = {"1","2","3","4","5","6","7"};
System.out.println(binarySearch(sortedList, 0, sortedList.length, "4"));

public static int binarySearch(String items[], int begin, int end, String target) {
	if(begin > end) {
		return -1;
	}else {
		int middle = (begin + end) / 2;
		int compareResult = items[middle].compareTo(target);
		if(compareResult == 0) {
			return middle;
		}else if(compareResult > 0) {
			return binarySearch(items, middle + 1, end, target);
		}else {
			return binarySearch(items, begin, middle - 1, target);
		}
	}
}
```

오늘 학습에 사용된 코드는 깃허브 [알고리즘 레파지토리](https://github.com/xmfpes/daily-algorithm/commit/ddc6b63fd27db4d2114e190b8748d7036aedc54f)에 커밋 로그로 남겨두었습니다.