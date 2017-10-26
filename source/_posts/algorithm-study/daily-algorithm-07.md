---
title: 알고리즘 스터디 - 07(Recursion - 재귀를 이용한 멱집합(powerset) 알고리즘 구현)
date: 2017-09-30 13:08:24
tags: 
- Algorithm
- powerSet
categories: 
- CS
- Algorithm
- Study
---
## 매일 하려고 하는 알고리즘 스터디 - Recursion

![algorithm](/images/algorithm.png)

### Day 7
## Recursion 응용을 이용한 멱집합(powerset) 구하기

멱집합은 주어진 집합의 모든 부분집합들을 원소로 가지는 집합이다.

data = {a, b, c, d}를 이용해 멱집합을 구한다고 치면,

result = {}, {a}, {b}, {c}, {d}, {a,b}, {a,c} ..... {a,b,c,d}

총 16개의 결과가 나온다.

### {a,b,c,d,e,f}의 부분집합들을 구하는 과정

다음과 같은 두가지로 나누어 생각할 수 있다.
- a를 포함하는 경우 :
{b,c,d,e,f}의 부분집합 각각들에 대해 {a}를 더해주어 나올 수 있다.
- a를 포함하지 않은 경우 :
{b,c,e,d,f}의 부분집합들이 될 수 있다.

두가지 각각 2^5 경우 두가지를 더해서, 총 2^6가지의 부분집합을 가진다.

여기서 재귀의 요소를 찾을 수 있는데

전체의 부분집합을 구하기 위해 {b,c,d,e,f}의 부분집합을 구해야 하는데

이때 또 {b}를 제외하고 {c,d,e,f}의 부분집합을 구해서 {b}의 부분집합을 더해서 구할 수 있고,
여기서 또 {c,d,e,f}의 부분집합을 구할때에도, {d,e,f} .... 이렇게 쪼개어서 재귀적인 요소를
이용해 전체의 부분집합을 구할 수 있다.

이를 이용해 powerset을 구하는 과정을 슈도코드를 작성해 보자.

```java
powerSet(P, S){
  if S is empty set{
    print p
  }else{
    powerSet(P, S-{t});
    powerSet(P U {t}, S-{t});
  }
}
```

초기호출은 P 공집합, S 전체 데이터로 시작한다.
이럴 경우 S는 전체의 멱집합을 구하고 P와 합집합을 하지만, P가 공집합이기 때문에 S의 멱집합을 구하고 출력하게 되는 형태가 된다.

S가 공집합이라면, S에 P를 합집합하여 출력하게 된다.

{b,c,d,e,f}의 부분집합에 {a}를 추가한 집합을 나열하려면
1. {c,d,e,f}의 부분집합에 {a,b}를 추가한 집합을 나열한다.
2. {c,d,e,f}의 부분집합에 {a}를 추가한 집합을 나열한다.
의 경우를 생각해 볼 수 있고 여기서 S는 {c,d,e,f}, P는 포함 여부에 따라 {a,b}가 되거나 {a}가 된다.

S는 k번째 원소부터 마지막 까지, P는 처음부터 k-1번째 원소들 중 일부이다.
결국 S와 P를 집합 형태로 넘길 필요 없이 int k 정수를 이용해서 P와 S를 구할 수 있다는 이야기가 된다.
따라서, 아래처럼 powerSet 메소드의 파라미터는 int k가 될 수 있다.

```java
powerSet(int k){
  if(S is empty){
    print p
  }else{
    powerSet(P, S-{t});
    powerSet(P U {t}, S-{t});
  }
}
```

결국, S를 표현하기 위해서 S 전체를 넘길 필요 없이 k라는 변수 하나를 넘겨서 S와 P의 데이터를 얻어올 수 있다는 이야기가 된다.

글로만 계속 적으니 이해가 잘 안되는데, 아래의 상태공간트리는
{a,b,c} 경우의 멱집합을 구하는 예이다. include와 exclude가 뒤바뀌어 있다는 점을 참고하고 보자

![powerset](/images/powerset.png)

위는 includes / exclude가 뒤바뀐 상태공간트리

공집합에서, a를 exclude하는 경우와 include 하는 경우로 나눌 수 있고,
이 경우에서 또 b를 exclude, include하는 경우로 나눌 수 있다

이렇게 되면 첫번째 사이클에서 {} / {a}가 나오며
두번째 사이클에서 {}, {b} / {a}, {a,b}가 나온다.
세번째 사이클에서는 {}, {c}, {b}, {b,c} / {a}, {a,c}, {a,b}, {a,b,c}
로, 전체의 멱집합을 구할 수 있다.

이를 코드로 나누어 구현해보자
우선 base case는, k가 data의 length와 같아지는 k==N인 경우이다.
k가 부분집합을 가진 배열 data의 length와 같아지는 경우는 상태공간트리에서 나타내는 제일 아래 depth에 도달한 것이기 때문에 이때의 부분집합을 출력해야 한다.
이는 아래와 같이 구현할 수 있다.
```java
if(k==N) {
  for(int i=0; i<k; i++) {
    if(includes[i]) {
      System.out.print(data[i] + " ");
    }
  }
  System.out.println();
  return;
}
```
위에서, includes에는 각각 a,b,c,d,e를 포함시킬지 말지에 대한 true false 정보가 들어있다.

그리고 재귀호출을 통해 basecase에 도달하도록 하는 부분의 코드를 작성해 주어야 한다.
제일 처음 k=0일때를 기준으로 설명하자면,
처음 k=0를 포함하는 경우 / k=0을 포함하지 않는 경우로 나누어서 powerSet 메소드를 호출하면 된다.

이를 코드로 구현해보면
```java
else{
  includes[0] = false;
  //a를 포함하지 않고 {b,c,d,e}를 구한다.
  powerSet(1);
  //상태공간트리를 기준으로 먼저 왼쪽으로 내려간다(제일 처음 출력은 모든것이 exclude된 공집합)
  includes[0] = true;
  //a를 포함한 상태에서 {b,c,d,e}를 구한다.
  powerSet(1);
  //상태공간트리를 기준으로 오른쪽으로 내려간다.
}
```
라는 간단한 코드가 나온다.
트리나 설명은 복잡하지만, 코드 자체의 내용은 간단하다.
여기서 includes[k]를 포함하지 않는 경우의 부분집합 / includes[k]를 포함하여 얻는 부분집합으로 나누어 재귀를 진행하며 k==N인 경우에는 data 내의 모든 원소들에 대해 포함할지 안할지 여부가 정해진 것이기 때문에 그 결과값을 출력한다.

두개의 조건을 합쳐서, 아래와 같은 멱집합을 구하는 코드를 도출할 수 있다.

```java
public static void main(String[] args) {
		powerSet(0);
	}
	public static char data[] = {'a','b','c','d','e'};
	public static final int N = data.length;
	public static boolean includes[] = new boolean[N];
			
	public static void powerSet(int k) {
		if(k==N) {
			for(int i=0; i<k; i++) {
				if(includes[i]) {
					System.out.print(data[i] + " ");
				}
			}
			System.out.println();
			return;
		}else {
			includes[k] = false;
			powerSet(k+1);
			includes[k] = true;
			powerSet(k+1);
		}
	}
```

아래의 출력된 결과와 상태공간트리를 비교해보면, 어떤식으로 재귀가 호출되었는지 쉽게 알 수 있다.
```

e 
d 
d e 
c 
c e 
c d 
c d e 
b 
b e 
b d 
b d e 
b c 
b c e 
b c d 
b c d e 
a 
a e 
a d 
a d e 
a c 
a c e 
a c d 
a c d e 
a b 
a b e 
a b d 
a b d e 
a b c 
a b c e 
a b c d 
a b c d e 

```

첫줄부터 보자면 상태공간트리에서 가장 왼쪽인 a,b,c,d,e 모두가 exclude된 공집합이 출력되었고
그다음에는 a,b,c,d가 exclude되고 e만 inlcude된 상태,
그다음에는 a,b,c가 exclude되고 d가 inlcude된 상태에서 e가 exclude, include된 두가지 경우가 나온다.
이런식으로 결과값이 어떻게 도출되었는지를 생각하면 위의 상태공간트리와 코드가 이해될 것이다.
