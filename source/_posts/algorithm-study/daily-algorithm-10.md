---
title: 알고리즘 스터디 - 10(Sort - 빠른 정렬(Quick Sort)
date: 2017-10-04 17:44:16
tags: 
- Algorithm
- Quick Sort
categories: 알고리즘 스터디
---

## 매일 하려고 하는 알고리즘 스터디 - Sort

![algorithm](/images/algorithm.png)

### Day 10
## 정렬(Sort) - 빠른 정렬(Quick Sort)

Quick Sort

이전에 다룬 합병 정렬과 같이, 빠른 정렬은 분할정복법(Divide and Conquer)이라는 전략을 사용하는 정렬 알고리즘이다.

 ## Quick Sort 개요

 빠른 정렬은 분할정복법으로 합병 정렬과 유사하지만 약간의 차이점이 있다.

   - 분할 : Pivot을 선정해서, Pivot보다 작은 값과 큰값으로 분할
   - 정복 : 각각의 작은 문제를 순환적으로 해결
   - 합병 : 이미 Recursion을 통해 정렬이 이루어지기 때문에 합병할 필요는 없다.

슈도 코드

```java

quickSort(int arr[], int l, int r){
 if(p<r){
   int pivot = partition(arr, l, r);
   quickSort(arr, l, pivot-1);
   quickSort(arr, pivot+1, r);
 }
}

int partition(int arr[], int l, int r){
 arr[r]을 기준(Pivot)으로 작은값은 왼쪽, 큰값은 오른쪽에 배치한 후에
 그 경계위치에 Pivot을 배치하고 그 Pivot의 위치값인 arr[r]의 위치값을 리턴한다.
}
```

Pivot을 정해 기준값으로 잡고 Pivot보다 작은 값을 왼쪽, 큰값을 오른쪽으로 나누어 분할한다.

빠른 정렬에서는 Partition이 가장 중요하다. 분할해서 Pivot의 위치값만 반환 받으면, Recursion을 통해 재귀 호출하면 되기 떄문이다.

보통 Quick Sort 예제 코드들은 l, r을 왼쪽 끝과 오른쪽에 두고 피벗을 기준으로 큰값이 왼쪽에 있다면 찾고, 작은 값이 오른쪽에 있으면 찾아서 두 값을 swap하는 방식으로 구현되는데

아래의 예제에서는 l,r 인덱스를 앞에서부터 시작하면서 정렬을 시작한다.

시작 인덱스를 아래와 같이 잡고, pivot값을 제일 마지막 값으로 배치한다.

```java
int i = l - 1;(-1)
int j = r;(0)
int pivot = arr[r];
```

![partition](/images/algorithm/partition.png)

```java
for(int j=r; j<r; j++){
  if(arr[j] < pivot){

  }
}
```
위와 같은 반복문을 통해서, 만약 arr[j]가 피벗보다 작다면 i의 인덱스 값을 증가시킵니다.

현재 i의 인덱스값에는 pivot보다 작은 값 중 마지막 값이 들어있으므로 증가시키면, 피벗보다 큰 값중 가장 제일 앞 인덱스를 가지게 됩니다.

pivot보다 작은 값이 발견된 j인덱스의 값과 증가시킨 i인덱스의 값을 swap하면서 반복문을 진행시키면 pivot보다 작은 값이 왼쪽에 모이고, 큰 값이 오른쪽에 모이게 됩니다.

반복문이 종료되고 나면 아래의 코드를 통해 오른쪽의 피벗보다 큰 값중 제일 앞 인덱스와 피벗의 값을 교체한 후에 pivot의 인덱스 값을 리턴하고 다시 quickSort가 피벗 기준 왼쪽, 오른쪽으로 재귀호출 되게 됩니다.

```java
int temp = arr[i+1];
arr[i+1] = arr[r];
arr[r] = temp;

return i+1;
```

위의 코드를 합쳐서 완성된 자바 코드는 아래와 같습니다.
```java
public static void quickSort(int arr[], int l, int r) {
	if(l<=r){
    int p = partition(arr, l, r);
    quickSort(arr, l, p-1);
    quickSort(arr, p+1, r);
  }
}
public static int partition(int arr[], int l,int r) {
	int pivot = arr[r];
	int i = l - 1;
	for(int j=l; j<r; j++) {
		if(arr[j] < pivot) {
			i+=1;
			int temp = arr[i];
			arr[i] = arr[j];
			arr[j] = temp;
		}
	}
	int temp = arr[i+1];
	arr[i+1] = arr[r];
	arr[r] = temp;

	return i+1;
}
```

## Quick Sort 시간 복잡도
#### 최악의 경우 : O(n^2)
- 항상 한쪽은 0개 나머지는 n-1개로 분할되는 경우 : 1...n-1 = n(n-1)/2
- 이미 정렬된 데이터 : 1...n-1 = n(n-1)/2

#### 최선의 경우 : O(nlogn)
- 항상 절반으로 분할되는 경우 : 매번 피벗이 가운데 값인 경우

최악의 경우 복잡도가 O(n^2)으로 느리지만
한쪽이 1/9이상만 되도록 분할이 된다면 O(nlogn) 복잡도가 나오므로 분할이 아주 극단적이지만 않으면 충분히 빠른 정렬 알고리즘이다.


## Pivot의 선택

- 첫번째나 마지막 값을 피봇으로 선택

현실의 데이터는 랜덤하지 않으므로 거꾸로 정렬되거나 하는 데이터가 들어올 가능성이 충분히 존재하기 때문에 좋은 방법이 아니다.

- 첫번째와 마지막 값의 중간값을 피봇으로 선택

위의 경우를 생각해보면, 현실적으로 중간값을 피봇으로 선택하는 것이 좋다.
하지만 최악의 경우 시간 복잡도가 달라지지는 않는다.

- 랜덤으로 선택

입력값에 대해 랜덤으로 피봇을 고르기 때문에 어떤 최악의 경우의 입력값이 정해지지 않는다
이미 정렬된 경우의 최악의 입력값이어도, 랜덤으로 피봇을 정하기 때문에 최악의 경우의 입력값은 정해지지 않는다.
하지만 랜덤으로 최악의 경우의 피봇을 뽑을 수는 있다.(no worst case instance, but worst case execution) 최악의 입력은 없지만, 최악의 실행은 있을 수 있다.
