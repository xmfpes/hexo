---
title: 알고리즘 스터디 - 06(Recursion - 재귀를 이용한 N Queens Problem 알고리즘)
date: 2017-09-29 22:28:47
tags: 
- Algorithm
- N Queens Problem
categories: 알고리즘 스터디
---
## 매일 하려고 하는 알고리즘 스터디 - Recursion

![algorithm](/images/algorithm.png)

### Day 6
## Recursion 응용을 이용한 N Queens Problem

N Queens Problem에서는 NxN인 2차원 배열의 체스 판이 주어진다.
이 체스 보드에 N개의 말을 놓을 때, 어떤 두 말도 동일한 행, 열, 대각선에 오지 않도록 놓을 수 있는지 물어보는 문제이다.

체스의 퀸의 경우에는, 대각선, 직선 모두의 말들을 먹을 수 있으므로 이런 경우가 없도록 배치할 수 있는지 없는지에 대한 것을 반환하는 문제.

이 문제를 해결하기 위해서 다음과 같은 단계로 생각 할 수 있다.

- 한 행에 말을 하나씩 배치하면서 탐색

여러 말이 동일한 행에 놓일 수 없기 때문에, 결국 한 행에는 하나의 말만이 존제하게 된다.

- 더 이상 진행 할 수 없는 경우 BackTraking을 통해 바로 앞의 경우의 수에서 둔 말의 위치를 변경하고 다시 진행한다.

더이상 놓을 자리가 없는 경우, BackTraking을 통해 앞의 말을 다른 위치로 옮기고 계속 진행하며 이런 식으로 반복하여 모든 경우의 수를 탐색했을 때 모든 말을 둘 수 없다면 배치할 수 없는 경우가 된다.

N Queens Problem에서는 아래의 두가지 개념이 나온다.

### - BackTraking

어떤 결정들을 내려가다가, 더이상 진행 할 수 없을때 가장 최근에 내린 결정을 번복하고 다시 뒤로 돌아가 다른 선택을 하면서 문제를 해결하는 방법을 말합니다.

체계적으로 가시화 하여 문제를 해결하기 위해서
상태공간트리라는 개념을 사용한다.

되추적 기법이라고 하며, 상태공간 트리를 깊이 우선 탐색(dfs)로 탐색하여 해를 찾는다.

### - 상태공간트리

찾는 해를 포함하는 트리
상태공간 트리는 모든 경우의 수를 다 가지기 때문에
해가 존재한다면, 반드시 이 트리의 어떤 한 노드에 해당한다.
따라서 트리를 탐색하면 해를 구할 수 있다.

상태공간트리를 직접 코드상에 구현하는 것이 아니라
이 트리를 염두해 두고 문제를 해결하는 것이지 실제로 트리를 만드는 것은 아니다.

해답을 찾기 위해 모든 노드를 찾을 필요는 없다.
infeasible(non-promising)한 노드((1,1) -> (2,1)), ((1,1) -> (2,2))이런 식으로 가는 경우에는 이미 같은 열에 존재하기 때문에 해가 되기 위한 기본적인 조건을 위반하였기 떄문에 더이상 이 노드 밑으로 내려갈 필요가 없기 때문에 이런 노드는 더 찾을 필요가 없다.

간략한 슈도 코드를 작성한 뒤에 단계별로 내용을 채우면서 진행하자.

```java
return queens(parameter){
  if(non-promising){
    return failure;
  }else if success{
    return answer;
  }else{
    visit children recursively
  }
}
```

우선 위의 코드에서 말의 위치를 저장할 배열이 필요하므로 추가한다.

한 행에는 하나의 말만 놓일 수 있으므로 2차원 배열을 굳이 쓸 필요가 없다.

ex)

cols[1] : 1번말이 놓일 열 번호

cols[2] : 2번말이 놓일 열 번호

...

cols[N] : N번말이 놓일 열 번호

```java
int [] cols = new int[N + 1];
```

그 다음 함수가 받을 파라미터를 정해 줍니다.
```java
return queens(int level)
```

queens(3)이라면, 1번 말 부터 3번까지의 말은 이미 어디에 놓일지 정해졌다는 것을 뜻합니다.

1번 말 부터, level번째의 말이 어디에 놓였다는 것을 cols에 저장합니다.

그 다음으로는, 리턴 타입을 정해준다.
```java
boolean queens(int level)
```

이제 각 조건에 해당하는 코드를 구현합니다.
### 1. non-promising 체크 로직
노드가 어떤 경우에 non-promising인지를 판단해야 한다.

같은 열에 있는 경우 / 같은 대각선상에 존재하는 경우를 검출해야 한다.

```java
if(!promising(level)){
    return false;
}
```
위의 promising 메소드를 구현해야 한다.
```java
boolean promising(int level){
  for(int i=1; i<level; i++){
    if(cols[i]==cols[level]){
      return false;
    }else if(level-i==Math.abs(cols[level]-cols[i])){
      return false;
    }
  }
  return true;
}
```
### 2. 최종 답 체크 로직
최종 답이 되려면,
위의 if에서 promising 테스트를 통과하고
level이 N이어야 한다.
level이 N이라는 것은, 현재 놓인 말의 개수가 N개라는 것이기 때문에 이 경우에는 true를 반환하고 종료한다.
```java
else if(level==N){
  return true;
}
```
level==N이라면, N
### 3. 자식 노드를 Recursive하게 방문 하게 하는 로직

queens(level)이 호출 된 경우
1번째 열부터 말을 배치해서, queens(level+1)이 true를 리턴할 수 있는지를 체크한다.
만약 queens(level+1)이 false라면 현재 위치에 말을 배치했을 경우 답이 나오지 않는 경우기 때문에 다음 열에 배치해보고, 모든 열에 배치해도 답이 나오지 않는다면 false를 리턴합니다.
```java
else{
  for(int i=1; i<=N; i++){
    cols[level+1] = i;
    if(queens(level+1)){
      return true;
    }
  }
  return false;
}
```

## 최종 로직 구현

위의 코드들을 전부 합쳐서, 아래와 같은 n queens Problem 코드를 작성 할 수 있다.

처음 호출은 level 0부터 시작하기 때문에 queens(0)로 호출한다.

답은 2차원 배열 형태로 출력해 줄수도 있지만, 1차원 배열로 답을 구했기 때문에 간단하게 출력했다.
```java
public static final int N = 8;
public static int cols[] = new int[N + 1];
public static void main(String[] args) {
	queens(0);
}
public static boolean queens(int level) {
	if(!promising(level)) {
		return false;
	}else if(level==N) {
		for(int i=1; i<=N; i++) {
			System.out.println("(" + i + "," + cols[i] + ")");
		}
		return true;
	}else {
		for(int i=1; i<=N; i++) {
			cols[level + 1] = i;
			if(queens(level + 1)) {
				return true;
			}
		}
		return false;
	}
}
public static boolean promising(int level) {
	for(int i=1; i<level; i++) {
		if(cols[level] == cols[i]) {
			return false;
		}else if(level-i == Math.abs(cols[level] - cols[i])) {
			return false;
		}
	}
	return true;
}
```
