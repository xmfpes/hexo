---
title: TIL-10
date: 2017-10-25 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일

- ### Servlet과 JSP


Embedded Tomcat과 Servlet 환경 설정을 진행하고 난 다음에 넥스트 스텝 실습을 진행했습니다. 2년 전 쯤에 아주 얕게 해봐서 조금 당황하긴 했지만 HTTP 웹 서버 구현을 하면서 구동 방식에 대해 알게 되어서인지, 생각보다 금방 할 수 있었습니다.

Tomcat(Servlet Container) 동작 방식에 대해서도 알 수 있었고 매번 예제 코드를 치거나, 가져다 쓰면서 JSP와 JSTL, EL이 뭔지 정확히 몰랐는데 오늘 대략적으로 알 수 있었습니다.

하지만 아직 정확히 잡히지 않은 개념들이 있는데 내일 확실히 질문할 생각입니다.



아직 헷갈리는 부분들

- Tomcat이 실행되는 시점에 프로젝트 내의 Servlet들을 생성하고 Servlet에 해당하는 해당 요청 시 스레드에 Servlet이 들어가 req, res를 받아서 실행된 뒤 해당 스레드가 종료되는 방식인 것 같은데, 이해한 것이 맞는지 모르겠음
- 구현하면서 session.setAttribute("user", user)를 통해 로그인, session.removeAttribute("user")를 통해 로그아웃을 했는데 이해한 내용이 맞는지 확실히 모르겠다. "user" 값을 통해 세션 정보를 가진 Map으로 접근하고, 그 안에 있는 Map에 접근할 때 sessionId값을 키로 사용하는 걸까? 
- 로그인 한 후에 Response 헤더에 Cookie:JSESSIONID=330B0A9853E012C08C106294B20F7F5F 값이 찍히는 걸 보면, 세션에 User 정보를 할당하고 응답에 SetCookie로 세션 아이디 값을 주는 것 같은데 내가 이해한게 맞나?
- 이번에 HttpSession이나 HttpServletRequest 등을 이용하면서 메소드 등을 확인하고 싶은데 인터페이스라 인터페이스의 구현체를 확인해야 하는데 이클립스 상에서 그 인터페이스를 구현한 구현체들을 볼 수 있는 방법은 없을까?
- HttpSession을 파라미터로 하는 SessionManager 클래스의 테스트는 어떻게 하면 좋을까? 파라미터로 인터페이스를 받기 때문에 구현체가 어떤 건지 정확히 몰라서 테스트를 어떻게 해야 할지 모르겠다.


그동안 얼마나 네트워크에 대해 모르고 웹 개발을 했는지 이번 기회를 통해 알 수 있었습니다. 전공자이지만 같이 레벨 4를 진행하는 비전공자 두분보다 네트워크나 전공 지식에 대해 뒤떨어지는것 같아 조금 부끄럽습니다.



- - -


## 내일 할 일

- ### ServletFilter 살펴보기, 이때까지 진행한 Servlet과 Http 내용 정리하기

그동안 진행한 내용에 대해서 조금 정리를 하면서 진행해야 할 것 같습니다. 내일로 Servlet은 마무리 하고 이번주 후반부에는 기대하고 있는 MVC 프레임워크 구현에 들어갈 것 같습니다.



- ### Aws Lambda로 slack 봇이나, api 만들어보기

옆에서 람다를 활용해 여러 봇을 만들고 있는데, 관심이 가던 분야이기도 해서 하루나 이틀정도를 투자해 이번 기회에 공부할 생각입니다. 초기 학습 비용이 조금 클 것 같지만 구현해 두면 유용하게 사용할 수 있을것 같습니다.



- ### React와 Spring Boot 연동해보기

저번에 배웠던 리액트 기초를 벗어나, Java Spring과 연동해 볼 생각입니다.



- - -



최근들어 전공자이지만, 전공 지식에 대해 부족함을 많이 느낍니다. 열심히 해야겠습니다.

그리고 오늘은 학원 부근으로 저번 화이트과정을 같이 한 재민씨가 놀러와서 부득이하게 오후는 제대로 공부를 하지 못했습니다.



