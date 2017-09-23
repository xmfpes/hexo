---
title: 알고리즘 스터디 - 02(Recursion - 재귀)
date: 2017-09-22 23:01:08
tags: Algorithm
categories: 알고리즘 스터디
thumbnail: /images/algorithm.png
---

## 매일 하려고 하는 알고리즘 스터디 - Recursion

### Day 2
## Recursion(재귀) 함수 - 2

어제에 이어 간단한 재귀함수들을 구현해본다.

- ### 문자열 순서대로 하나씩 출력하는 재귀함수 구현

```java
public static void main(String[] args) {
		String str = "1234567";
		printStr(str);
	}
	public static void printStr(String str) {
		if(str.length() == 0) {
			return ;
		}else {
			System.out.print(str.charAt(0));
			printStr(str.substring(1));
		}
	}
}
```

- ### 문자열 하나씩 뒤집어서 출력하는 재귀함수 구현하기

간단하긴 한데, 뭔가 순간 이해가 안돼서 조금 고민한 문제..

위에서는 0번째를 출력하고 인덱스를 하나 증가시킨 곳에서 다시 0번째 출력이라면

이번에는 재귀함수를 먼저 실행해서 길이가 0일때 마지막에 실행된 메소드가 종료되며

메소드 호출은
 
reverseStr(1~7) -> reverseStr(2-7) -> reverseStr(3-7) -> ... reverseStr(7) -> reverseStr("")

메소드는 스택에 쌓이므로 제일 마지막에 실행된 메소드부터 실행된다.

공백을 가진 reverStr에서는 종료가 되고, 그다음 쌓여있던 reverseStr(7)에서 System.out.print(7)을 실행해서 7

그다음 쌓인 메소드인 reverseStr(6,7)에서 System.out.print(6) 이런식으로 실행된다.

메소드가 스택에 쌓이는 개념에 대해 이해가 조금 부족해서 순간 헷갈리는 문제였음

```java
public static void reversePrintStr(String str) {
    if(str.length() == 0) {
        return;
    }else {
        reversePrintStr(str.substring(1));
        System.out.print(str.charAt(0));
    }
}
```

- ### 숫자 n을 이진수로 바꾸어 출력하기

이 문제는 2로 나눈 나머지값을 이용해 이진수를 구하는 것을 재귀함수로 구현하는 문제이다.

간단하지만, 재귀에 대한 개념이 익숙치 않아서 조금 헷갈렸다.

binary(4) -> binary(2) -> binary(1) 
binary(1)에서 1 출력, 2에서 0 출력 , 4에서 0 출력해서 100이 출력된다.

나중에 이진수를 뽑을 일이 있으면, return해서 StringBuilder에 붙이고 reverse 해주면 될거 같다.

```java
public static void binary(int n) {
		if(n < 2) {
			System.out.print(n);
		}else {
			binary(n/2);
			System.out.print(n%2);
			
		}
	}
```

- ### 배열 값을 더해서 출력하기
간단한 문제인데, 문제만 보고 단순히 배열만 더하려고 했더니
n을 파라미터로 넣을 생각을 안해서 조금 고민했다.
탑 다운으로 n + arraySum(n-1)을 해주어서
arr[n] + arr[n - 1] + arraySum(n-2) 이런식으로 해주고, 마지막에 0일때는 0을 더해주어서 값을 출력하는 식으로 구현했다.

```java
public static int arraySum(int n, int arr[]) {
		if(n < 1) {
			return 0;
		}else {
			return arr[n-1] + arraySum(n-1, arr);
		}
	}
```

- ### Scanner를 통해 입력 받는데, data의 0번째 항 부터 입력받는 재귀함수 구현하기
input(int n, int data[], Scanner in)
위의 함수와 파라미터를 보고 먼저 생각해서 구현 해보자.
위의 예제를 잘 해왔다면, 어렵지 않게 구현할 수 있다.

아래의 코드를 통해, 0번째 부터 제대로 입력이 되었는지 출력도 해보자

```java
public static void inputArray(int n, int data[], Scanner input) {
    if(n==0) {
        return;
    }else {
        inputArray(n-1, data, input);
        data[n-1] = input.nextInt();
    }
}

int inputArr[] = new int[10];
inputArray(10, inputArr, new Scanner(System.in));
for(int i=0; i<10; i++) {
    System.out.println(inputArr[i]);
}
```

모든 반복문은, 재귀함수로 표현할 수 있고 재귀함수도 다 반복문으로 표현할 수 있다.
재귀함수는 복잡한 알고리즘을, 간단히 표현할 수 있지만 함수 호출에 따른 오버헤드도 존재(파라미터 전달 등등)