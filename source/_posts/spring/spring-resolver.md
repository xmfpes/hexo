---
title: Spring - HandlerMethodArgumentResolver 인터페이스
date: 2017-11-10 22:20:03
tags: 
- Spring
- HandlerMethodArgumentResolver
categories:
- Java
- Spring
---



![](/images/spring/spring-ecosystem.jpg)

HandlerMethodArgumentResolver는 Spring의 컨트롤러들을 다룰 때, 컨트롤러에 들어오는 파라미터를 수정하거나 공통적으로 추가해야 하는 경우에 사용한다.

예를 들어, 로그인 사용자의 아이디를 가져온다고 생각해보자. 매번 HttpSession을 파라미터로 받아서 처리해주어야 할 것이다. 그런 경우가 한 두번이라면 모를까, 매번 체크해주어야 하는 것이기 때문에 번거롭다. HandlerMethodArgumentResolver는 사용자 요청이 Controller에 도달하기 전에 그 요청의 파라미터들을 수정할 수 있게 해주는 역할을 한다.



### HttpSession의 유저 정보를 @LoginUser 어노테이션을 이용해 받아오기

@LoginUser라는 인터페이스를 구현해 현재 세션의 로그인 유저 정보를 받아오는 것을 작성한다고 생각해보자.

우선 LoginUser 어노테이션을 생성한다.

LoginUser annotation

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface LoginUser {
}
```



그 다음 HandlerMethodArgumentResolver 인터페이스를 구현하는데, 이때 두가지 메소드를 구현해주어야 한다.

```java
public class LoginUserHandlerMethodArgumentResolver implements HandlerMethodArgumentResolver {

	@Override
	public Object resolveArgument(MethodParameter arg0, ModelAndViewContainer arg1, NativeWebRequest webRequest,
			WebDataBinderFactory arg3) throws Exception {
		Object user = webRequest.getAttribute(UserSessionUtils.USER_SESSION_KEY, WebRequest.SCOPE_SESSION);
		return user;
	}

	@Override
	public boolean supportsParameter(MethodParameter parameter) {
		return parameter.hasParameterAnnotation(LoginUser.class);
	}

}
```

- supportsparameter method

Resolver가 적용 가능한지 검사하는 역할을 한다. 여기서는 해당 컨트롤러의 파라미터가 LoginUser 어노테이션을 포함하는지를 검사해서 boolean 값을 리턴한다.

만약 뭐 같은 타입인지를 체크하려면 `CommandMap.class.isAssignableFrom(parameter.getParameterType());` 이와 같이 처리할 수도 있겠다.

- resolveArgument method

메서드는 파라미터와 기타 정보를 받아서 실제 객체를 반환한다. 여기서는 webRequest에 담긴 세션에 담긴 User 값을 받아오는데 이부분은 정확히 이해가 가지 않는다.



### CustomResolver 등록하기

위에서 만든 CustomResolve는 만들기만 해서 작동하지 않는다. 따로 등록해주어야 하는데 WebMvcConfig에서 등록해 줄 것이다.

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = { "next.controller" })
public class WebMvcConfig extends WebMvcConfigurerAdapter {
    private static final int CACHE_PERIOD = 31556926; // one year
    
    @Bean
    public LoginUserHandlerMethodArgumentResolver loginUserMethodArgumentResolver() {
    		LoginUserHandlerMethodArgumentResolver bean = new LoginUserHandlerMethodArgumentResolver();
    		return bean;
    }
    
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
    		argumentResolvers.add(loginUserMethodArgumentResolver());
    		super.addArgumentResolvers(argumentResolvers);
    }
}
```



일단 위와같이 LoginUserHandlerMethodArgumentResolver를 Bean형태로 생성하고, argumentResovers List에 넣어주었다. 위에서 Bean으로 등록된 loginUserMethodArgumentResolver는 호출될 때 BeanFactory에서 먼저 Bean 객체가 있는지 없는지 판단하고, 있다면 BeanFactory에서 꺼내오고 없으면 Bean을 생성해 넣어주는 방식으로 구동된다고 한다.



어노테이션의 사용은 아래와 같이 할 수 있다.

```java
public ModelAndView update(String userId, User formUser, @LoginUser User loginUser) {
  User user = userDao.findByUserId(userId);

  if (!user.equals(loginUser)) {
    throw new UnAuthorizedException();
  }
  User updateUser = formUser;
  log.debug("Update User : {}", updateUser);
  user.update(updateUser);
  return new ModelAndView("redirect:/");
}
```

@LoginUser 어노테이션을 통해 바로 loginUser값을 꺼내와서 사용할 수 있다.