---
title: 알고리즘 스터디 - 01(Recursion - 재귀)
date: 2017-09-21 22:01:08
tags: Algorithm
categories: 알고리즘 스터디
---

## 매일 하려고 하는 알고리즘 스터디 - Recursion

![algorithm](/images/algorithm.png)

### Day 1
## Recursion(재귀) 함수

Recursion이란 아래의 코드와 같이 자기 자신을 호출하는 함수(메소드)이다.

```java
public static void func(){
  func();
}
```

알고리즘에서 자주 쓰이며, 아래의 간단한 예제들을 직접 구현하며 익힌다.

- ### 1 ~ n까지의 합 구하기

```java
public static void main(String[] args) {
		int n = 10;
		int sum = 0;
		sum += sum(n);
		System.out.println(sum);
	}
	public static int sum(int n) {
		if(n == 0) {
			return 0;
		}else {
			return n + sum(n-1);
		}
	}
```

- ### 재귀함수를 이용한 Factorial 구하기

```java
public static void main(String[] args) {
		int n = 5;
		int sum = 1;
		sum *= factorial(n);
		System.out.println(sum);
	}	
	public static int factorial(int n) {
		if(n <= 1) {
			return n;
		}else {
			return n * factorial(n-1);
		}
	}

```

- ### 재귀함수를 이용한 Fibonacci 수열 구하기

```java
public static void main(String[] args) {
		int n = 5;
		int fibonacchi = 1;
		fibonacchi = fibonacchi(n);
		System.out.println(fibonacchi);
	}	
	public static int fibonacchi(int n) {
		//1번째 항은 1, 2번째 항 (n-1)(1) + (n-2)(0)
		if(n < 2) {
			return n;
		}else {
			return fibonacchi(n-1) + fibonacchi(n-2);
		}
	}
```

- ### 재귀함수를 이용해 유클리드 호제법을 구현해 최대 공약수 구하기

유클리드 호제법은 최대공약수를 구하는 방법인데
두 수가 있을 때, a, b에 대해 a를 b로 나눈 나머지를 r이라고 하면(단 a>b 인 경우)
a와 b의 최대공약수는 b와 r의 최대공약수와 같다고 하고,
이 성질에 따라 b를 r로 나눈 나머지를 구하고 다시 r을 r'로 나눈 나머지를 구하는 과정을 반복하여 나머지가 0이 되었을 때 나누는 수 b가 최대 공약수이라는 것이다.

아래의 예를 보자,

gcb(18, 12) -> 
gcb(12, 18%12) == gcb(12, 6) ->
12와 6에서 나머지가 0이므로, 최대공약수는 6이 된다.

여튼, 재귀함수를 통해 이를 구현할 수 있다.

a>b여야 한다고는 하는데.. b>a인 경우에 나머지가 a가 되고 b가 a로 가면서 결국 재귀호출때
더 큰 값을 앞으로 보내게 되어 있으므로 별로 상관 없는듯 하다.


```java
public static void main(String[] args) {	
		int gcb;
		gcb  = gcb(18, 12);
		System.out.println(gcb);
	}	
	public static int gcb(int a, int b) {
		//a와 b를 나눈 나머지가 0이라
		if(a%b == 0) {
			return b;
		}else {
			return gcb(b, a%b);
		}
	}
```
