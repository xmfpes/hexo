---
title: TIL-1
date: 2017-10-16 15:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

## **10/16 Today I Learned(TIL)**

![til](/images/til/til.jpg)

## 오늘 한 일
- ### [HTTP 웹 서버 구현 실습](https://github.com/xmfpes/java-was/commit/26fde28bd9450a3d44ff02d4af676848cae9db5e)

오늘은, 기존의 프레임워크를 사용하지 않고 직접 HTTP Request Header를 파싱해서

해당 요청에 맞는 페이지를 보여주거나, 서버에서 처리하는 것을 직접 구현해보았습니다.

요구하는 기능에 대해서는 모두 제작하였지만 문자열 처리나, 요청에 대해 처리하는 부분이 조금 지저분한데 이를 조금 더 깔끔하게 고칠 수 있도록 해야 할 것 같습니다.

### 제작 기능

- HTTP Request Header 출력해보기
- HTTP Request Body 값을 추출해 회원가입 기능 구현하기
- HTTP status 302 Redirect 기능 구현
- Cookie header값 주어서 로그인 기능 구현
- 로그인 인증 기능 추가(비로그인 시 리다이렉트로 로그인 페이지 이동)
- HTTP Request Header Content-Type 처리 기능

- ### [알고리즘(Heap Sort)](https://xmfpes.github.io/algorithm-study/daily-algorithm-11/) 


### 제작 기능

- Heap Sort 단위 별 기능 나누기(힙 Build, 힙 재 구성, 힙 소트)
- Heap Sort 단위 별 기능 메소드 자바로 구현하기


## 느낀점

- #### 프레임워크가 얼마나 편한 기능들을 제공하는지 알게 되었다.
- #### Heap Sort를 기능 단위별로 나눠 구현하니 생각보다 어렵지 않았다.


## 내일 할 일

- #### HTTP 웹 서버 구현
- #### 알고리즘
- #### 코딩 테스트