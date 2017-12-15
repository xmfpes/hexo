---
title: TIL-23
date: 2017-11-12 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 주말간 한일

- ### MVC 3단계 구현

금요일에 거의 완료한 상태에서, HandlerAdapter 기능을 토요일에 추가했다. 기존 LegacyController, Servlet, HandlerExecution들의 구현이 다르기 때문에 이를 모두 실행할 수 있는 어댑터를 추가해주는 것 같은 느낌인데..내가 이해한게 맞겠지? 일단 내가 생각한 대로의 MVC 3단계 구현은 완료했다. 

근데, 진행을 하면서 위화감을 떨칠수가 없었는데 포비가 워낙 가이드라인을 잘 잡아줘서, 이끌어주는대로 그냥 진행한 느낌이다. 물론 어느정도 이해를 하고 구현하긴 했지만 아무런 가이드라인 없이 한다고 해도 이런 설계를 할 수 있을까? Servlet이나 LegacyController 모두를 통합할 수 있는 Adapter의 개념이나 그를 이용한 방식 등 아직 갈길이 멀은 것 같다.

구현 후에 뭔가 그냥 넘어가도 될것같은 부분에 오래 시간을 끌어서 주말간 DI는 진행하지 못했다. 문서만 읽어보고 merge된 다음에 진행할 생각이다.



그때 피드백을 받으면서 생각했던 개념을 적용하기 위해서 Optional을 이용해봤는데, Handler를 찾을때 요청이 온 Url, Method에 해당하는 LegacyController를 찾고, 없으면 AnnotationHandler에서 찾고, 그래도 없다면 PageNotFound를 표시해주는 LegacyController를 리턴하도록 설계했다. 

원래 엄청 코드가 길었던거 같은데 그래도 많이 줄일 수 있었다. 예외처리도 아마도 잘 될거 같다. 없는 페이지를 요청할 경우 예외처리도 아마 확실히 될 것 같고..Null에 대한 부분도 아마 Optional을 이용해서 잘 제거되지 않았을까?

```java
public Object getHandler(HttpServletRequest req) throws Exception {
  return lhm.getHandler(req).orElse(ahm.getHandler(req)
            .orElseGet(PageNotFoundHandlingController::new));
}
```



고치고 싶은 점이야 한두개가 아니지만, DispatcherServlet에 예외처리를 RuntimeException으로 처리하는 부분에서 문제가 있었다.

```java
@Override
protected void service(HttpServletRequest req, HttpServletResponse resp){
  try {
    getModelAndViewInHandlerAdapter(req, resp).ifPresent(mav -> {
      mav.render(req, resp);
    });
  } catch (Exception e) {
    throw new DispatcherServletException();
  }
}



Optional<ModelAndView> mav = getModelAndViewInHandlerAdapter(req, resp);
if(mav.isPresent()) 
  mav.get().render(req, resp);
```

위와같이 RuntimeException을 생성해서 Catch에서 throw를 하는데, 아랫부분에 작성한 코드처럼 처리하면 문제가 없다. 하지만 위의 코드로 구현을 하면 RuntimeException으로 throw 하는데도, ifPresent의 내부에서 또 중복으로 try, catch 처리를 해 주어야 하는 문제점이 발생했다.

스트림이나 Optional에서 내부적으로 도는 코드 내에서 발생하는 예외의 경우에는 위처럼 처리가 안되는것 같은데, 어떻게 방법이 없을까??



- ### 결혼식 참여

일요일은 동기였던 형이 결혼을 하게돼서 인천을 갔고, 오랜만에 부산 친구들을 만나서 반가웠다. 주변 사람이 결혼을 하니까 뭔가 싱숭생숭하다. 거기다 오랜만에 친구들을 보니 더 그런듯 하다.



------

## 내일 할 일
