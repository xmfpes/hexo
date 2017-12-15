---
title: TIL-21
date: 2017-11-8 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한일



- ### RESTful API 정리

오늘 그간 엄청 많이 들었던 RESTful API에 대해 포비가 정리해둔 자료를 그냥 쭉 읽어봤다.

깊게 들어가진 않았는데 훑어보는 과정에서 이해가 안가는 부분이 좀 있었다. 

[Restful API 정리](https://xmfpes.github.io/web-network/restful-api) 깊게 정리한 건 아니고, 간략히 이게 무슨 개념인지 대답할 수 있을 정도로만 정리했다.



REST의 특징 중

캐싱, 계층형 구조에 대해 다룬 내용이었는데. 여러가지 명확히 이해되지 않는 내용들이 많았다.

- *REST의 가장 큰 특징중 하나는 HTTP라는 기존 웹 표준을 그대로 사용하기 때문에, HTTP가 가진 캐싱 기능 적용이 가능합니다. Last-Modified 태그나 E-Tag를 이용하면 캐싱 구현이 가능합니다.*
- *REST 서버는 다중 계층으로 구성될 수 있으며 보안, 로드밸런싱, 암호화 계층을 추가해 구조상의 유연성을 들 수 있고 PROXY 게이트웨이같은 네트워크 기반의 중간매체를 사용할 수 있게 합니다.*

HTTP의 캐싱이라면, 저번에 서블릿과 웹 서버 쪽에서 다룬 내용 같은데. 그 내용은 정적인 자원들을 브라우저상에서 캐싱해서 사용하는 느낌이었는데, REST와 캐싱간의 연관관계가 그림이 그려지지 않는다.

그리고 다중 계층으로 구성된다는 말이 API 요청이 들어오기 전에 다른 곳에서 먼저 그 요청을 받아서 보안이나 암호화를 한 다음 API를 처리한다는 이야기일까?



그리고 Richardson의 성숙 모델에서도 레벨 3의 내용이 이해되지 않았다.

레벨 0과 1의 경우 이해했고..레벨 2는 보편적인 Restful api에 대해 이야기하는거 같아 이해했지만 안전한 오퍼레이션에 대한 내용이 이해되지 않았다. 레벨 3은 정확히 어떤 느낌인지 잘 모르겠다. CU에게 설명을 들으면서 감을 잡긴 했지만 ..



- **안전한 오퍼레이션(예 GET)과 안전하지 않은 오퍼레이션 간의 강한 분리를 제공**하는 것이다.

레벨 2의 특징에서 나온 내용인데. GET 방식의 경우 오히려 안전하지 않은 오퍼레이션이라고 생각하고 있었는데..

- *다음 단계의 작업을 위한 리소스의 URI를 알려주는 것이다. 이 단계는 적용하면 클라이언트에 영향을 미치지 않으면서 서버를 변경하는 것이 가능해 진다는 것이다.*

위가 레벨 3의 설명인데, 클라이언트에 영향을 미치지 않으면서 서버를 변경하는것은 기존에 레벨 2에서도 제공하는 기능이 아닌가? 잘 모르겠다.



오늘은 코딩 테스트 때문에 별 거 안하고, 네트워크나 Rest에 대해서 조금 봤는데 역시 기초 지식이 부족해서 그런지 모르는것 투성이다. 아마 이 글을 포비가 읽을것 같은데, 내일 포비에게 잔뜩 질문하고 전체적인 네트워크나 웹 처리에 대한 그림을 머릿속에 그릴 수 있으려면 어떻게 공부를 해나가야 할지에 대해 조언을 구해야겠다.



저번에 이야기할때도 L4캐시는 4계층 까지만 어쩌고 저쩌고..해서 포트만 분류할 수 있고, L7 캐시는 7계층 다 커버할 수 있다고 하는 이야기를 했는데 그런 장비들은 또 뭐고 게이트웨이는 뭐고 프록시는 뭐고. 모르는것 투성이다.

네트워크 책을 보면 단편적인 내용들.. OSI 7계층에 각 계층은 뭐고, 각 계층에서 어떤게 있는지 정도야 그냥 외우면 그만인데.. 항상 그렇게 넘어가다 보니 전체적인 그림이 머릿속에 그려지지 않는다.

 





아 그리고 오늘 넥스트스텝의 문서들을 보면서 흥미로운 코드를 발견했는데, 이때까지 RESTful API 형태로 제공해주는 댓글같은 형태들을 항상 Repository를 따로 만들고 컨트롤러에서 처리를 해주었는데 오늘 링크를 보니까.. 아래와 같은 코드가 있었다.

```java
@RepositoryRestResource(collectionResourceRel = "account", path = "account")
public interface AccountRepository extends JpaRepository<Account, Long>
```

확실하진 않은데, 내용을 대강 보니 RestController의 역할을 Jpa에서 바로 해 줄 수 있는 듯 하다. 아닌가? 여튼 문서를 찾아보고 다음에 적용해보아야 겠다. Jpa의 각각의 메소드 자체가 API로 제공되어진다면 기존의 쓸데없는 컨트롤러와 코드들이 다 사라질 듯 하다.



이번에 MVC 프레임워크 3단계 구현을 마치고, 레벨 4가 마무리될때쯤에는 ORM도 한번 유사하게 제작해보고, 가능하다면 위와 같은 어노테이션들도 직접 Reflection을 통해 구현해보아야겠다. 아직 잘 몰라서 그런지 그냥 하면 되지 않을까? 싶은데 브라이언이 고생하는거 보면 힘들거 같기도 하다.

하지만 이번에 해보면 스프링의 구조에 대해 거의 완벽히 알수 있지 않을까 싶다.



- ### 줌인터넷 코딩 테스트


오늘 총 4문제, 5시간의 엄청난 코딩 테스트를 쳤다.

점심먹고 쳐서 저녁먹을때까지 했는데 참 집중이 요즘 왜이렇게 안되는지 모르겠다.

집중은 안되서 이것저것 딴짓하면서 풀었는데.. 일단 다 풀기는 했다.

요즘은 뭘 하려고 마음을 다 잡으려치면 코딩 테스트, 정신좀 차리려고 하면 면접, 정신 차리려고 하면 시험.. 거기다 이제 결과도 쭉 나올텐데 포비의 말대로 멘탈을 잘 잡아야 겠다.

- ### 1번

1번 문제는 캔디의 절반을 동생에게 주고, 자신이 최대한 다양한 사탕을 먹을 수 있는 사탕의 갯수를 구하는 문제였다. 사탕은 위와같이 배열의 형태로 주어지고 숫자가 사탕의 종류이다. 즉, 문제에서는 80 사탕, 1000…, 1234… 사탕 3종류가 존재하고, 10개중 5개를 동생에게 주고 자신은 최대한 다양한 사탕을 남겨서 먹을 수 있어야 한다.

```java
public static void main(String[] args) {
    int t []  = {80, 80, 1000000000, 80, 80, 80, 80, 80, 80, 123456789};
    int count = solution(t);
    System.out.println(count);
}
public static int solution(int[] T) {
    int expectedCandiesCount = T.length/2;
    int candyCount = Arrays.stream(T).distinct().boxed().collect(Collectors.toList()).size();
    return  candyCount > expectedCandiesCount ? expectedCandiesCount : candyCount;
    
}
```

그냥 단순하게 생각해서.. 배열의 중복을 distinct로 제거하면 각 종류별 사탕이 남을거고, 거기서 만약 기대하는 사탕 수 보다 종류가 많다면, 기대하는 사탕 수가 정답이고, 그것보다 적다면 그냥 그 중복을 제거한 사탕 갯수가 답이 될 것이라 생각해서 그냥 위와같이 간단하게 구현했다.

- ### 2번

2번은..문제가 명확하지 않아서 구현하는데 고생을 했는데. 아직도 문제는 잘 모르겠다.

문자가 주어졌을 때, 주어진 숫자 만큼으로 문자를 잘라서 몇개로 자를 수 있는지, 자를 수 없다면 -1을 리턴하는 문제였는데. 영어기도 하고 조건이 명확하지 않았다.

```java
public static int solution(String s, int K) {
    List<String> list = new ArrayList<String>();
    List<String> tokenList = getToken(s);
    int currentPointer = 0;
    StringBuilder st = new StringBuilder();
    while(currentPointer < tokenList.size()) {
        if(st.length() + tokenList.get(currentPointer).length() + 1 < K) {
            if(st.length() != 0) {
                st.append(" ");
            }
            st.append(tokenList.get(currentPointer++));
        }else {
            list.add(st.toString());
            st = new StringBuilder();
        }
    }
    return list.size();
}
public static List<String> getToken(String s){
    List<String> list = Arrays.asList(s.split(" "));
    return list;
}
```

일단 저렇게 구현하니 테스트 케이스는 통과하길래 제출했는데, 맞는진 모르겠다.

- ### 3번

이 문제는, 우선 각각 연결된 그래프들이 주어지고, 1번을 기준으로 거리가 1인 것들의 개수, 2인 것들의 개수 3인 것들의 개수…해서 쭉 구하는 문제였다.

우선 배열이 int [] T = {9, 1, 4, 9, 0, 4, 8, 9, 0, 1}; 같은 형태로 주어지는데 이건 0번 노드가 9번과 연결되고 1번 노드가 1번, 2번 노드가 4번과 연결되고.. 3번 노드가 9번과 연결되고… 4번 노드가 0번과 연결..



결국 1번 노드는 9번과 연결되기 때문에 길이가 1인 건 1개

9번 노드는 0번, 3번, 7번과 연결되기 때문에 길이가 2인건 0번 3번 7번 3개

이런식으로 구해가는 문제였는데. 어떻게 풀지 고민을 많이 했는데 그냥 무식하게 풀었다.

```Java
public static void main(String[] args) {
    int [] T = {9, 1, 4, 9, 0, 4, 8, 9, 0, 1};
    solution(T);
}
public static int[] solution(int[] T) {
    int [] count = new int[T.length-1];
    int currentPointer = 0;
    List<Integer> list = new ArrayList<Integer>();
    list.add(1);
    while(currentPointer<T.length) {
        List<Integer> tempList = new ArrayList<Integer>();
        tempList.addAll(list);
        list = new ArrayList<Integer>();
        for(int i=0; i<T.length; i++) {
            if(tempList.contains(T[i]) && i != T[i]) {
                count[currentPointer]++;
                list.add(i);
            }
        }
        currentPointer++;
    }
    return count;
}
```

처음에 list에 1을 넣어주고, 배열의 값인 T[i]가 그 리스트에 들어있다면, 그 값을 추가하고 그 값을 tempList에 넣어준다.

그리고 반복문을 통해 그 다음 루프에서 그 tempList에 있는 값들을 체크해서 반복하는 식으로 풀었다.

- ### 4번

4번은 prefix와 suffix를 구해서. 예를 들어서

codility라고 치면 prefix는 "", "c", "co" … "codilit" suffix는 "", "y", "ty", "ity" … "odility" 가 되고, 두개가 일치하는 값의 길이를 구하는 문제였다.

```java
public static int solution(String s) {
    List<String> prefixList = getPrefix(s);
    List<String> suffixList = getSuffix(s);
    suffixList.forEach(System.out::println);
    for(int i=prefixList.size()-1; i>=0; i--) {
        if(prefixList.get(i).equals(suffixList.get(i))) {
            return prefixList.get(i).length();
        }
    }
    return -1;
}
public static List<String> getPrefix(String s){
    List<String> list = new ArrayList<String>();
    for(int i=0; i<s.length(); i++) {
        list.add(s.substring(0, i));
    }
    return list;
}
public static List<String> getSuffix(String s){
    List<String> list = new ArrayList<String>();
    for(int i=0; i<s.length(); i++) {
        list.add(s.substring(s.length()-i, s.length()));
    }
    return list;
}
```



일단 4문제 다 풀긴 했는데 테스트케이스가 주어지지 않아서 확인할 수가 없었다. 아마 시간 초과라던가, 테스트케이스를 통과하지 못하는 예외가 많이 발생할 것 같다.

실질적으로는 1, 2문제 정도만 맞지 않았을까? 포비라면 어떤식으로 위의 문제를 설계하고 풀어나갈지 궁금하다. 

알고리즘의 경우 공부를 좀 접고 있었는데, 매주 코딩테스트를 치다보니까 억지로 공부하는 느낌이다. 