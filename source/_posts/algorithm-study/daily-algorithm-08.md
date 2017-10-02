---
title: 알고리즘 스터디 - 08(Sort - 기본적인 정렬 알고리즘)
date: 2017-10-01 13:17:32
tags: 
- Algorithm
- Sort
categories: 알고리즘 스터디
---

## 매일 하려고 하는 알고리즘 스터디 - Sort

![algorithm](/images/algorithm.png)

### Day 8
## 정렬(Sort) - 기본적인 정렬 알고리즘

정렬은 가장 중요한 것 중 하나이다.

- Simple, Slow
  - Selection Sort
  - Bubble Sort
  - Insertion Sort

- fast
  - Quick Sort
  - Merge Sort
  - Heap Sort


- Radix Sort O(N)

오늘은 간단하고, 비교적 느린 정렬인 Selection, Bubble, Insertion Sort만을 다룬다.

# 1. Selection Sort

아래 코드에서는 가장 큰값을 뒤에 넣어서 구현했지만,
보통은 가장 작은 값을 앞에 채워넣는 식으로 구현한다.

Init Array.

| 0 | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 29 | 10 | 14 | 37 | 13 |

가장 큰 값을 찾아서, 마지막 인덱스와 자리를 바꾼다.

#### STEP 1.

| 0 | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 29 | 10 | 14 | 13 | **37** |

STEP2에서는 제일 마지막에 가장 큰 수가 들어왔으므로, 이제 마지막 인덱스는 신경 써줄 필요가 없다.

#### STEP 2.

| 0 | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 13 | 10 | 14 | **29** | **37** |

이제 제일 마지막과 그 앞의 인덱스는, 정렬이 되었으므로 또 신경쓸 필요가 없다.

#### STEP 3.

| 0 | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 13 | 10 | **14** | **29** | **37** |

#### STEP 4.

| 0 | 1 | 2 | 3 | 4 |
| -- | -- | -- | -- | -- |
| 10 | **13** | **14** | **29** | **37** |

슈도 코드
```java
selectionSort(int arr[], int n){
  for(last <- n down to 2){
    find max[k]
    swap(max[k], arr[last])
  }
}
```
자바에서 직접 Selection Sort를 구현해 보았다.

위의 예제와 달리, 최소값을 가지는 인덱스를 구해 0번째부터 넣어주는 방식으로 구현했다.
```java
public static void selectionSort(int arr[]) {
		for(int i=0; i<arr.length; i++) {
			int minIndex = i;
			for(int j=i+1; j<arr.length; j++) {
				if(arr[minIndex] > arr[j]) {
					minIndex = j;
				}
			}
			int temp = arr[i];
			arr[i] = arr[minIndex];
			arr[minIndex] = temp;
		}
	}
```

##### Selection Sort 시간 복잡도
 (n-1) + (n-2) + ... + 2 + 1 = n(n-1)/2  = O(n^2)


# 2. Bubble Sort

 Init Array.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 29 | 10 | 14 | 37 | 13 |

 가장 큰 값을 찾아서, 마지막 인덱스와 자리를 바꾼다.

 #### PASS 1.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | **29** | **10** | 14 | 37 | 13 |

 첫번째 두번째 비교해서, 더 큰값을 뒤로 보낸다.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | **29** | **14** | 37 | 13 |

 두번째 세번째를 비교해 뒤로 보낸다.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | 14 | **29** | **37** | 13 |


 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | 14 | 29 | **37** | **13** |

 위의 과정을 반복하면, 최종적으로는 가장 뒤에는 제일 큰 값이 남게 된다.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | 14 | 29 | 13 | 37 |

 PASS 1에서 최종적으로는 가장 뒤에는 제일 큰 값이 남게 된다.

 #### PASS 2.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | **10** | **14** | 29 | 13 | 37 |

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | **14** | **29** | 13 | 37 |

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | 14 | **29** | **13** | 37 |

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | 14 | 13 | **29** | **37** |

 #### PASS 3.

 | 0 | 1 | 2 | 3 | 4 |
 | -- | -- | -- | -- | -- |
 | 10 | 13 | 14 | 29 | 37 |

 슈도 코드
 ```java
bubbleSort(int arr[], int n){
   for(last <- n down to 2){
     for(i 0 .. n - 1){
       if(arr[i] > arr[i+1]){
         swap(arr[i], arr[i+1]);
       }
     }
   }
 }
 ```

자바 코드
 ```java
 public static void bubbleSort(int arr[]) {
 		for(int i=arr.length; i>0; i--) {
 			for(int j=0; j<i-1; j++) {
 				if(arr[j] > arr[j+1]) {
 					int temp = arr[j];
 					arr[j] = arr[j+1];
 					arr[j+1] = temp;
 				}
 			}
 		}
 	}
 ```

 ##### Bubble Sort 시간 복잡도
 (n-1) + (n-2) + ... + 2 + 1 = n(n-1)/2  = O(n^2)

 역순배열을 정렬할때 가장 부하가 많이 일어난다. 모두 다 변경이 일어남


# 3. Insertsion Sort

삽입 정렬은, 제일 첫번째 데이터 하나를 보고 정렬되어 있다고 가정하고, 거기서부터
데이터를 삽입하면서 정렬하는 개념이다.

 Init Array.

 | 0 | 1 | 2 | 3 | 4 | 5 |
 | -- | -- | -- | -- | -- | -- |
 | 15 | 12 | 13 | 10 | 14 | 11 |

0번째 인덱스인 15가 이미 정렬된 데이터라고 가정하고, 거기에 12, 13을 삽입해서 정렬된 배열을 만드는 개념이다.

#### STEP 1.

 정렬된 데이터 15에 12를 삽입한다. 15보다 작으므로 15의 앞에 삽입한다.

 | 0 | 1 | 2 | 3 | 4 | 5 |
 | -- | -- | -- | -- | -- | -- |
 | **12** | **15** | 13 | 10 | 14 | 11 |

#### STEP 2.

정렬된 12 , 15 데이터에 13을 삽입하는데, 이는 12, 15의 사이에 해당하므로 해당 위치에 삽입한다.

 | 0 | 1 | 2 | 3 | 4 | 5 |
 | -- | -- | -- | -- | -- | -- |
 | 12 | **13** | 15 | 10 | 14 | 11 |

#### STEP 3.

10을 삽입한다. 이는 제일 앞에 해당하는 위치에 삽입될 것이다.

| 0 | 1 | 2 | 3 | 4 | 5 |
| -- | -- | -- | -- | -- | -- |
| **10** | 12 | 13 | 15 | 14 | 11 |

#### STEP 4.

14를 삽입한다.

| 0 | 1 | 2 | 3 | 4 | 5 |
| -- | -- | -- | -- | -- | -- |
| 10 | 12 | 13 | **14** | 15 | 11 |

#### STEP 5.

11을 삽입한다.

| 0 | 1 | 2 | 3 | 4 | 5 |
| -- | -- | -- | -- | -- | -- |
| 10 | **11** | 12 | 13 | 14 | 15 |

삽입 과정에서 시프트 되는것을 생략했는데, 아래와 그림과 같이 이루어진다.

![insertion](/images/algorithm/insertion.png)

뒤에서부터 비교를 해서 삽입하는데, 이는 앞에서부터 비교를 하더라도, 삽입할 위치 뒤의 원소들을
한칸씩 시프트 해주어야 하기 떄문에 결국 뒤의 데이터를 조작할 필요가 있다.
따라서 그냥 처음부터 뒤에서 비교를 하면서 바로 시프트 해주는 것이 더 효율적이다.

자바로 짠 insertion Sort 코드
```java
public static void insertionSort(int arr[]) {
		for(int i=1; i<arr.length; i++) {
			int temp = arr[i];
			int j=i-1;
			while(j >= 0 && temp < arr[j]) {
				arr[j+1] = arr[j];
				j--;
			}
			arr[j+1] = temp;
		}
	}
```
##### Insertion Sort 시간 복잡도
최악의 경우 :
1,3,4,5,7 에 -1을 삽입하는 경우에는 삽입하는 데이터가 가장 작으므로
모든 값과 비교가 일어난다.

최악의 경우 : (n-1) + (n-2) + ... + 2 + 1 = n(n-1)/2  = O(n^2)

최선의 경우 : O(n), 처음부터 모든 데이터가 정렬되어 있다면, n-1번만에 연산이 된다.