---
title: TIL(11/7)
date: 2017-11-7 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일

- ### Optional과 Exception

오늘 Optional을 이용해 Null에 대한 처리를 하는것과 RuntimeException 관련해서 오전에 공부했다.

오전에 Optional을 볼땐 완벽히 이해했는데..막상 다시 정리를 하려고 밤에 보니까 혼란스럽다. flatMap과 map에 대해서는 스트림을 공부하면서 정리했는데, Optional과 관련된 부분은 모르고 정리했다. 책에도 저런 내용은 없었는데..



집에 와서 학습일지를 쓰다보니 혼동이 오는데..한번 정리해보자면

```java
String version = computer.map(Computer::getSoundcardOptional)
                  .map(Soundcard::getUSBOptional)
                  .map(USB::getVersion)
                  .orElse("UNKNOWN");

String version = computer.flatMap(Computer::getSoundcard)
                   .flatMap(Soundcard::getUSB)
                   .map(USB::getVersion)
                   .orElse("UNKNOWN");
```

일단 두 코드 중 위의 내용에서 Computer::getSoundcardOptional을 map으로 받으면         Optional<Optional<Soundcard>>의 형태로 반환하게 되어서 문제가 생기는 것 같다.

이를 해결하기 위해 flatMap을 이용하는데 이는 Optional의 안의 값을 한번 꺼내고, 그 값을 Optional 형태로 만들어 주는것 같은데 내가 이해한게 맞는지 모르겠다.

일단 알긴 알겠는데 내가 직접 글로 설명하거나 남에게 이해를 시켜주려고 하니 혼동이 온다. 제대로 모르는거 같다.

Stream을 사용하면서 flatmap을 사용하는 경우는 리스트에서 꺼내올 해당 값의 null처리를 해주기 위해 Optional 형태로 반환하는 경우에 사용하는건가?



일단 기억을 더듬어서, 아까의 예제를 급하게 구현해봤는데..맞는지 모르겠다 내일 다시 해봐야지

```java

public static String getVersionOptional(Computer computer) {
		String version = UNKNOWN_VERSION;
		Optional<Computer> com = Optional.ofNullable(computer);
		return com.flatMap(Computer::getSCOptional).flatMap(Soundcard::getUSBOptional).map(USB::getVersion).orElse(version);
}
```





- ### MVC 2단계 구현

오늘 드디어 진도가 나가지 않던 2단계를 끝마쳤다. 큰 이슈는 없었던거 같은데 코드 컨벤션이 맞지 않는 부분이 꽤 있었고 나도 이해할 수 없는 코드들이 많았다. 단순히 구조를 바꾸는 과정에서 실수한게 많았던것 같다.

오늘 indent를 바꾸는 과정에서 이슈가 있었는데, 탭과 스페이스의 문제가 있었다. 파이썬에서나 생기는 문제라고 생각했는데.. 앞으로 코드를 복사해올때 주의해야 할 것 같다.



- ### RESTful API 파트 읽기


오후에 집중이 되지 않아서 Restful api 부분을 읽었다. 예전부터 중요하다 중요하다고 하는데 항상 개념을 명확히 설명하기가 좀 난감했다. 이번 기회에 짚고 넘어가야겠다.

내가 이해한 바로는 HTTP method(GET, POST, DELETE, PUT ..)을 통해 자원의 행위(insert, delete, select) 등을 명시적으로 알 수 있게 하고 URI 자체에 해당 자원의 이름을 넣어 어떤 자원이 사용되는지에 대해 알수있게 하는 일종의 규약?같은 느낌인데.. 이 부분에 대해서는 조금 더 공부해보고 질문을 해봐야 할 것 같다.



## 내일 할 일

- ### 코딩 테스트

줌 인터넷 코딩 테스트를 아마 내일 진행할 것 같다. 5시간 가량 걸리니 앞 뒤로 휴식, 밥 등을 고려하면 하루종일 걸리지 싶다.





------

최근 이상하게 정말 집중이 잘 되지 않는다. 아예 쉬던가, 다른 공부를 잠시 하던가 해야겠다.

오늘도 면담이 끝나고 어떻게 해야할지 방향을 잡아서 답답함은 덜었지만, 공부가 조금 손에 잡히지 않았다. 결국 뚜렷하게 뭔갈 하지 못하고 하루가 덧없이 지나갔는데 요즘 왜 이런지 모르겠다. 앉아있는 시간은 길지만 집중하는 시간은 손에 꼽는듯 하다.



