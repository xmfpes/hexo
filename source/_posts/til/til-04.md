---
title: TIL-4
date: 2017-10-19 15:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

# **10/19 Today I Learned(TIL)**

![til](/images/til/til.jpg)

# 오늘 한 일

- ## 웹 서버 구현 리팩토링

오전중에 HttpParsing을 담당하는 클래스를 추출해, Http 헤더와 바디 정보를 저장하는 클래스를 생성하고 RequestHandler내에 존재하는 Request와 Response의 처리를 따로 클래스를 추출해서 넘기는 리팩토링 작업을 진행했습니다.

리팩토링 이후에 피드백을 받으면서 기존에 url을 if, els로 구분하는 코드에 대해 개선점을 생각해 볼 수 있었고 Map을 이용해 url 키 값을 이용해 구분을 진행했습니다.

```java
private Map<String, RequestMapper> requsetStrategy;

this.requsetStrategy.put("GET",new GetMapper());
this.requsetStrategy.put("POST",new PostMapper());
```

RequestHandler 내부에 위의 맵을 가지고, 맵의 키 값에 GET, POST를 지정해 바로 해당하는 처리를 담당하는 클래스의 run을 실행하여 처리해주었습니다.

추후에 PUT이나 DELETE를 추가할 것도 고려해서 구현했습니다.

- ### 추가 수정 사항
  - GET 메소드에서 html이나 css파일을 리턴해주는 경로는 그 경로를 그대로 처리하면 되는데 그 디폴트 처리와 특정 경로의 처리를 구분해 주는 작업을 if / els로 처리해 준 점
  
  - Response 관련된 처리를 제대로 분리해주지 않은 점, 메소드마다 일일히 Response 헤더를 텍스트로 입력해 주는 과정에서 생기는 중복 처리를 제거하지 않은 점
  
  - 약간 현재 구조가 이상한 것 같은데 이에 대한 리팩토링




- ### Codility 알고리즘 문제 3개 해결

제한시간 2시간짜리의 문제 3개를 해결했습니다.

BinaryGap

CyclicRotation

OddOccurrencesInArray

자바의 자료구조를 이용해 쉽게 구현한 문제도 있었습니다.




## 오늘 부족했던 점

- 웹서버 구현 중 부족한 HTTP에 대한 학습 부족

- 알고리즘 강의 수강

문제는 꾸준히 풀고 있는데, 이론에 대한 부분이 계속 밀리면서 부족해 지는 것 같다.

- 리액트 강의 수강

짧은 강의라 하루 하나씩이라도 보려고 하는데, 강의를 보는게 힘들다.


## 앞으로 해야 할 일들


- 알고리즘 강의 매일 1개 수강하기

- 넥슨 알고리즘 문제 못푼거 하나 정리하기

- Node.js 스터디 기간동안 공부했던 내용 정리와 마무리 짓기

스터디를 마무리 하기 전에, 그래도 공부한 내용을 빨리 찾아 적용할 수 있도록 블로그에 포스팅 형태로 다 정리해서 올리기..한참 남음

- JavaScript 공부하기

서버 개발이 더 중요하지만, 최소한의 프론트엔드 구현을 위한 자바스크립트 공부(스코프, 프로토타입, es6 문법 등등등)

- 리액트 혹은 앵귤러 공부

팀원들과 구현하기로 한 프로젝트의 프론트엔드를 리액트 / 앵귤러로 구현하기로 했으므로 공부하기

할게 많다

- 구상 프로젝트 구현하기