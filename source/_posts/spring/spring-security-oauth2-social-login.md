---
title: Spring Boot 기반 Spring Security OAuth2를 적용한 소셜 로그인 구현하기
date: 2017-11-27 15:03:54
tags:
- Spring Boot
- Spring Security
- OAuth2
categories:
- Java
- Spring
---

![](/images/spring/spring-security.jpg)

스프링 시큐리티 OAuth2를 이용한 소셜 로그인 적용 시큐리티와 OAuth 통합 구조 및 시큐리티 작성하기



------

### 프로젝트에 Spring Security OAuth2 종속성 추가하기

------



기존에 Spring Security가 적용된 상태에서 OAuth2와 관련된 디펜던시를 추가.

Maven

```Xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.security.oauth</groupId>
	<artifactId>spring-security-oauth2</artifactId>
</dependency>
```

Gradle

```
compile("org.springframework.boot:spring-boot-starter-security")
compile('org.springframework.security.oauth:spring-security-oauth2')
```



시큐리티는, 이미 있으니 하지 않아도 되고 아래의 security-oauth2 부분만 추가하면 된다.

------

### Facebook Api 앱 등록하기

------

- 앱 이름을 입력하고 앱 ID 만들기


- 왼쪽 상단 대시보드 클릭 -> 플랫폼 선택 -> 웹 사이트 선택
- 오른쪽 상단 Skip Quick Start 클릭
- 왼쪽 앱 검수 클릭 -> 앱 공개 설정 예로 변경 후 카테고리 선택
- 설정 -> 기본 설정 -> 앱 도메인 localhost 입력
- 설정 -> 기본 설정 -> 플랫폼 추가 -> 웹 사이트 추가 웹 사이트 URL <http://localhost:8080/> 입력

![](/images/spring/facebook-api-redirect.png)

위와 같이 설정하고 나서, 그냥 완료하면 리다이렉션 과정에서 에러가 난다. 아래와 같이 리다이렉션 URL을 추가해 주어야 한다.

![](/images/spring/facebook-api-setting.png)



------

### OAuth2 클라이언트 수동 설정하기

------

기존에는 SecurityConfig 파일에는 아래와 같이 어노테이션이 설정되어 있다.

```Java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
}
```



여기서 OAuth2와 관련된 어노테이션을 추가해야 한다.

`@EnableOAuth2Sso` 어노테이션은  OAuth2 클라이언트, 인증의 두가지 기능을 가지고 있다.

현재가 우리가 구현해야 할 기능은, OAuth2 클라이언트에서 `OAuth2ClientContext`를 주입하고, 우리의 시큐리티 설정에 추가할 인증 필터를 만들어야 한다.

그렇기 때문에 OAuth2의 클라이언트단 기능을 제작해야 하고, `@EnableOAuth2Sso` 가 아닌, 하위 레벨의 `EnableOAuth2Client` 어노테이션을 사용해야 한다.



일단 간단히, `@EnableOAuth2Sso` 는 자동으로 하나의 소셜 로그인 설정을 지원하지만, 여러개의 필터를 이용해 소셜 로그인을 붙일 경우 `EnableOAuth2Client`를 사용해서 진행하면 된다.



- #### EnableOAuth2Client 어노테이션 삽입

```Java
@EnableWebSecurity
@EnableOAuth2Client
public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
}
```

- #### Social 로그인에 사용할 필터 제작

아래의 작업들은 전부 SecurityConfig.class에서 진행되는 작업들이다.

Oauth2ClientContext 의존성 주입

```java
@Autowired
OAuth2ClientContext oauth2ClientContext;
```

페이스북 소셜 로그인에 사용할 ssoFilter 메소드 제작 Filter는 Servlet.Filter를 import하면 된다.

```java
private Filter ssoFilter() {
  OAuth2ClientAuthenticationProcessingFilter facebookFilter = new OAuth2ClientAuthenticationProcessingFilter("/login/facebook");
  OAuth2RestTemplate facebookTemplate = new OAuth2RestTemplate(facebook(), oauth2ClientContext);
  facebookFilter.setRestTemplate(facebookTemplate);
  UserInfoTokenServices tokenServices = new UserInfoTokenServices(facebookResource().getUserInfoUri(), facebook().getClientId());
  tokenServices.setRestTemplate(facebookTemplate);
  facebookFilter.setTokenServices(tokenServices);
  return facebookFilter;
}
```

위의 필터에서 사용할, application.yml 파일의 프로퍼티들을 빈 형태로 제작해야 한다.

```Java
@Bean
@ConfigurationProperties("facebook.client")
public AuthorizationCodeResourceDetails facebook() {
return new AuthorizationCodeResourceDetails();
}

@Bean
@ConfigurationProperties("facebook.resource")
public ResourceServerProperties facebookResource() {
return new ResourceServerProperties();
}
```

그리고 application.yml 파일에 아래와 같은 페이스북 앱 세팅을 해주어야 한다.

```yaml
facebook:
    client:
      clientId: Your app Id
      clientSecret: Your app secret key
      accessTokenUri: https://graph.facebook.com/oauth/access_token
      userAuthorizationUri: https://www.facebook.com/dialog/oauth
      tokenName: oauth_token
      authenticationScheme: query
      clientAuthenticationScheme: form
    resource:
      userInfoUri: https://graph.facebook.com/me
```

그리고 리다이렉션 처리를 위한 필터를 등록하자.

앱에서 Facebook으로의 리다이렉션을 명시적으로 지원해 주어야 한다.  Spring OAuth2에서 서블릿으로 처리되며 필터를 이용해 처리해야 하는데 이를 위해 필터를 연결해주어야 한다.

```java
@Bean
public FilterRegistrationBean oauth2ClientFilterRegistration(
    OAuth2ClientContextFilter filter) {
  FilterRegistrationBean registration = new FilterRegistrationBean();
  registration.setFilter(filter);
  registration.setOrder(-100);
  return registration;
}
```

위에서 스프링 부트 어플리케이션에서 올바른 순서로 호출할 수 있도록 필터를 연결해야하는데, OAuth2ClientContextFilter를 받아서 Spring Security보다 낮은 순서로 필터를 등록하고, 이를 이용해 인증 요청의 예외에 의해 리다이렉션을 처리하는데 사용할 수 있다.



- ### SecurityConfig.class 전체 코드

```java
package com.kyunam.trello.jwptrellobe.config;

import javax.servlet.Filter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.security.oauth2.resource.ResourceServerProperties;
import org.springframework.boot.autoconfigure.security.oauth2.resource.UserInfoTokenServices;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.oauth2.client.OAuth2ClientContext;
import org.springframework.security.oauth2.client.OAuth2RestTemplate;
import org.springframework.security.oauth2.client.filter.OAuth2ClientAuthenticationProcessingFilter;
import org.springframework.security.oauth2.client.filter.OAuth2ClientContextFilter;
import org.springframework.security.oauth2.client.token.grant.code.AuthorizationCodeResourceDetails;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableOAuth2Client;
import org.springframework.security.web.authentication.www.BasicAuthenticationFilter;

@EnableWebSecurity
@EnableOAuth2Client
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	OAuth2ClientContext oauth2ClientContext;

	@Override
	public void configure(WebSecurity web) throws Exception {
		web.ignoring().antMatchers("/css/**", "/script/**", "image/**", "/fonts/**", "lib/**");
	}

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http.authorizeRequests().antMatchers("/admin/**").access("ROLE_ADMIN").antMatchers("/**").permitAll().and()
				.addFilterBefore(ssoFilter(), BasicAuthenticationFilter.class);
	}

	private Filter ssoFilter() {
		OAuth2ClientAuthenticationProcessingFilter facebookFilter = new OAuth2ClientAuthenticationProcessingFilter(
				"/login/facebook");
		OAuth2RestTemplate facebookTemplate = new OAuth2RestTemplate(facebook(), oauth2ClientContext);
		facebookFilter.setRestTemplate(facebookTemplate);
		UserInfoTokenServices tokenServices = new UserInfoTokenServices(facebookResource().getUserInfoUri(),
				facebook().getClientId());
		tokenServices.setRestTemplate(facebookTemplate);
		facebookFilter.setTokenServices(tokenServices);
		return facebookFilter;
	}

	@Bean
	@ConfigurationProperties("facebook.client")
	public AuthorizationCodeResourceDetails facebook() {
		return new AuthorizationCodeResourceDetails();
	}

	@Bean
	@ConfigurationProperties("facebook.resource")
	public ResourceServerProperties facebookResource() {
		return new ResourceServerProperties();
	}
	
	@Bean
	public FilterRegistrationBean oauth2ClientFilterRegistration(
	    OAuth2ClientContextFilter filter) {
	  FilterRegistrationBean registration = new FilterRegistrationBean();
	  registration.setFilter(filter);
	  registration.setOrder(-100);
	  return registration;
	}
}
```



위와같이 설정하고, 아래와 같이 소셜 로그인에 연결될 링크를 설정하고, 클릭하면 정상적으로 작동할걸?

```html
<a class="facebook-login-text" href="/login/facebook">facebook으로 로그인</a>
```








