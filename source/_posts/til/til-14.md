---
title: TIL-14
date: 2017-10-30 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일


- ### MVC 프레임워크 구현 1단계 완료

최대한 컨벤션을 지키려고 노력하면서 구현했더니, 첫번째 풀 리퀘스트에서 codacity의 수정 사항이 5개밖에 나오지 않았다. 구현하면서 거슬렸던 점들을 포비와 리팩토링 하면서 다 제거할 수 있었고 최종적으로는 codacity에서 하나도 문제가 발생하지 않아 기분좋게 구현을 끝낼 수 있었다.

- ### JDBC 리팩토링

JDBC 코드 리팩토링 실습을 진행했다. 익명 클래스를 사용한 경험이 많지 않은데, 아래와 같은 경우에는 User를 파라미터로 주지 않고도 user의 값을 이용할 수 있어 익명 클래스를 활용해 구현하는 경우 User 파라미터를 넘기지 않아도 되고, 매번 다른 쿼리에 대해 쿼리와 pstmt만 설정해 주면 되서 좋은 것 같다. 이런 생각은 하지 못했는데 필요한 경우가 있다면 적용해봐야 겠다.

```java

public void insert(User user) throws SQLException {
		JdbcTemplate insertJdbc = new JdbcTemplate() {
			public String createQuery() {
				return "INSERT INTO USERS VALUES (?, ?, ?, ?)";
			}
			public void setValueforInsert(PreparedStatement pstmt) throws SQLException{
				pstmt.setString(1, user.getUserId());
				pstmt.setString(2, user.getPassword());
				pstmt.setString(3, user.getName());
				pstmt.setString(4, user.getEmail());
			}
		};
		insertJdbc.insert(user);
	}
```

추상 클래스 / 추상 메소드 / 익명 클래스 / 인터페이스 활용 등 다양한 자바 문법을 접할 수 있는 굉장히 좋은 실습 예제인것 같다. 단순한 JDBC를 이렇게까지 리팩토링 할 수 있구나 라는 생각이 들었고 내일 반복해서 학습하고 적용할 수 있는 것은 코드에 즉각적으로 적용해봐야겠다.



## 내일 할 일

- ### JDBC 리팩토링 한번 더 해보기, 처음부터 정리해보기

- ### 가능하다면 XHR, AJAX 진행하기

  ​
  ​


- - -



오늘 JDBC 리팩토링을 하면서 자바 문법에 대해 많이 부족하다는 것을 한번 더 느꼈다. 그리고 hue와 함께 jin의 로또 코드를 보면서 상속 관련 이슈를 접했는데 상속이나 이런 개념들을 사용함에 있어서 내부적으로 어떻게 돌아가는지 조금 더 자세히 알고 써야 할 것 같다.

Lotto의 자식 클래스에서 private int bonus = 0;으로 선언해 준 부분에서 bonus번호가 할당 되고 나서도 출력이 제대로 되지 않는 이슈가 있었다. 

Lotto의 생성자에서 makeLotto메소드를 호출하는데, 이 호출한 시점에 makeLotto는 Override된 자식의 메소드 였고, 거기서 bonus 번호가 할당 되지만 부모의 생성자가 호출 된 이후에 자식의 생성자가 호출되면서 bonus에 0이 재 할당되서 생긴 문제였던것 같다. 정확히 맞는지는 모르겠지만, 출력 순서를 고려하면 저게 문제였던것 같다.

코드가 정확히 기억나진 않지만 아래와 같은 구조였던것 같다.

```java
WinningLotto extends Lotto{
  private int bonus = 0;
  public WinningLotto(){
    
  }
  @Override
  public void makeLotto(){
    
  }
}
Lotto {
  public Lotto(){
    makeLotto();
  }
}
lotto = new WinningLotto();
```

WinningLotto가 생성되는 순간 Override된 makeLotto가 호출되고, bonus가 할당 된 이후 자식의 생성자가 호출되면서 bonus가 0이 되는것 같은데, 어떻게 보면 당연한 것이지만 남이 짠 코드를 보면서 무엇이 잘못됐는지 찾는건 상당히 어려웠다.



기회가 된다면 다른 사람들이 짠 코드를 보거나, 이슈를 같이 해결해보면서 많은 지식을 쌓아야겠다.