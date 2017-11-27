---
title: Spring Boot 기반 Spring Security 회원가입 / 로그인 구현하기
date: 2017-11-27 15:03:44
tags:
- Spring Boot
- Spring Security
categories:
- Java
- Spring
---



스프링 부트 환경에서 BCryptPasswordEncoder를 이용한 회원가입 암호화 / Security를 이용한 로그인 구현





## 프로젝트에 Spring Security 적용하기



Maven

```Xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Gradle

```
compile("org.springframework.boot:spring-boot-starter-security")
```



위와 같이 디펜던시를 추가한 다음에 적절한 패키지를 생성하고, SecurityConfig 클래스를 추가한다. 패키지는 스프링 프로젝트의 패키지 하부에 있어야 한다.

```Java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

}
```

위와같이 설정해주면, 이제 사이트 전체가 잠겨서, 비밀번호를 쳐야 접근할 수 있게 된다. 원활한 프로젝트 진행을 위해 일단 페이지의 인증을 해제하자.

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	
	@Override
	public void configure(WebSecurity web) throws Exception
	{
		web.ignoring().antMatchers("/css/**", "/script/**", "image/**", "/fonts/**", "lib/**");
	}
	@Override
	protected void configure(HttpSecurity http) throws Exception
	{
		http.authorizeRequests()
			.antMatchers("/admin/**").access("ROLE_ADMIN")
			.antMatchers("/**").permitAll();
	}
}
```

위처럼 자원에 대한 접근을 풀고, 밑에서는 세부적인 설정을 따로 넣어준다. 일단은 원활한 진행을 위해 모든 경로에 대해 permitAll을 줬다.



## 적용된 Security 기반 회원가입 로직 추가하기

회원가입 이전에, 먼저 User정보를 구성할 테이블을 생성해야하는데 Security의 내부적으로 사용하는 클래스의 이름이 User이다. 겹치기 때문에 Account나 Member라는 이름을 사용해서 구현하자.



