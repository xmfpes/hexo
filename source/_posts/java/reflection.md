---
title: Java Reflection
date: 2017-10-18 18:06:08
tags: 
- Reflection
categories:
- Java
- Reflection
---



Java의 Reflection은 컴파일한 클래스 정보를 활용해 동적으로 프로그래밍이 가능하도록 지원하는 API이다. 예를 들면, 아래와 같은 상황에서 유용하게 활용 할 수 있다.



- Junit4에서 @Test 애노테이션이 설정되어 있는 메소드를 단위 테스트로 실행하고 싶다.
- 현재 실행하는 클래스의 클래스, 필드, 메소드 정보를 알고 싶어요.
- 인자로 전달하는 클래스의 인스턴스를 생성한 후 메소드를 실행하고 싶어요.
- Eclipse/Intellij처럼 동적으로 setter와 getter 메소드를 만들고 싶을때
- 데이터베이스에서 조회한 데이터의 칼럼 이름과 자바 클래스의 필드 이름이 같은 경우 자동으로 매핑하고 싶다.





### 클래스의 모든 필드, 생성자, 메소드에 대한 정보를 출력하는 예제

```java
Class<Question> clazz = Question.class;
		
Field[] fields = clazz.getDeclaredFields();
Constructor<?>[] constructors = clazz.getConstructors();
Method[] methods = clazz.getMethods();

Arrays.stream(fields).forEach(f -> logger.debug("fields :" + f));
Arrays.stream(constructors).forEach(s -> logger.debug("constructor : " + s));
Arrays.stream(methods).forEach(m -> logger.debug("method : " + m));
```

Class<?> 형태로 class를 받아서 getDeclaredFields, getConstructors 등 메소드를 이용해 필드와 생성자 정보를 받아올 수 있다.



### test로 시작하는 메소드만 실행시키는 예제

```java
Class<Junit3Test> clazz = Junit3Test.class;
Method[] methods = clazz.getDeclaredMethods();
Arrays.stream(methods)
  .filter(m -> m.getName().startsWith("test"))
  .forEach(m -> {
    try {
      m.invoke(clazz.newInstance());
    } catch (Exception e) {
      e.printStackTrace();
    }
  });
```

Method들을 받아와서 getName을 통해 메소드 이름을 받고, filter로 메소드 이름이 test로 시작하는 메소드를 분류한 다음에 invoke를 통해 실행시킨다.

invoke의 첫번째 파라미터는 메소드를 실행할 인스턴스가 되고, 두번째 인자는 가변 인자인 파라미터가 된다.



### Test 어노테이션이 설정된 메소드만 출력하는 예제

```java
Class<Junit4Test> clazz = Junit4Test.class;
Method[] methods = clazz.getMethods();
Arrays.stream(methods)
  .filter(m -> m.isAnnotationPresent(MyTest.class))
  .forEach(m -> {
    try {
      m.invoke(clazz.newInstance());
    } catch (Exception e) {
      e.printStackTrace();
    }
  });
```

위의 메소드 이름을 체크하는것과 비슷하게, 이번에는 isAnnotationPresent를 이용해 파라미터로 받은 어노테이션 정보를 가지는지 체크하고 메소드를 실행한다.

### Private 필드를 가진 객체에 값 할당하기

private 필드에 set 메소드가 없어도, 리플렉션을 이용해 값을 할당할 수 있다.

```java
Class<Student> clazz = Student.class;
Field[] fields = clazz.getDeclaredFields();
Student st = new Student();
fields[0].setAccessible(true);
fields[1].setAccessible(true);
try {
  fields[0].set(st, "재성");
  fields[1].set(st, 30);
} catch (Exception e) {
  e.printStackTrace();
}

System.out.println(st.getAge());
System.out.println(st.getName());
```

field 정보를 모두 받은 다음에, 해당 필드에 접근 가능하도록 setAccessible 값을 true로 설정하고, set 메소드를 이용해 첫번째 파라미터로 받은 객체에 두번째 파라미터의 값을 설정해 줄 수 있다.



###  인자를 가지는 생성자를 가진 클래스의 인스턴스를 생성하기

기본 생성자가 없이, 파라미터를 받는 생성자만을 가진 클래스의 경우 clazz.newInstance()를 통해 생성해 줄 수 없다.

이럴 경우 Constructor를 받아서 newInstance에 파라미터를 넘겨줘서 인스턴스를 생성할 수 있다.

```java
Constructor<?>[] constructors = clazz.getDeclaredConstructors();
Arrays.stream(constructors).forEach(c -> {
  try {
    User user = (User) c.newInstance("kyunam", "hello", "hi", "haha");
    System.out.println(user);
  } catch (Exception e) {
    e.printStackTrace();
  }
});
```

Constructor에서 newIntsance로 파라미터를 넘겨줘서 user 인스턴스를 생성할 수 있다.



### 패키지 내 특정 어노테이션을 가진 메소드를 맵에 저장해 활용하기

메소드를 저장해서 사용하려면, 그 클래스의 인스턴스와 그 메소드의 값을 가져야 한다. 어떤 커스텀 클래스를 생성하고, 변수로 Class<?> 타입의 클래스, Method 타입의 method를 저장하도록 생성한다.

그리고 그 메소드를 실행해서 반환값을 리턴해 줄 메소드를 생성한다.

일단 그 이전에, @Controller라는 어노테이션을 가진 클래스들을 가져온다.

```java
Object[] obj;
Reflections reflections = new Reflections(obj);
  
```

위의 메소드의 반환 타입은 ```Set<Class<?>>``` 타입이다 이 Set을 받아서, Class<?> 타입을 키로, Class<?>의 newInstance를 통해 얻어온 객체를 값으로 맵에 저장해야 한다.

```java
annotated.stream().collect(Collectors.toMap(a -> a, a -> {
  try { return a.newInstance(); } catch (Exception e) { throw new ControllerInstantiationException(); }
}))
```

Class<?>인 a를 키, Class<?>의 인스턴스인 a.newInstance()를 밸류로 하는 맵을 생성한다.

이제 @Controller를 가지는 클래스들을 모두 저장해서 가지고 있고, 여기서 메소드 정보를 받아와서 분류해서 또 다른 Map에 넣어주어야 한다.

```java
controllerScanner.getControllerKeySet().stream()
  .forEach(clazz -> {
    ReflectionUtils.getAllMethods(clazz, ReflectionUtils.withAnnotation(RequestMapping.class))
      .forEach(classMethod -> {
        RequestMapping rm = classMethod.getAnnotation(RequestMapping.class);
        handlerExecutions.put(new HandlerKey(rm.value(), rm.method()), new HandlerExecution(clazz, classMethod));
      });
  });
```

기존에 저장한 Controller 어노테이션이 지정된 클래스의 맵의 KeySet을 받아서 RequestMapping 어노테이션이 적용된 클래스들을 모두 받아오고, 그 클래스의 모든 메소드들을 받아온 후 그 메소드의 어노테이션을 classMethod.getAnnotation(RequestMapping.class) 를 통해 받아오고 그 어노테이션인 RequestMapping이 가진 url 정보, http method 정보를 받아온 뒤 HandlerKey를 생성해 이를 키 값으로, 밸류로 HandlerExecution을 Class<?> 형태의 클래스, Method 형태의 메소드를 인자로 받는 생성자로 생성해 넣어준다.

그러면 보유한 맵에 HandlerKey를 키로, HandlerExecution을 밸류로 가지는 원소를 삽입하고, HandlerKey에 Hashcode와 equals를 구현해준 뒤 나중에 맵에서 Url, Method를 가지는 HandlerKey를 이용해 메소드를 찾을 수 있다.

```java
private Class<?> handleClass;
	private Method handleMethod;
	public HandlerExecution(Class<?> handleClass, Method handleMethod) {
		this.handleClass = handleClass;
		this.handleMethod = handleMethod;
	}
	public ModelAndView handle(HttpServletRequest req, HttpServletResponse resp) throws Exception {
		Object[] parameterArrays = {req, resp};
		return (ModelAndView)handleMethod.invoke(handleClass.newInstance(), parameterArrays);
	}

```

위는 invoke를 통해 req, resp를 파라미터로 넣어주고 ModelAndView 타입을 반환하는 메소드를 호출해 값을 반환하는 예시이다.





