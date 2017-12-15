---
title: TIL-8
date: 2017-10-23 15:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

# 오늘 한 일
- ## 람다와 인터페이스를 이용한 리팩토링

```java
//람다를 적용하지 않은 익명 클래스를 이용한 인터페이스 구현
public static int sumAll(List<Integer> numbers) {
  return sum(numbers, new Conditional() {
    @Override
    public boolean test(Integer number) {
      return true;
    }
  });
}

//람다와 인터페이스를 이용하 리팩토링
public static int sumAll(List<Integer> numbers) {
	    return sum(numbers, number -> true);
	}
	
	public static int sumAllEven(List<Integer> numbers) {
		return sum(numbers, number -> number %2 == 0);
	}

	public static int sumAllOverThree(List<Integer> numbers) {
	    return sum(numbers, number -> number > 3);
	}
	
	public static int sum(List<Integer> numbers, Conditional con) {
		int total = 0;
		for(int number : numbers) {
			if(con.test(number)) {
				total+=number;
			}
		}
		return total;
	}
	
	public interface Conditional {
	  boolean test(Integer number);
	}
```

인터페이스를 이용해 웹 서버 리팩토링도 진행하고, 체스도 구현해봤지만
위의 내용을 과제로 진행하면서 인터페이스가 아직 까지는 익숙치 않다는 것을 느꼈습니다.
오늘 구현을 통해 조금 더 인터페이스를 어디에 적용해야 할지 알게 된 것 같고
앞으로는 간단한 구현이라도, if els 대신에 인터페이스를 적용해 구현해 볼 생각입니다.

인터페이스 뿐만 아니라 익명 클래스나 이런 개념에 대해서도 익숙치 않고
람다나 스트림 등, 자바에서도 아직 배울게 산더미라는걸 느꼈습니다..

- ## 자바 람다와 스트림에 대해 정리
[람다 스트림 정리](https://xmfpes.github.io/java/java-se8-syntax/)

기존의 정리한 내용에 조금 추가해서 정리했습니다. collect, reduce, 매칭 등 최종 처리와 중간 처리 등에 대해 정리했습니다. 
스트림으로 대단히 많은 일을 할 수 있다는걸 알게 됐습니다. 특히 collect 메소드는 Map, Set,List 등 대부분 주로 사용하는 리스트를 바로 뽑아올 수 있고, 익숙해진다면 여러 응용을 통해 유용하게 사용할 수 있을것 같습니다.

- ## HTTP 
[HTTP](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Basics.html)

next step의 http 관련 링크와 책을 조금 읽어보았습니다. 관련 내용을 조금 보기 쉽게 정리해두어야 할 것 같습니다.

- ## 스트림을 이용한 달팽이 배열 문제

1시간 정도 어떻게 풀어야 할지에 대해서 고민을 해봤는데 감이 전혀 잡히지 않아 먼저 스트림에 대해 공부하고, 반복문으로 구현한 후에 옮겨볼 생각입니다.

- ## 웹 Servlet / Embedded Tomcat 환경 프로젝트 셋팅하기

Embedded Tomcat을 설정하고 Servlet의 doGet을 이용해 페이지를 로드하는 부분까지 진행했습니다.

## 내일 할 일

- ## Hexo 블로그 변경하기
  - 현재 블로그가 코드 가독성이 떨어지는 것 같아 변경하려고 생각하고 있습니다.
- ## nextstep Servlet 내용 계속 진행하기
- ## HTTP 웹서버 리팩토링
  - 채점된 항목 중 컨벤션에 맞지 않는 잘못된 코드들 수정해 다시 커밋하기
  - Reponse 관련 리팩토링에 대해 감이 잡히지 않는데, MVC 프레임워크처럼 간편하게 쓸수 있도록 하려면 어떻게 구현해야 할지 고민해보기
