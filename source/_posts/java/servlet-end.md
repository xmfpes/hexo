---
title: Servlet
date: 2017-10-27 22:20:03
tags: 
- Servlet
- Tomcat
categories:
- Java
- Servlet
---

![java](/images/java/java.png)

## Servlet과 동시성

- ### **Servlet Container는 multi thread로 동작한다. multi thread 환경에서 하나의 Servlet 인스턴스가 재사용될 때 고려해야할 점은 무엇인가?**


우선 Servlet은 서버가 실행될 때 각각 Servlet별로 하나의 인스턴스만 init되는데 하나의 인스턴스라고 해서 한번에 하나의 유저만 해당 Servlet에 접근해서 사용하는 것이 아니다.

여러 요청이 들어올 때 Servlet Container는 Thread Pool에 있는 Thread를 꺼내, 각각 유저별 Thread를 할당하고 그 각각에 해당 Servlet 받아와서 실행하게 된다.

#### **이때 생기는 문제점들**

아래와 같은 Servlet 코드가 있다고 가정해보자.

```java
@WebServlet("/user/logout")
public class LogoutSessionServlet extends HttpServlet{
	private static final long serialVersionUID = 1L;
	private User currentUser;
	private List<User> list = new ArrayList<User>();
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		HttpSession session = req.getSession();
		session.removeAttribute("user");
		resp.sendRedirect("/index.jsp");
	}
}
```

각각 메소드 내의 local 변수들의 경우에는 스택 영역에 저장되어 따로 관리되게 되지만, 인스턴스를 가지게 된다면 Heap 영역에 저장되어 해당 Servlet을 사용하는 모든 스레드에서 인스턴스들을 공유하게 된다.

여기서 A와 B 유저가 들어오는 경우가 있다고 가정해자.

A 유저가 list.get(0)로 currentUser에 0번째 유저를 불러 왔고, B 유저가 list.get(1)로 currentUser에 1번째 유저를 불러온 경우, 먼저 Sevlet을 실행한 A유저가 가진 currentUser값이 1번째 유저 값으로 바뀌는 문제점이 생기게 된다.

이 상황 이외에도 A유저가 list.get(1)번째 유저를 가져오려고 하는 상황에서 B 유저가 list.remove(1)을 해버린다면? 이렇게 Servlet 내부에서 메소드 내에 로컬 변수들이 아닌 인스턴스를 생성해 관리하게 되는 경우 여러 문제가 생기게 된다.

최대한 이런 상황을 피하도록 내부에 인스턴스를 가지지 않게 하고, 피치 못하게 사용해야 하는 경우는 **Collections.synchronizedList**같은 함수를 사용하거나 해서 동기화 처리를 해주어야 함.



- ### Servlet Container란?

Servlet들의 라이프사이클을 관리하는 것, Spring으로 예를 들면 Bean Container가 된다(Bean의 라이프사이클을 관리).



- ### ServletFilter 동작 방법

아래와 같은 ServletFilter 코드가 있다고 가정해 보자.

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
    		logger.debug("Tomcat 종료");
    }
}
```

위의 하나의 필터 뿐만 아니라, 프로젝트 내에 여러 필터가 존재하는 경우 ```chain.doFilter```에서 Filter가 모두 종료되는 것이 아니라, 다음에 적용될 Filter로 넘어가게 된다. 만약 모든 Response에 대해서 Filter에서 처리해주려면, ```chain.doFilter()```가 종료된 다음에 response에 처리를 해 주면 해당 처리가 완료 된 뒤에 브라우저로 Response를 보내게 된다.



- ### Servlet에서 IoC(Inversion of Control)

위의 예들을 보면, Servlet의 생명 주기를 프로그래머가 관리하지 않고 Servlet Container가 관리하게 된다.

이를 제어의 역전이라고 한다고 한다.



- ### Singleton Pattern과 DI(Dependency Injection)

Singleton 패턴으로 Instance를 하나만 생성하는 것은 테스트에 어려움이 있다. 그래서 Spring에서 DI의 개념이 등장하게 됨. 클래스 연결을 외부에서 하기 때문에 기능을 바꿔치기 해서 테스트 할 수 있다.