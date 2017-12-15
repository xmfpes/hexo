---
title: TIL-19
date: 2017-11-6 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일

- ### JSON API 및 Ajax 실습

미루고, 미뤄왔던 JSON Api와 ajax를 끝냈다. 이번 실습에서는 QuestionDao나 AnswerDao, Answer, Question 등 모델이 정의되지 않아서 그냥 건너뛰고 진행하려고 했는데.. 이런걸 그냥 지나치지 못하는 성격이라 Test코드까지 대강 작성해서 실습을 진행하고, 댓글 갯수 카운트 넘기는 것 등등을 간단하게 구현했다. 구현 과정에서 의문점은 여러 개 있었지만 너무 시간을 지체한 것 같아서 그냥 넘어갔다.

- sql 쿼리에서 발생한 codacy 에러
- Js 파일에서 발생한 coday 에러
- JSON 처리 부분에서 null이 되는 부분과 Response 처리 중 중복되는 코드 제거



- ### MVC 2단계 실습 진행(ModelAndView 구조 적용)

JSON API 실습을 마치고, 2단계 실습으로 넘어갔다. 앞에서 이해를 제대로 다 하고, 이래저래 시간을 끌다가 진행해서 그런지 이해하기 쉬웠고 금방 진도를 나갈 수 있었다. 



- JsonView Response

```java
@Override
    public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response)
            throws Exception {
    		ObjectMapper mapper = new ObjectMapper();
	    	try {
	        response.setContentType("application/json;charset=UTF-8");
	        PrintWriter out = response.getWriter();
	        out.print(mapper.writeValueAsString(model));
            out.print(mapper.writeValueAsString(Result.ok()));
	        
	    	} catch(Exception e) {
	    		PrintWriter out = response.getWriter();
	    		out.print(mapper.writeValueAsString(Result.fail(e.getMessage())));
	    	}
    }
```

위와 같은 코드뭉치에서, out.print를 통해 Result.ok()와 insert 한 후 그 결과값을 클라이언트 쪽에 응답을 보내주는데.

여러개를 print해서 보내는 경우 json 형태가 바뀌어서 그런지 그런지 제대로 받아지지가 않는다. 이를 어떻게 해결해야 할까? 여러개를 보낼 방법이 없을까?

-> ModelAndView 형태로 전환하면서 각각 컨트롤러에서 object를 생성해서 해결해주었다.

하지만 위와같이 여러개 데이터를 보내야 하는 상황에서 어떻게 해야 하는걸까? 스프링에서는 Map을 키, 밸류 형태로 내보내 주거나 ResponseEntity를 만들어 넣어줬던거 같은데. 요즘 하도 스프링을 하지 않아 잘 기억이 나지 않는다.



이번에 꽤 많은 기능을 집어넣으면서 리팩토링이 제대로 되지 않은것 같아 마음 한켠에 계속 찜찜한 마음이 남아있지만, 진도가 너무 늦어지는것 같아서 일단 완성을 했다. 내일 오전에 피드백을 받고 최대한 고치고 PR을 다시 보내야겠다.



### Optional 관련 문서 읽기

예전에 스트림을 공부하면서 봤었고, 깊게 보지 않고 예외처리를 위해 자바 8에서 새로 나온거구나, 하고 넘어갔던 Optional이 이번 예외처리 관련 문서에서 다시 등장했다. 2단계 구현에 정신이 팔려서 제대로 읽진 못했지만, 관련 문서를 조금 읽었다.

내일 시간이 날때 오류 처리 탭의 강의 내용을 모두 진행하자



## 내일 할 일

- 포비와 면담
- 오류 처리 관련 내용 실습 진행하기
- MVC 2단계 리팩토링 하기



------



내가 움직이는게 아니라 기차가 움직이는거지만, 무거운 짐들을 들고 서울과 부산을 여러번 왔다갔다하는건 역시 힘들다. 그리고 역시 집이 좋다.