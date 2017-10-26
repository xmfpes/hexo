---
title: 알고리즘 스터디 - 09(Sort - 합병 정렬(Merge Sort)
date: 2017-10-02 16:30:55
tags: 
- Algorithm
- Merge Sort
categories: 
- CS
- Algorithm
- Study
---

## 매일 하려고 하는 알고리즘 스터디 - Sort

![algorithm](/images/algorithm.png)

### Day 9
## 정렬(Sort) - 합병 정렬(Merge Sort)

Quick Sort / Merge sort

이 두가지 정렬법은 분할정복법(Divide and Conquer)이라는 전략을 사용하는 정렬 알고리즘이다.

## Merge Sort 개요
합병 정렬은 아래의 3가지 프로세스로 이루어진다.
  - 분할 : 해결하고자 하는 문제를 작은 여러 문제로 분할
  - 정복 : 각각의 작은 문제를 순환적으로 해결
  - 합병 : 작은 문제의 해를 합병(merge)하여 원래 문제의 해를 구함

![merge](/images/algorithm/merge.png)

위의 그림에서 ALGORITHMS를 정렬한다고 가정했을 때
반씩 나누어 정렬을 한 다음에, 각각 0번째 인덱스부터 비교해서 작은 값부터 채워넣어 정렬을 완료할 수 있습니다.

![merge](/images/algorithm/merge2.png)

위에서 제일 아래의 데이터 5,2,4,7,1,3,2,6이 우리가 정렬해야 할 데이터입니다.

여기서 정렬을 위해 {5,2,4,7}, {1,3,2,6} 두개로 나누고

여기서 정렬을 위해 또 반으로 나누어 {5,2}, {4,7} / {1,3}, {2,6},

마지막으로 {5},{2},{4},{7},{1},{3},{2},{6}으로 다 나누어 준 후 대소를 비교하며 합병을 시작합니다.

결국 정렬을 위해서 분할을 하다보면, 모든 데이터는 하나씩 나누어지게 됩니다.


이 정렬 과정에서 가장 중요한 것은 합병하여 정렬된 리스트를 만들어 내는 것인데,
합병할 때 a, b 배열 두개가 있다고 가정했을 시, 가장 작은 값은 a의 0번 인덱스 혹은 b의 0번 인덱스가 됩니다.

A 배열

i = 0;

| 0(i) | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 1 | 6 | 15 | 22 | 33 |

B 배열

j = 0;

| 0(j) | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 3 | 5 | 19 | 21 | 32 |

정렬되어 들어갈 C 배열

k = 0;

| 0(k) | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
|  |  |  |  |  |  |  |  |  |  |  |

위와같은 예가 있다고 한다면



제일 처음 값은 1과 3을 비교해서, 더 작은 1이 들어가게 됩니다.
```
if(a[i](1) < b[i](3)){
  c[k++] = a[i++];
  (c[0] = 1;)
}
```
i++, k++
값을 넣는 과정에서 a 배열의 인덱스와 k 배열의 인덱스를 하나씩 증가시켜줍니다.

#### PASS 1

| 0 | 1(i) | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 1 | 6 | 15 | 22 | 33 |

| 0(j) | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 3 | 5 | 19 | 21 | 32 |

| 0 | 1(k) | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| 1 |  |  |  |  |  |  |  |  |  |  |


#### PASS 2
이제 여기서 6과 3을 비교하면 3이 작으므로

| 0 | 1(i) | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 1 | 6 | 15 | 22 | 33 |

| 0 | 1(j) | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 3 | 5 | 19 | 21 | 32 |

| 0 | 1 | 2(k) | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| 1 | 3 |  |  |  |  |  |  |  |  |  |

위와 같이 진행되게 됩니다.

위의 과정을 반복해서 최종적으로

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| 1 | 3 | 5 | 6 | 15 | 19 | 21 | 22 | 32 | 33 |

위와 같은 정렬된 배열을 도출해 낼 수 있습니다.

## Merge Sort 슈도 코드

```java
mergeSort(arr[], int l, int r){
  if(l < r){
    int mid = (l+r)/2;
    mergeSort(arr, l, mid);
    mergeSort(arr, mid+1, r);
    merge(arr, l, mid, r);
  }
}

merge(arr[], int l, int mid, int r){
  정렬된 두 배열(arr[l..mid]), (arr[mid+1..r])를 합병한다.
  정렬된 하나의 배열(arr[l..r])을 도출한다.
}
```

merge를 구현해보면 아래의 코드와 같다.
```java
merge(int arr[], int l, int mid, int r){
  int i = l;
  int j = mid + 1;
  int k = l;
  int temp[] = new int[arr.length];
  while(i<=mid && j<=r){
    if(arr[i] < arr[j]){
      temp[k++] = arr[i++];
    }else{
      temp[k++] = arr[j++];
    }
  }

  while(i<=mid){
    temp[k++] = arr[i++];
  }
  while(j<=r){
    temp[k++] = arr[j++];
  }

  for(int i=l; i<=r; i++){
    arr[i] = temp[i];
  }
}
```
## Merge Sort 시간 복잡도
#### Merge Sort는 O(nlogn) 시간 복잡도를 가진다.

![merge](/images/algorithm/merge3.png)

n개의 데이터를 mergeSort 하는데에 걸리는 시간을 T(n)이라고 가정하면
n이 1인 경우는 정렬할 필요가 없으므로 비교 연산이 0번 일어난다.

T(n) = T([n/2]) + T([n/2])(홀수인 경우 조금 다를 수는 있지만, 점근적 시간복잡도 계산을 하기 때문에 생략한다.)

거기에 merge를 하는데 필요한 비교 연산의 횟수는 최대 n이다.

따라서 T(n) = 2T(n/2) + n 가 나오고 이 순환식을 수학적으로 풀면

O(nlogn)의 시간 복잡도가 나오게 된다.

## Merge Sort 전체 코드
자바로 구현한 Merge Sort 코드입니다.
위의 코드와 거의 비슷한 형태를 하고 있습니다.

다른점은 merge 과정의 마지막에 조건을 <k로 두었는데, <k나 <=r이나 결국 같은 값을 같기 때문에 같은 결과가 나옵니다.

```java
public static void main(String[] args) {
		int arr[] = {6,3,3,7,1,21,3,7,2,25};
		mergeSort(arr, 0, arr.length - 1);
		printArray(arr);
	}
	public static void mergeSort(int arr[], int l, int r) {
		if(l<r) {
			int mid = (l+r)/2;
			mergeSort(arr, l, mid);
			mergeSort(arr, mid+1, r);
			merge(arr, l, mid, r);
		}
	}
	public static void merge(int arr[], int l, int mid, int r) {
		int i = l;
		int j = mid+1;
		int k = l;
		int temp[] = new int[arr.length];
		while(i<=mid && j<=r) {
			if(arr[i] < arr[j]) {
				temp[k++] = arr[i++];
			}else {
				temp[k++] = arr[j++];
			}
		}
		while(i<=mid)
			temp[k++] = arr[i++];
		while(j<=r)
			temp[k++] = arr[j++];
		for(int index=l; index<k; index++)
			arr[index] = temp[index];
	}
	public static void printArray(int arr[]) {
		for(int i=0; i<arr.length; i++) {
			System.out.print( arr[i] + " ");
		}
		System.out.println();
	}
```

```java
int arr[] = {6,3,3,7,1,21,3,7,2,25};
mergeSort(arr, 0, arr.length - 1);
printArray(arr);

결과

1 2 3 3 3 6 7 7 21 25 
```