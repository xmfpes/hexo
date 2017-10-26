---
title: Spring Boot 프로젝트 초기 설정하기
date: 2017-09-21 15:10:03
tags:
- Spring Boot
- Jpa
- thymeleaf
categories:
- Java
- Spring
thumbnail: /images/spring.png
---

## **Spring Boot 프로젝트 초기 설정하기**

![spring](/images/spring.png)

MySQL /
Lombok /
thymeleaf /
JPA(ORM) /
Maven
환경의 프로젝트 셋팅 예제입니다.

## STEP 1. 프로젝트 생성하기

STS에서

Spring Starter Project 생성

Name, Package, Group을 설정하고

1.5.4 Version

DevTools, Lombok, Web, Thymeleaf, MySQL, JPA를 선택하고 프로젝트 생성

Lombok 이클립스에 추가 설정하기

https://projectlombok.org/

위의 사이트에서 lombok.jar 파일을 다운로드 받은 뒤
java -jar lombok.jar를 이용해 파일 실행

현재 사용하는 Eclipse가 나타난다면, 체크한 후 Install/Update를 클릭하여 셋팅

정상 설치된다면 Eclipse 내부에서 lombok.jar 파일이 추가 된 것을 확인할 수 있다.

## STEP 2. 서버 연결 테스트 및 Mysql 연결 및 Lombok 테스트

#### 1. 서버 동작 확인

.controller 패키지를 만들고

안에 HomeController 클래스를 추가한 후 아래의 코드를 작성한다.
```java
@Controller
public class HomeController {
	@GetMapping
	public String Home() {
		return "index";
	}
}
```
현재 thymeleaf 템플릿 엔진을 사용 중이므로, /template 경로 안에 연결될 index.html 뷰 파일을 생성한 후 아무 내용이나 작성한다.

이후 서버를 구동하고 localhost:8080/ 경로에서 inded에 작성한 내용이 출력되면 정상적으로 연결 된 것이다.

#### 2. MYSQL 연결하기

Mysql과 Spring Boot를 연동하기 위해서

application.properties에 아래의 코드를 추가한다.
```
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.url=jdbc:mysql://localhost:3306/DBNAME?autoReconnect=true&useSSL=false&useUnicode=true&characterEncoding=utf8
spring.datasource.username=USERNAME
spring.datasource.password=USERPASSWORD
```

위와 같이 작성하고 서버 실행 시 정상 작동된다면, 연결이 정상적으로 된 것이다.

#### 3 Lombok Getter Setter 테스트

Lombok 테스트를 위해 domain 패키지를 추가하고

안에 SampleVO를 만들고 코드를 작성한다.
```java
@Getter
@Setter
@ToString
public class SampleVO {
	private String val1;
}
```

컨트롤러에서 SampleVO를 사용해서 Getter와 Setter가 정상 작동 되는지 체크하자
```java
@Controller
public class HomeController {
	@GetMapping
	public String Home() {
		SampleVO vo = new SampleVO();
		vo.setVal1("lombok setter");

		System.out.println(vo.getVal1());
		return "index";
	}
}
```

localhost:8080 접속 후 콘솔창에 lombok setter 메세지가 출력될 것이다.

lombok에서 Getter, Settter, equals, hashCode, toString, 생성자를 자동으로 출력해 주려면 @Data 어노테이션을 적용하면 된다.

toString에서 특정 파라미터를 제외하려고 한다면 @ToString(exclude={"val3"}) 등의 속성을 추가할 수 있다.

하지만 @Data 어노테이션의 경우 잘못하면 무한 루프가 발생할 수 있음.. ORM에서 객체가 서로의 객체를 파라미터로 가지는 경우 서로 상대방의 toString을 호출하면서 무한 반복 호출이 생기는 경우가 있으므로 주의
