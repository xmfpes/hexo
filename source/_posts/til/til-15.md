---
title: TIL(10/31)
date: 2017-10-31 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일


- ### JDBC 리팩토링 완료


오늘은 JDBC 리팩토링을 모두 끝냈다. 어제 마지막 단계를 앞두고, 자바의 문법 습득에 도움이 되는 내용이 많은 것 같아서 지우고 다시 진행했다. 전부 다 요구하는 명세대로 구현한 거 같은데, 정확히 이게 맞는지는 모르겠다.

```java
public void update(String query, Object ...obj) {
  try(Connection con = ConnectionManager.getConnection();
      PreparedStatement pstmt = con.prepareStatement(query);){
    createPreparedStatementSetter(obj).setValues(pstmt);
    pstmt.executeUpdate();
  }catch(SQLException e) {
    throw new DataAccessException(e);
  }
}

public PreparedStatementSetter createPreparedStatementSetter(Object...obj) {
  return (pstmt) -> {
    int index = 1;
    for(Object o : obj) {
      pstmt.setString(index++, (String)o);
      //이게 이렇게 객체가 아닌 String을 받아오는게 맞나?
    }
  };
}
```

위의 경우는 Object를 가변 인자로 받는데 포비가 의도한 방식이 위와 같은 방식일까?

이번에도 PR을 보냈는데 Codacy에서 문제가 있는 코드가 하나도 검출되지 않았다. 계속 반복적으로 코드 컨벤션을 지키려고 노력하다 보니 의식하지 않아도 어느정도 컨벤션을 지키면서 개발하게 되는 것 같다.

- ### TDD 수업

오늘 오전에 TDD에 대해 수업했다. 오전에 컨디션이 너무 좋지 않아서 집중을 하나도 못했지만.. 중간에 정신을 차리고 열심히 들었다. 정말 도움이 되는 수업이었을텐데 집중하지 못해서 조금 아쉽다. TDD가 테스트 기반 개발인건 알았지만, 원래 테스트 코드를 먼저 짜고 클래스를 만들고, 메소드를 만든다는건 알지 못했었는데 앞으로 코드를 짜면서 최대한 의식적으로 테스트 코드를 먼저 짜고 클래스를 설계하는 습관을 길러야겠다.

오늘 실습 코드 중에 BiFunction을 사용하는 예제를 접했는데 흥미로웠다. 맵에 인터페이스 형태로 넣어 구현한 적은 꽤 있지만 BiFunction이라는 인터페이스를 이용해 본 적은 없는데 간단한 구현이라면 이 인터페이스를 이용해 구현해보아도 될 것 같다.



## 내일 할 일

- ### TDD 실습 과제 수행하기

테스트 코드를 먼저 짜면서 주어진 과제를 수행해보자.

- ### JDBC 리팩토링 다시 한번 해보기

오늘 다시 해보면서 도움이 많이 되는 것을 느꼈는데, 다시 한번 해보고싶다. 아직까지 Exception에 대해 개념이 덜 잡힌것 같은데 관련 내용도 학습해야겠다.

- ### 면접 준비하기

지원 자격 자체에 프론트엔드 관련된 내용이 많았고 우대사항에도 프론트엔드 관련 프레임워크나 es6, TypeScript등이 대부분이라 크게 기대는 하지 않지만 면접을 이왕 보는거 최선을 다해야겠다.

------



내일은 다 같이 소개? 비슷하게 진행하면서 약간 학습할 분위기가 아닐 것 같은데 간단하게 TDD 예제를 테스트 코드를 짜면서 진행해봐야 겠다. 그리고 남는 시간에는 면접 준비를 조금 해봐야겠다. 배울 건 아직 많은것 같은데 아무래도 돈 받으면서 배우는게 더 좋지 않을까. 이번주는 면접, 인적성, 약속 등이 많이 잡혀있고 이번에 부산도 내려가야해서 학습할 시간이 조금 부족할 것 같다.