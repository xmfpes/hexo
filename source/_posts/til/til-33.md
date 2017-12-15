---
title: TIL-33
date: 2017-11-30 23:10:03
tags:
- TIL
categories:
- 기타
- TIL
---

![til](/images/til/til.jpg)

## 오늘 한 일

- ### JPA 고급 매핑

소셜 로그인으로 로그인한 유저의 회원가입을 위해서 이래저래 해보고 있는데 여러가지 제약사항이 많다.

현재 들어온 요청이 어떤 소셜 로그인 타입(Github, Facebook)인지

```java
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "DTYPE")
```

JPA에서 상속 관계 매핑을 이용해 Member, GithubMember, FacebookMember를 단일 테이블로 매핑하려고 하는데

`InheritanceType.SINGLE_TABLE`로 설정해주어서 생성까지는 제대로 되는것을 확인했는데 MemberRepository를 그대로 활용해서 데이터를 가져올 수가 없었다.

```java
public Optional<Member> findByFacebookId(String facebook_id);
```

상속으로 확장한 facebookMember가 가진 facebookId값을 찾을수가 없어서 에러가 나는 상황인데 이를 어떻게 처리해야 할 지 모르겠다.



- ### 이력서 작성

이번에 지원할 회사들에 대한 이력서를 새로 쓰기 시작했다. 이때까지 한 활동들에 대해 정리가 안 되어있는 느낌인데, 이번 기회에 정리해야겠다.



## 내일 할 일

- ### 이력서 작성

얼른 이력서 작성을 마무리하고, 제출해보자.

- ### 소셜 로그인 기능 구현

소셜 로그인 분기별로, 회원가입 처리를 얼른 구현해보자.