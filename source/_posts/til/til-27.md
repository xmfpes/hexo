---
title: TIL-27
date: 2017-11-16 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일

- ### Trello 프로젝트 시작

간단히 Spring boot 기반에서 컨트롤러를 만들고, 회원가입 기능과 로그인 기능을 구현했다.

스프링을 오랜만에 잡았더니..간단한 로직 구현에도 상당히 애를 먹었다.



오늘도 테스트 코드를 만드는 과정에서 원인을 알 수 없는 에러가 발생했는데 Test 코드 내에서 userRepository를 Autowired로 받아오고 save를 한 후에 로그인 로직을 구현했더니 이상한 에러가 발생했다.

```java
@Test
  public void login() throws Exception {
	  UserDto user = new UserDto("xmfpes", "123@naver.com", "1234");
	  userRepository.save(new User(user));
	  createRestAssured(new UserDto("xmfpes", "1234"), "/login", HttpStatus.OK);
  }
  
  public void createRestAssured(Object obj, String url, HttpStatus status) {
	  given()
      .contentType(ContentType.JSON)
      .body(obj)
    .when()
      .post(url)
    .then()
      .statusCode(status.value());
  }
```

