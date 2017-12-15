---
title: TIL-32
date: 2017-11-30 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한 일



- ### SpringSecurity successHandler 구현

시큐리티에서, 로그인 성공 이후에 수행할 작업을 위해서 successHandler를 구현했다. 예전부터 로그인 이후에 특정 페이지로 리다이렉트 하는 것 말고 이전에 보던 페이지로 이동시키고 싶었는데 옆의 브라이언이 구현했던 걸 참고하면서 Referer 요청 헤더에 대한 정보를 찾고, successHandler를 이용해 로그인에 성공할 시 Referer 헤더를 읽어 이전 페이지의 정보를 받아와 로그인 성공시 이전 페이지로 리다이렉트 하는 부분을 구현했다.

SecurityConfig에서 successHandler에 핸들러를 등록해주면 되는거였는데, 등록할 핸들러의 타입은 `AuthenticationSuccessHandler `타입의 인터페이스고 우리는 그 인터페이스를 구현하는 `SimpleUrlAuthenticationSuccessHandler`를 상속하는 `SavedRequestAwareAuthenticationSuccessHandler`를 상속받아서 기능을 구현하면 되는 것 같다.

```java
public class CustomLoginSuccessHandler extends SavedRequestAwareAuthenticationSuccessHandler {
	public CustomLoginSuccessHandler(String defaultTargetUrl) {
        setDefaultTargetUrl(defaultTargetUrl);
    }
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, 
    	Authentication authentication) throws ServletException, IOException {
        HttpSession session = request.getSession();
        if (session != null) {
            String redirectUrl = (String) session.getAttribute("prevPage");
            if (redirectUrl != null) {
                session.removeAttribute("prevPage");
                getRedirectStrategy().sendRedirect(request, response, redirectUrl);
            } else {
                super.onAuthenticationSuccess(request, response, authentication);
            }
        } else {
            super.onAuthenticationSuccess(request, response, authentication);
        }
    }
}
```

위처럼 구현한 클래스를 빈 형태로 만들어서

```java
http.successHandler(successHandler())
  
@Bean
public AuthenticationSuccessHandler successHandler() {
  return new CustomLoginSuccessHandler("/");//default로 이동할 url
}
```

핸들러를 등록해주면 되는거였다.

시큐리티 관련 내용은 자료가 좀 많이 없는 것 같은데, 이런 부분들을 어디서 어떻게 찾아야할지가 굉장히 어려운 것 같다.

정말 한글 문서로는 한계가 있는 것 같다. 영어 문서를 읽을 수 있는 영어 리딩 능력을 키워야겠다.



이번에 그냥 가볍게 구현을 하면서 다음에 쉽게 할 수 있도록 정리를 해 뒀다.

[스프링 시큐리티 UserRepository를 이용한 로그인 구현](https://xmfpes.github.io/spring/spring-security/)

[스프링 시큐리티 OAuth2를 이용한 소셜 로그인 구현](https://xmfpes.github.io/spring/spring-security-oauth2-social-login/)

대충 BCryptPasswordEncoder를 이용한 회원가입 암호화 / domain 객체의 CrudRepository를 이용해 로그인 하는 부분, 로그인 이후 처리 / OAuth2를 이용한 소셜 로그인 구현에 대해 정리했다.



정리하는 모든 내용들이 나만 알아볼 수 있게 정리된 느낌이긴 하다. 나중에 많은 사람들이 레벨 4의 내용을 듣게 될 텐데 시큐리티 적용에 어려움을 겪는 사람들이 참고할 수 있으면 좋겠다.



- ### 이력서 작성

구인 목록에 있는 회사들에 대해 지원을 해보려고 이력서를 쓰고 있는데, 이때까지 계속 쓰던거지만 잘 써지지 않는다.



## 내일 할 일



- ### Spring Security OAuth2 소셜 로그인 관련 기능 추가하기

소셜 로그인 이후 디비에 저장됐는지 여부에 따라, 새로 회원가입을 시키는 로직을 추가해야 한다. 위의 handler에는 소셜 로그인이 걸러지지 않아서 따로 어떻게 처리해줘야 할 것 같은데 ssoFilter 내부에서 처리하면 되려나? 잘 모르겠다. 찾아봐야 할 것 같다.



- ### 이력서 작성



잠을 잘못 잤는지 목이 아팠는데 방을 보러 1시간가량 돌아다니면서 목감기에 걸린 것 같다. 최근 컨디션이 영 안좋은데 1일부터 새로 시작하는 마음으로 건강히 새 마음 새뜻으로 시작해야겠다.

