---
title: Servlet과 ServletFilter
date: 2017-10-25 22:20:03
tags: 
- Servlet
- Tomcat
categories:
- Java
- Servlet
---

## Servlet

관련 헷갈리던 내용들 정리

- Servlet Container와 Servlet의 동작 방식

제일 처음 톰캣이 구동됨과 동시에 프로젝트 내에 Servlet들이 생성되고, 이후에 해당 Servlet 요청이 들어오는 경우 스레드 풀에서 스레드를 가져와 해당 스레드에서 그 Servlet이 구동되게 된다. 이때 req, resp를 파라미터로 넘겨준다.

- Servlet에서 session 관련

HttpSession session = req.getHttpSession()으로 세션을 호출하면 Http Request를 읽어 현재 브라우저가 가진 쿠키의 SessionId값을 통해 그 SessionId에 해당하는 Map을 반환합니다. 만약 없다면 빈 Map을 생성해서 반환합니다.

이제 그 Map에서 set으로 객체들을 키/밸류 형태로 저장하거나, get을 통해 데이터를 가져올 수 있습니다.

- HttpSession과 관련된 테스트를 하려면?

```java
MockHttpSession session = new MockHttpSession();
session.setAttribute("user", "user");

```

위처럼 MockHttpSession 클래스를 이용해 임의의 값을 세팅하고 테스트를 할 수 있습니다.

- - -



## ServletFilter

#### 왜 필요할까?

- 모든 Servlet 요청과 응답에 대한 실행 시간을 구하고 싶다. 어떻게 구현할 것인가?
- 특정 Servlet 요청으로 전달되는 데이터를 UTF-8 인코딩으로 변경하고 싶다. HTTP의 기본 인코딩은 ISO 8859-1이라 한글을 지원하지 않는다. 어떻게 구현할 것인가?



#### Servlet에서 발생하는 중복 코드 어떻게 해결할 것인가?

- 위와 같은 요구사항을 해결하려면 Servlet에 상당히 많은 중복 코드가 발생한다. 이 중복을 어떻게 해결할 것인가?
- 이에 대한 해결책으로 등장한 녀석이 ServletFilter이다.

ServletFilter를 활용하는 대표적인 예가 Spring Security이다. 로그인 유무나 세션 관리를 위해 Spring Security에서 사용하고 있다.

이외에도 아래와 같이 Spring Framework에서 활용하는 아래와 같은 예가 있다.

- [HiddenHttpMethodFilter](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/filter/HiddenHttpMethodFilter.html): GET/POST 이외에 PUT, DELETE http method를 지원하기 위한 꼼수를 처리하기 위한 ServletFilter.
- [CharacterEncodingFilter](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/filter/CharacterEncodingFilter.html): HTTP를 통해 전달되는 데이터를 특정 encoding으로 변환하는 ServletFilter. 한글이 깨지지 않고 정상적으로 동작하도록 하려면 설정해야 함.
- [CorsFilter](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/filter/CorsFilter.html): Cross-Origin Resource Sharing (CORS) 설정을 위한 ServletFilter. 웹은 기본으로 Cross Domain을 지원하지 않는다. Cross Domain이 가능하도록 설정이 필요한데 CorsFilter를 활용해 해결하거나 CorsFilter와 같은 ServletFilter를 추가해 해결할 수 있음.



### ServletFilter의 구현

- ServletFilter는 Servlet과 완전히 독립적으로 구현
- ServletFilter를 적용할 Servlet을 url pattern을 설정함으로써 결정함.
- ServletFilter는 중복 코드를 제거하는 새로운 패턴

#### ServletFilter 구현

- javax.servlet.Filter 인터페이스를 구현



## 실습해보기

모든 Servlet 요청에 대해 각각 수행시간을 구하고 싶다면 어떻게 해야 할까?

만약 Servlet 하나하나에 모든 코드를 구현한다면 코드의 중복이 엄청나게 발생할 것이다. 이를 해결하기 위해 filter로 모든 경로에 대해 매핑하고, 그 Servlet 요청에 대해서 실행 시간을 구할 수 있다.



기존에 구현되어 있던 CharacterEncodingFilter.java에 실행시간을 구하는 로직을 추가해보자.

```java
@WebFilter("/*")
public class CharacterEncodingFilter implements Filter {
    private static final String DEFAULT_ENCODING = "UTF-8";
    private static final Logger logger = LoggerFactory.getLogger(CharacterEncodingFilter.class);

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    		logger.debug("init 시작");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
    		long start = System.currentTimeMillis();
        request.setCharacterEncoding(DEFAULT_ENCODING);
        response.setCharacterEncoding(DEFAULT_ENCODING);
        chain.doFilter(request, response);
        long end = System.currentTimeMillis();
        logger.debug("실행 시간 : " + ( end - start )/1000.0);
    }

    @Override
    public void destroy() {
    }
}
```

init은 처음 Tomcat이 구동되면서 Servlet들을 init할때 실행되고, destroy는 Tomcat이 종료되는 시점에 호출된다.

doFilter안에서 chain.doFilter(req,res) 부분이 servlet을 실행하는 부분이므로 이 전에 시작될 때 start time을 구하고 종료된 뒤 end time을 구해서 실행시간을 구할 수 있다.



- 어노테이션 기반의 Servlet과 ServletFilter 연결 방법

```java
@WebFilter("/*")
public class MyServletFilter implements Filter {
    // implements Filter's methods here...
}
```

- xml 기반의 Servlet과 ServletFilter 연결 방법 - web.xml

```xml
<filter>
  <filter-name>myServlet</filter-name>
  <filter-class>com.xxxx.core.filter.MyServletFilter</filter-class>
</filter>

<filter-mapping>
  <filter-name>myServlet</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```



## ServletFilter의 구조

ServletFilter는 아래와 같은 구조를 가지고 있다.

![servlet-filter](/images/java/servlet-filter.JPG)

요청을 DispatcherServlet에서 가져가기 전에 Filter를 거치게 됩니다. 이 내용은 아직 애매한 부분이 있어 추후 정리