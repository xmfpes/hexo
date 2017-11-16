---
title: TIL(11/13)
date: 2017-11-13 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일



- ### Spring MVC 구현 실습

다 해본 내용이라 그냥 넘어갈 까..라고 생각하던 차에 뒷 내용을 봤는데 @LoginUser 어노테이션을 이용한 세션 처리 중복 제거와 @ControllerAdvise 예제가 있어서 오늘 오후에 구현을 시작했다.

User기반 컨트롤러를 하나에 통합시키고, @LoginUser 어노테이션을 구현해줬는데. 맞게 한 건지 잘 모르겠다.

```java
@Bean
public LoginUserHandlerMethodArgumentResolver getMethodArgumentResolver() {
  LoginUserHandlerMethodArgumentResolver bean = new LoginUserHandlerMethodArgumentResolver();
  return bean;
}

@Override
public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
  argumentResolvers.add(getMethodArgumentResolver());
  super.addArgumentResolvers(argumentResolvers);
}
```

우선 WebMvcConfig에 빈을 등록하고 했는데, 저렇게 리턴하는 메소드에 Bean 어노테이션을 붙여서 선언하는 방식으로 빈을 등록하는지도 모르겠고, addArgumentResolvers를 오버라이드 해서 그 리스트에 직접 add해주는 방식이 맞는지도 모르겠다.



스프링을 깊게 해 보지 않다보니 이런 기능들에 대해 처음 알게 됐다.

HandlerMethodArgumentResolver 인터페이스의 경우도 처음 알았는데, 이 인터페이스를 구현해서 사용자 요청이 Controller에 도달하기 전에 그 파라미터를 수정하거나, 공통적으로 추가해줄 수 있는 것 같다. 유용하게 사용할 수 있을듯. 이전에 HttpSession의 중복처리를 못한 부분이 걸렸는데, 이를 이용해 해결할 수 있을듯 하다.

[HandlerMethodArgumentResolver 정리](https://xmfpes.github.io/java/spring-resolver/)



내일은 ControllerAdvise에 대해 정리할 생각이다. 요구 사항에 대해서는 모두 구현했지만, ControllerAdvise에 대해 명확히 개념이 잡히지 않는다. 내일 오전중에 질문을 하고, 명확히 정리해보자.





- ### Stream 내부에서의 checked Exception 처리

이번에 함수형 프로그래밍의 숙달을 위해서 모든 내용을 스트림과 Optional 등을 이용해 구현했는데, 생각보다 많은 도움이 됐던거 같다. Optional을 이용해서 처리해주면서 NullPointerException에 대해선 조금 걱정을 덜었다. 물론 Optional의 ofNullable이 아닌 of를 써서 NullpointerException이 뜨기도 했지만.. 

오늘 포비와 오전에 고민을 했었고, 해결하지 못한 파이프라인 안에서의 예외 처리에 대해 이야기 했는데. 관련 내용을 포비가 코드리뷰로 올려서 한번 훑어봤다. 꽤 내용이 많아서 살펴보려면 시간이 조금 걸릴 것 같다. 공부는 끝이 없는듯



## 내일 할 일

- ### ControllerAdvise 정리하고, 적용해보기

이전에도 이를 적용하려다 안한 적이 있는데 이번 기회에 정리를 해 볼 생각이다. 오전중에 해야지.

- ### DI 내용 진행해보기

DI 내용을 한번 읽어봤는데, 내용이 그리 길지 않아서..이해만 잘 하면 금방 할 수 있지 않을까?



------



지금 레벨 4를 진행하는 인원 모두 실력이 굉장히 많이 늘고 있는것 같다. 물론 나도 그렇고 본인들은 그렇게 생각 안하겠지만.. 일단 나는 한 세달, 네달 전 아무것도 모를때에 비하면 굉장히 빠른 속도로 성장하는 것 같은 느낌이다. 지금도 솔직히 허접하기 그지없지만..

시간이 되면 레벨 4 처음 내용부터 다시 차근차근 해보고 싶다. 다시 처음부터 하면 좀 더 완성도 있는 프레임워크를 구현할 수도 있을것 같은데.. 시간이 된다면 트렐로에 OAuth.. 보안이나 로깅, 로드밸런싱 등의 기능을 넣어 API를 구성하고나서 프레임워크를 다시 구현해 볼 생각이다.



그리고 면접에 대비해서, 어떤 질문에 대해서 한 마디로 명확히 설명할 수 있도록 개념들을 조금 정리할 필요가 있는 것 같다.

나는 생각보다 많은 개념에 대해서 얕게든 ,어떻게든 알고 있긴 하지만 그를 명확히 설명하라고 하면... 잘 말을 못 하는 느낌이다.