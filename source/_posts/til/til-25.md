---
title: TIL-25
date: 2017-11-14 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일

### DI Framework 구현

BeanFactory를 생성하고 그 기능을 구현했다. 구현하면서 잘 모르겠다 싶은 부분이 있으면 포비의 힌트가 있어서 Bean을 생성하고 가져오는 부분 까지는 쉽게 구현했는데, 오히려 리팩토링을 하고 적용하는 부분에서 조금 헤맸다.

우선 지금까지 구현한 내용들 중에서 조금 신경 쓰인 내용들은 아래의 내용들이다.



- ### 사용되지 않고 있었던 Map의 제거

우선 ControllerScaner -> BeanScanner로 변경하는 과정에서 갑자기 맵을 왜 쓰는지에 대해 의아함이 생겼다.

 ```java
private Map<Class<?>, Object> annotationHandleMap = new HashMap<Class<?>, Object>();
 ```

맵을 이용해 구현하고 맵을 사용을 하질 않았었다. 몰랐는데 BeanScaner(구 ControllerScaner)에서 아래의 메소드는 지워도 호출되는 곳이 없어서 에러가 나지 않았다.

```java
public Object getControllerInstance(Class<?> clazz) {
 	return annotationHandleMap.get(clazz);
 }
```

Map의 형태를 가지지만 Map에 KeySet 메소드를 이용해 Key값으로 쓰는 Class<?>를 Set을 이용해 받아올 뿐 Map의 value를 사용하는 코드는 찾을 수 없었다.

```java
public Set<Class<?>> getAnnotationHandleKeySets(){
  return annotationHandleSet;	
}
```

그래서 Map을 제거하고 Set으로 변경해주고 BeanScanner형태로 리팩토링 해서 Controller.class, Service.class, Repository.class 세개의 어노테이션을 가지는 클래스들을 Set에 저장하도록 구현했다.



- ### BeanFactory에서 반환된 Controller 빈들의 처리

일단 구현해보자는 생각으로 다 구현하고 PR을 보냈는데, AnnotationHandlerMapping의 처리가 이상한 것 같다. 빈 팩토리에서 빈을 관리하도록 실컷 해 두고는 Controller에 RequestMapping이 설정된 메소드를 실행할 때 매번 새 인스턴스를 newInstance()로 생성해주는게 아닌가? 이러면 뭔가 이상한거 같다. 

요구사항에서 beanScanner와 beanFatory를 사용하래서 하긴 했는데 결국 제대로 쓰지 않은 셈이 됐다.

```java
@SuppressWarnings("unchecked")
public void initialize() {
  BeanScanner beanScanner = new BeanScanner(basePackage);
  BeanFactory beanFactory = new BeanFactory(beanScanner.getAnnotationHandleKeySets());
  beanFactory.getControllerInBeansFactory().keySet().stream()
    .forEach(clazz -> {
      ReflectionUtils.getAllMethods(clazz, ReflectionUtils.withAnnotation(RequestMapping.class))
        .forEach(classMethod -> {
          RequestMapping rm = classMethod.getAnnotation(RequestMapping.class);
          handlerExecutions.put(new HandlerKey(rm.value(), rm.method()), new HandlerExecution(clazz, classMethod));
        });
    });
}
```

위는 BeanFactory에 빈을 가진 맵 내부에 존재하는 Controller Bean들을 가져와서, AnnotationHandlerMapping에서 handlerExecutions 맵에 handling된 컨트롤러들을 각각 url, method에 따라 분류해 넣어주고 있는 부분이다.

여기서, HandlerExecutions에 값을 넣을 때, BeanFactory에서 가지는 키 값이 clazz이다. 여기서 보면 그 키 값인 Class<?> 를 넣고 있지, Bean 객체, map.get(key)의 반환값인 Object 타입의 Bean 인스턴스를 받고 있지 않다.

이러면 결국에 그 클래스를 받아 newInstance로 새 인스턴스를 만들 것이고, 그러면 결국 빈을 사용하는 것이 아니라 매번 새 인스턴스를 생성해 메소드를 실행하는 비 효율적인 구조를 가지게 되는거 아닌가?



그래서 AnnotationHandlerMapping의 메소드를 수정해서 HandlerExecution을 생성할 때 BeanFactory의 값을 받아서 넣어주도록 변경했다.

```java
@SuppressWarnings("unchecked")
public void initialize() {
  BeanScanner beanScanner = new BeanScanner(basePackage);
  BeanFactory beanFactory = new BeanFactory(beanScanner.getAnnotationHandleKeySets());
  Map<Class<?>, Object> controllerBeans = beanFactory.getControllerInBeansFactory();
  controllerBeans.keySet().stream()
    .forEach(clazz -> {
      ReflectionUtils.getAllMethods(clazz, ReflectionUtils.withAnnotation(RequestMapping.class))
        .forEach(classMethod -> {
          RequestMapping rm = classMethod.getAnnotation(RequestMapping.class);
          handlerExecutions.put(new HandlerKey(rm.value(), rm.method()), new HandlerExecution(controllerBeans.get(clazz), classMethod));
        });
    });
}
```

이제 Bean을 받아 저장해서 Object 형태로 가지고 있도록 한다.

```java
public class HandlerExecution {
	private Object handleClassBean;
	private Method handleMethod;
	public HandlerExecution(Object handleClassBean, Method handleMethod) {
		this.handleClassBean = handleClassBean;
		this.handleMethod = handleMethod;
	}
	
	public ModelAndView handle(HttpServletRequest req, HttpServletResponse resp) throws Exception {
		Object[] parameterArrays = {req, resp};
		return (ModelAndView)handleMethod.invoke(handleClassBean, parameterArrays);
	}
}
```

그래서 위처럼 HandlerExecution의 구조를 Class<?> 를 가지지 않고 Object로 해당 빈을 가지게 만들었다. 일단 동작은 하는데 빈을 사용하고 나면 반환해야 하는건 아닌가 싶기도 하고 잘 모르겠다.



나중에 보니까 bean이 다 null이었다. 테스트는 통과하는데 대체 왜? 라고 보니까 beanFactory에서 initialize를 안해줬네..



### 내일 할 일

- ### 공부한 내용들 조금 정리

앞에 내용을 휴가 하고있어서 조금씩 옆에서 보게 되는데 머리가 새하얗다. 정리가 필요할 듯 하다. 지금 진행하는 내용도 포비의 친절한 설명과 힌트 덕에 진행했는데 내가 스스로 구현한다면 조금 힘들 것 같다. 정리를 해보자

- ### 리액트 / HTML / CSS / JavaScript 중에 하나 공부하기

요즘 너무 자바만 한 느낌이다. DI를 빠르게 끝냈으니까 잠시 다른걸 하면서 머리를 식히자.

 