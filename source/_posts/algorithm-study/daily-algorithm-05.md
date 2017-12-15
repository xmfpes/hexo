---
title: 알고리즘 스터디 - 05(Recursion - 재귀를 이용한 Counting cells in a blob 알고리즘)
date: 2017-09-26 10:36:19
tags: 
- Algorithm
- Counting cells in a blob
categories: 
- CS
- Algorithm
- Study
---

## 매일 하려고 하는 알고리즘 스터디 - Recursion

![algorithm](/images/algorithm.png)

### Day 5
## Recursion 응용을 이용한 Counting cells in a blob

- ### Recursion을 이용한 Counting cells in a blob 문제 해결하기

blob이란 서로 연결된 image pixel들의 집합을 각각의 blob이라고 한다.


![blob](/images/algorithm/blob.png)

위의 이미지에서, 파란 이미지가 image pixel이며
blob은 상하좌우 및 대각방향으로 이어진 image pixel의 집합이며 
위의 이미지에서 총 4개의 blob이 존재하게 된다.

입력 : N*N 크기의 2차원 그리드(x, y)
출력 : (x,y)가 포함된 blob의 크기, 속하는 blob이 없으면 0

blob을 구하는 과정에서는 다음과 같은 경우가 있다.

#### - 입력받은 (x,y) 픽셀이 이미지가 아닌 경우

  이 픽셀은 이미지 픽셀이 아니므로 blob 일리가 없으므로 0을 리턴해주면 된다.
#### - 입력받은 (x,y) 픽셀이 이미지 픽셀인 경우
  - 이미지 픽셀 인 경우, 현재 픽셀을 먼저 카운트 해준다. (count+=1)
  - 이후 현재 픽셀의 중복 카운트를 막기 위해서, 다른 색으로 칠해준다.
  - 현재 픽셀에 이웃한(상, 하, 좌, 우, 대각선)에 대해 그 픽셀들이 속한 blob의 값을 더해 카운트 해준다.


```java
public class Recursion_day05 {
	public static final int N = 8;
	public static final int COUNTED = 2;
	public static int pixel[][] = {
			{1,0,0,0,0,0,0,1},
			{0,1,1,0,0,1,0,0},
			{1,1,0,0,1,0,1,0},
			{0,0,0,0,0,1,0,0},
			{0,1,0,1,0,1,0,0},
			{0,1,0,1,0,1,0,0},
			{1,0,0,0,1,0,0,1},
			{0,1,1,0,0,1,1,1}
	};

	public static void main(String[] args) {
		System.out.println(countingCells(6,4));
	}
	
	public static int countingCells(int x, int y) {
		if(x < 0 || y <0 || x > N - 1 || y > N -1) {
			return 0;
		}else if(pixel[x][y] != 1) {
			return 0;
		}else {
			pixel[x][y] = COUNTED;
			return 1 + countingCells(x, y+1) + countingCells(x+1, y+1) + 
					countingCells(x+1, y) + countingCells(x+1, y-1) + 
					countingCells(x, y-1) + countingCells(x-1, y-1) + 
					countingCells(x-1, y) + countingCells(x-1, y+1);
		}
	}
}

```

위의 코드를 통해, 입력받은 픽셀에 포함된 blob의 값을 리턴 받을 수 있다.
이미 카운트만 픽셀의 값을 바꿔주는것만 빼먹지 않는다면, 미로찾기를 응용하여 간단하게 구현할 수 있다.


오늘 학습에 사용된 코드는 깃허브 [알고리즘 레파지토리](https://github.com/xmfpes/daily-algorithm/commit/f57f5b664629d4f6fe884d3ff2165cbba8481917)에 커밋 로그로 남겨두었습니다.
