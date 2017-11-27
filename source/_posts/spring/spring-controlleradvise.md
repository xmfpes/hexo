---
title: Spring - ControllerAdvice 어노테이션
date: 2017-11-10 22:20:03
tags: 
- Spring
- HandlerMethodArgumentResolver
categories:
- Java
- Spring
---



![](/images/spring/spring-ecosystem.jpg)

스프링에서 예외처리를 전역적으로 핸들링하기 위해 @ControllerAdvicde 사용할 수 있다.



```java
@ControllerAdvice
public class ControllerExceptionHandler {
	private static final Logger log = LoggerFactory.getLogger(ControllerExceptionHandler.class);
	
	@ResponseStatus(value = HttpStatus.UNAUTHORIZED)
	@ExceptionHandler(value = UnAuthorizedException.class)
	public ModelAndView handleUnAutorizedException(UnAuthorizedException e) {
		return new ModelAndView("UnAutorrizedPage.jsp");
	}
}
```

위와 같이 작성하면 UnAutorizedException 예외를 핸들링해서 처리하며, 응답 값으로 401 status code를 보내줄 수 있다.

@ControllerAdvice를 발생하는 exception 전체를 핸들링하게 하려면, ComponentScan을 통해 명시적으로 Scan을 해줘야 한다.



위는 일반적인 Controller들에 대한 exception handling이고, REST 요청에 대한 에러를 핸들링하려면 @RestControllerAdvice를 사용하면 된다.

```java
@RestControllerAdvice
public class RestExceptionHandler {
  @ResponseStatus(value = HttpStatus.UNAUTHORIZED)
  @ExceptionHandler(value = UnAuthorizedException.class)
  public Object handleUnAutorizedException(UnAuthorizedException e) {
    return new Object(e.getMessage());
  }
}
```

RestControllerAdvice는 리턴하면서 자동으로 JSON 형태로 바꾸어 준다고 한다. 안 써봐서 아직 잘 모르겠다.

