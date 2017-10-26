---
title: 알고리즘 스터디 - 04(Recursion - 재귀를 이용한 미로찾기 알고리즘)
date: 2017-09-25 13:36:19
tags: 
- Algorithm
- Maze
categories: 
- CS
- Algorithm
- Study
---

## 매일 하려고 하는 알고리즘 스터디 - Recursion

![algorithm](/images/algorithm.png)

### Day 4
## Recursion 응용을 이용한 미로찾기 알고리즘 구현하기

- ### Recursion을 이용한 미로찾기 알고리즘 구현

미로찾기의 경우에는, 결과가 yes or no로 나오고
이런 문제를 Decision Problem 이라고 한다.

```java
private static int[][] maze = {

  { 0, 0, 0, 0, 0, 0, 0, 1 },
  { 0, 1, 1, 0, 1, 1, 0, 1 },
  { 0, 0, 0, 1, 0, 0, 0, 1 },
  { 0, 1, 0, 0, 1, 1, 0, 0 },
  { 0, 1, 1, 1, 0, 0, 1, 1 },
  { 0, 1, 0, 0, 0, 1, 0, 1 },
  { 0, 0, 0, 1, 0, 0, 0, 1 },
  { 0, 1, 1, 1, 0, 1, 0, 0 }
};
```

위와 같은 미로가 있다고 가정하자, 1은 벽이고
maze[N-1][N-1]까지 도달하는 경로를 찾는 것이 목적인 문제이다.

위의 미로를 탈출하는 경로를 찾는 알고리즘을 재귀함수를 이용해 구현해 보자.

현재 위치에서 출구까지 가는 경로가 있으려면

- 현재 위치가 출구
```java
if(x == N-1 && y == N-1){
  return true;
}
```
- 이웃한 셀 들 중 하나에서 현재 위치를 지나지 않고 출구까지 가는 경우가 있는 경우
```java
if(findMazePath(x-1, y) || findMazePath(x, y + 1) ||
 findMazePath(x + 1, y) || findMazePath(x, y - 1)) {
				return true;
			}
```

위의 경우에서 주의할 점은, 무한으로 재귀가 호출 될 수 있다는 점이다.
Recursion 메소드를 작성 할 때 무한루프에 빠지지 않도록 조건을 잘 만들어야 한다.
Recursion 수행 중 Base case에 무조건 수렴 할 수 있는 조건을 주어야 한다.

위의 조건에서는 x,y에서 인접한 셀인 x-1, y로 재귀를 호출 하는 경우 x-1, y에서도 인접한 x,y의 위치로 재귀를 호출하기 때문에 무한 루프에 빠질 수 있고, 이를 방지하려면 지나온 경로들에 대해서 지나갔다고 표시를 해 주어야 한다.

```java
private static final int PATHWAY_COLOR = 0;
private static final int WALL_COLOR = 1;
private static final int BLOCKED_COLOR = 2;
private static final int PATH_COLOR = 3;
```

위에서 0은 미로에서 지나갈 수 있는 길을 뜻하고, 1은 벽을 뜻하며
2는 출구까지의 경로가 없는 길을 표시하고
3은 출구까지의 경로를 뜻한다.
재귀호출 결과가 나오기 전까지는, 결과를 3으로 두고, 만약 출구까지의 길이 없는 곳이라면 3을 2로 변경해주는 방법으로 경로를 표시할 수 있다.

Recursion을 설계할때, 현재 방문한 셀이 벽이 아닌지, 방문한 곳이 아닌지를 체크하고
만약 벽이거나 방문된 위치이면 즉시 false를 리턴하고 종료하는 식으로 설계한다.

아래는 완성된 미로탐색 알고리즘이다.

```java
private static final int N = 8;
private static int[][] maze = { { 0, 0, 0, 0, 0, 0, 0, 1 }, { 0, 1, 1, 0, 1, 1, 0, 1 }, { 0, 0, 0, 1, 0, 0, 0, 1 },
    { 0, 1, 0, 0, 1, 1, 0, 0 }, { 0, 1, 1, 1, 0, 0, 1, 1 }, { 0, 1, 0, 0, 0, 1, 0, 1 },
    { 0, 0, 0, 1, 0, 0, 0, 1 }, { 0, 1, 1, 1, 0, 1, 0, 0 } };

private static final int PATHWAY_COLOR = 0;
private static final int WALL_COLOR = 1;
private static final int BLOCKED_COLOR = 2;
private static final int PATH_COLOR = 3;

public static void main(String[] args) {
  findMazePath(0,0);
  for(int i=0; i<N; i++) {
    for(int j=0; j<N; j++) {
      System.out.print(maze[i][j] + " ");
    }
    System.out.println();
  }
}

public static boolean findMazePath(int x, int y) {
  if(x < 0 || y <0 || x > N - 1 || y > N - 1) {
    return false;
  }else if(maze[x][y] != PATHWAY_COLOR) {
    return false;
  }else if(x==N-1 && y==N-1) {
    maze[x][y] = PATH_COLOR;
    return true;
  }else {
    maze[x][y] = PATH_COLOR;
    if(findMazePath(x-1, y) || findMazePath(x, y + 1) ||
        findMazePath(x + 1, y) || findMazePath(x, y - 1)) {
      return true;
    }
    maze[x][y] = BLOCKED_COLOR;
    return false;
  }
}
```

위의 미로 출구 찾기 코드에서는 0,0부터 미로 찾기 탐색을 시작하며, 재귀호출을 통해 서, 북, 동, 남 순서로 탐색하며 만약 미로의 범위를 넘어선 좌표가 들어오거나, 지나가지 않은 셀(값 0)이 아니라 이미 지나갔거나 벽인 경우에 false를 리턴한다.

출구일 경우 바로 true를 리턴해 주며 재귀를 종료하고 그 이외의 경우에서는 각각 서 북 동 남 순서로 재귀 호출을 진행한다.
만약 서 북 동 남 중에서 출구로 가는 경우를 가진 경우가 있다면 각각의 경로를 PATH_COLOR로 바꾸어 출구까지의 경로를 표시하고, 만약 그런 경로가 존재하지 않는다면 해당 셀도 BLOCKED_COLOR로 변경해 준다.

정상적으로 findMazePath 메소드를 작성하고, 메인에서 실행한다면 아래와 같은 결과값이 출력될 것이다.

```java
public static void main(String[] args) {
		findMazePath(0, 0);
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				System.out.print(maze[i][j] + " ");
			}
			System.out.println();
		}
	}
```
```
3 2 2 2 2 2 2 1
3 1 1 2 1 1 2 1
3 2 2 1 2 2 2 1
3 1 2 2 1 1 2 2
3 1 1 1 2 2 1 1
3 1 3 3 3 1 2 1
3 3 3 1 3 3 3 1
0 1 1 1 0 1 3 3
```

여기서 출구까지의 길이 PATH_COLOR 값인 3으로 표시되어 출구까지의 길이 표시된다.

오늘 학습에 사용된 코드는 깃허브 [알고리즘 레파지토리](https://github.com/xmfpes/daily-algorithm/commit/2baee40bddff5ee551aa9effdf44c1ada95dbf78)에 커밋 로그로 남겨두었습니다.
