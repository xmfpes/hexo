---
title: TIL-12
date: 2017-10-27 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일

### Servlet QnA

오늘은 Servlet에 대해 학습한 내용 중 궁금한 내용에 대해 QnA를 진행했습니다. 저번 Http때도 그렇지만, 많은 정보들을 얻을 수 있는 시간인 것 같습니다. 

- Servlet에서 동시성

하나의 Servlet을 A가 사용 중이어도 B가 사용할 수 있다. 인스턴스의 참조를 가지고 스레드 내에서 실행되기 때문인 것 같다.

Servlet의 메소드 내에 로컬 변수는 스택 영역에 있어 각 Servlet마다 별개로 값을 처리하지만, 인스턴스를 생성하면 Heap 영역에 참조 변수 형태로 저장되어 여러 유저들이 인스턴스의 값을 쓸 경우 값이 보장되지 않는다.? 맞나?

Servlet 내부에 로컬 변수가 아닌 인스턴스를 생성하는 경우를 최대한 피하고, 피치못하게 사용해야 하는 경우 동기화 처리를 해주어야 한다.

- Servlet Container의 Servlet 생명 주기 관리

Spring의 bean처럼 Servlet도 프로그래머가 관리를 해주지 않음, IoC(Inversion of Control) 제어의 역전 개념이 나온다

- Singleton보다 DI 형태가 테스트 등에 좋다.

Singleton 패턴으로 Instance를 하나만 생성하는 것은 테스트에 어려움이 있다. 그래서 Spring에서 DI의 개념이 등장하게 됨. 클래스 연결을 외부에서 하기 때문에 기능을 바꿔치기 해서 테스트 할 수 있다.



### 구름 테스트 문제 풀기

문제가 간단해서 금방 풀었다. 저번 nhn때도 구름 문제를 풀어봤지만..실제 문제는 어려웠는데 들어가고 싶은 기업인 만큼 잘 쳤으면 좋겠다.


- - -

## 내일 할 일

- ### 우아한형제들 코딩 테스트


- ### MVC 프레임워크 구현 1단계 구현하기
