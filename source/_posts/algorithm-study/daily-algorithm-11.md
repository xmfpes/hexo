---
title: 알고리즘 스터디 - 11(Sort - 힙 정렬(Heap Sort)
date: 2017-10-10 17:44:16
tags: 
- Algorithm
- Heap Sort
categories: 
- CS
- Algorithm
- Study
---

## 매일 하려고 하는 알고리즘 스터디 - Sort

![algorithm](/images/algorithm.png)

### Day 11
## 정렬(Sort) - 힙 정렬(Heap Sort)

최악의 경우에도 시간 복잡도 O(nlong) 인 정렬.

Merge Sort와 다르게 추가적인 배열이 필요 없다.

이진 힙(Binary Heap) 자료 구조를 사용한다.

## Heap
![heap](/images/algorithm/heap.png)

### - Complete binary tree

![tree](/images/algorithm/tree.png)

이진 트리는, 각 노드가 최대 두개의 자식을 가지며,
그 두 자식은 또 두 자식을 가질 수 있는 구조를 가진 트리이다.

full binary tree는 노드들이 꽉찬 형태의 이진 트리이며

완전 이진 트리는, 마지막 레벨을 제외하고 가득 찬 트리이다.
가장 마지막 인덱스부터 연속된 몇개의 노드는 비어있을 수 있다.

### - Heap property

Max Heap Property와 Min Heap Property로 나눌 수 있는데,
- Max Heap

부모가 항상 자식보다 크거나 같은 Heap

- Min heap

부모가 항상 자식보다 작은 Heap

이번 예제에서는 Max Heap을 이용해 Max Heap을 구성하고, 맨 위에 제일 큰 값을 제일 뒤 인덱스로 빼고 배열의 인덱스를 하나 감소시키는 방법으로 재귀호출해서 최종적으로 정렬되어 있도록 구현할 것이다.

![heap](/images/algorithm/heap2.png)

힙은 위처럼, 같은 데이터를 가져도 모양이 다를 수 있다.

힙은 일차원 배열로 표현할 수 있고 complete binary tree이기 때문에

부모 자식 관계에 대해서도 아래처럼 인덱스 값을 이용해 가져올 수 있다.

#### A[i]의 부모 노드 = A[i/2]

#### A[i]의 왼쪽 자식 노드 = A[i*2]

#### A[i]의 오른쪽 자식 노드 = A[i*2+1]


### MAX - HEAPIFY
Heap을 이용해 정렬하기에 앞서서 유일하게 Root 노드의 값만이 heap을 만족하지 않는 상황이 있다고 가정하고, 이 상황에 Heap을 만드려면 어떻게 해야하는지에 대해 살펴보자.

![heap](/images/algorithm/heap3.png)

위처럼 4가 루트에 있는 경우, 두 자식 중 큰 값과 교환하고, 그 일을 반복해서 아래와 4번째의 트리처럼 다시 Heap을 구성해 줄 수 있다.

이는 반복되는 작업이기 때문에 Recursion으로 구현할 수 있다.


### Max Heapify 메소드 Version 1 (Recursion)
```java
MAX-HEAPIFY(A, i){
  //자식이 없으면 종료
  if there is no child of A[i]
    return;
  //두 자식중 더 큰 쪽을 A[k]에 할당
  k <- index of the biggest child of i
  //자식보다 본인이 크면 Heapify는 완료된 것이므로 종료
  if(A[i] >= A[k]){
    return;
  }
  //스왑 후 재귀 호출
  swap(A[i], A[k]);
  MAX_HEAPIFY(A, i);
}
```

### Max Heapify 메소드 Version 2 (Loop)
```java
MAX-HEAPIFY(A, i){
  while(A[i] has no child){
    //두 자식중 더 큰 쪽을 A[k]에 할당
    k <- index of the biggest child of i
    if(A[i] >= A[k]){
      return;
    }
    swap(A[i], A[k]);
    i=k;
  }
}
```

우선 HEAPIFY를 수행하기 이전에,
정렬하려는 배열을 HEAP으로 만들어줄 필요가 있다.

아래와 같이
4 1 3 2 16 9 10 14 8 7
을 가진 배열이 있다고 한다면,

아래와 같이 완전 이진 트리 형태로 만들어 줄 수 있다.
![heap](/images/algorithm/heap4.png)

트리 기준 오른쪽에서 왼쪽으로 가면서, 힙인지 아닌지 여부를 판단하고 힙으로 만들어 주면서 진행하면 된다.

처음으로 리프 노드가 아닌 노드에서(=마지막 노드의 부모 노드 n/2) 이 노드를 루트로 하는 서브트리에서 이 서브트리에 대해 작은 힙인지 아닌지를 판별할 수 있다.

처음 판별해야 하는 노드는 16이며 마지막 인덱스부터 해서, 16, 2, 3, 1, 4 순으로 체크하게 된다.

### Build max Heap method
```java
buildMaxHeap(arr a[]){
  int length = a.length;
  //마지막 노드의 부모 노드에서부터 시작
  for(int i=length/2; i>=0; i--){
    MAX-HEAPIFY(a, i);//logn 시간 복잡도
  }
}
```

힙으로 만드는 시간 복잡도는, 여러 과정을 거쳐서 O(n)이 된다.

위의 과정을 거쳐 주어진 배열을 힙으로 만들고 나서

![heap](/images/algorithm/heap5.png)

루트 노드와 마지막 노드의 자리를 바꾼 뒤, 배열의 크기를 1 줄여서
마지막 값을 힙의 일부가 아니도록 수정합니다.
그리고 루트 노드에 대해서 HEAPIFY(0)를 수행하고, 위의 과정을 반복하면

마지막 인덱스부터 차근차근 큰 값이 쌓여, 오름차순으로 정렬되게 됩니다.

이를 간단한 코드로 나타내면

이런 구조를 가지게 됩니다.

### Heap Sort method
```java
heapSort(int[] arr){
  buildMaxHeap(arr);
  for(int i=arr.length; i>=1; i--){
    swap(arr[1], arr[i]);
    length-=1;
    MAX_HEAPIFY(a, 0)
  }
}
```

조금 지저분하지만, 직접 짜본 힙 소트의 자바 코드입니다.
```java
  public static void main(String[] args) {
		int arr[] = { 5, 2, 1, 7, 112, 42, 52, 62 };
		heapSort(arr);
		printArray(arr);
	}

	private static void heapSort(int[] arr) {
		// TODO Auto-generated method stub
		int length = arr.length - 1;
		maxHeapBuild(arr);
		for (int i = length; i >= 1; i--) {
			swap(arr, 0, i);
			length-=1;
			maxHeapify(arr, 0);
		}
	}

	private static void maxHeapBuild(int[] arr) {
		int length = arr.length;
		// 마지막 노드의 부모 노드에서부터 시작
		for (int i = length / 2; i >= 0; i--) {
			maxHeapify(arr, i);
		}
	}

	private static void maxHeapify(int[] arr, int i) {
		int length = arr.length;
		int leftChild = i*2;
		int rightChild = (i*2)+1;
		int k = 0;

		//자식 노드가 없을 경우에는 종료한다.
		if(leftChild > length || rightChild > length) {
			return;
		}
		//더 큰 Child를 k에 할당한다.
		if (arr[leftChild] > arr[rightChild]) {
			k = leftChild;
		} else {
			k = rightChild;
		}

		//부모 노드가 자식보다 큰 경우 종
		if (arr[i] >= arr[k]) {
			return;
		}

		//swap
		swap(arr, i, k);
		maxHeapify(arr, k);
	}

	public static void printArray(int arr[]) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	public static void swap(int arr[], int i, int j) {
		int temp = arr[i];
		arr[i] = arr[j];
		arr[j] = temp;
	}
```