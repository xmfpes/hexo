---
title: 정규 표현식(Regular Expression)
date: 2017-10-31 22:01:08
tags: Regular Expresstion
categories: 
- 기타
- AWS
- Lambda
---

## Regular Expression

RegExp는 case sensitive, 대소문자를 구분한다.



- ### ^(캐럿) 기호

^who 라고 정의할 시 who로 시작되는 소스를 검출 할 때 사용한다.

Source : who is who

RegExp : ^who

First match : **who**  is who

All matches: **who** is who

```java
RegExp reg = new RegExp();
reg.parseString("who is who", "^who");

Matcher m = Pattern.compile(regExp).matcher(text);
List<String> allMatches = new ArrayList<String>();
while (m.find()) {
  allMatches.add(m.group());
}
allMatches.stream().forEach(System.out::println);

//결과
//who
```



- ### $ 기호

who$ 로 표현식을 정의할 시 who로 끝나는 소스를 검출할 때 사용

Source : who is who

RegExp : who$

First match : who is **who**

All matches : who is **who**



### $ 기호가 표현식에 사용되는데, 이 문자를 찾는 경우라면?

escape(\\)이용해서, 기존 $역할을 해제하고 문자로 인식시켜 찾을 수 있다.



Source : $12$ \-\ $25$

위의 문자열에서 $를 검출한다고 가정하자, 근데 $자체가 표현식의 기호이기 때문에 그냥 사용하면 찾을 수 없다.

RegExp :  \\$($를 인식하게 하기 위해서 슬래시를 하나 추가해야 함)

First match : **$**12$ \-\ $25$

All matches: **$**12**$** \-\ **$**25**$**

```java
RegExp reg = new RegExp();
reg.parseString("$12$ -\\ $25$", "\\$");

Matcher m = Pattern.compile(regExp).matcher(text);
List<String> allMatches = new ArrayList<String>();
while (m.find()) {
  allMatches.add(m.group());
}
allMatches.stream().forEach(System.out::println);
//결과
//$
//$
//$
//$
```

$로 시작되는 문자열을 찾으려면

```java
reg.parseString("$12$ -\\ $25$", "^\\$");
```

$로 끝나는 문자열을 찾으려면

```java
reg.parseString("$12$ -\\ $25$", "\\$$");
```



- ### . 기호

.이라는 기호는 어떠한 문자던, 공백이건 특수문자건 모든 문자를 가리키는 기호.(와일드카드 같은 역할)

Source : who is who

RegExp : .

First match : **w**ho is who

All matches : **who is who**



Source : who is who

RegExp : …

First match : **who** is who

All matches : **who is wh**o

3개씩 묶여서 되므로, 마지막의 o는 선택되지 않는다.

. 기호도 \\(이스케이프)를 통해 . 문자를 검출할 수 있다.



- ### [] brackets

특정 문자를 지정할 수 있다.

Source : How do you do?

RegExp : [oyu]

First match : H**o**w do you do?

All matches : H**o**w d**o** **you** d**o**?

[] 안에 여러 문자가 들어가도 하나로 인식한다. [o], [y], [u] 중 하나가 매칭되는 걸 인식하는 구조이다.



Source : How do you do?

RegExp : [oyu]\[yow]

First match : H**ow** do you do?

All matches : H**ow** do **yo**u do?



-기호를 이용해 Range를 설정해 줄수도 있다.

Source : ABCDEFGH

RegExp : [A-D] == [ABCD]

First match : **A**BCDEFGH

All matches : **ABCD**EFGH



[C-Ka-d2-6] 이런식으로, 여러개를 설정해 줄 수도 있다.



#### [] 안에서 ^ 기호를 사용할 수 있는데, 이때는 not 부정의 의미이다.

[^CDghi45]라고 정규 표현식을 사용하면, 저 안의 문자들을 제외한 나머지를 선택하게 된다.



- ### Sub pattern|(Pipe 기호)

Source : Monday Tuesday Friday

RegExp : (on|ues|rida)

First match : M**on**day Tuesday Friday

All matches : M**on**day T**ues**day F**rida**y



만약 요일을 다 추출하고 싶다면, day를 공통으로 빼 주면 된다.

Source : Monday Tuesday Friday

RegExp : (on|ues|rida)day

First match : **Monday** Tuesday Friday

All matches : **Monday Tuesday Friday**



- ### Quantifiers(수량자)

  - \* 수량자

  0개 ~ 여러개를 가진다. 

  Source : aabc abc bc

  RegExp : a*b

  First match : **aab**c abc bc

  All matches : **aab**c **ab**c **b**c

  a가 0개여도 되고, 여러개를 가져도 되면서 b로 끝나는 문자열을 찾는다.

  ​

  - \+ 수량자

  1개 ~ 여러개를 가진다.

  Source : aabc abc bc

  RegExp : a+b

  First match : **aab**c abc bc

  All matches : **aab**c **ab**c bc

  a를 1개 이상 가지면서 b로 끝나는 문자열을 찾는다.

  a가 반드시 1개 이상이어야 한다.

  ​

  - ? 수량자

  없거나 1개인 경우

  Source : aabc abc bc

  RegExp : a?b

  First match : a**ab**c abc bc

  All matches : a**ab**c **ab**c **b**c

  ​

### 여러 기호들과 수량자의 결합

Source : -@- \*\** -- "*" -- *** -@-

RegExp : -A*-

First match : -@- \*\** **—** "*" -- *** -@- (--)

All matches : -@- \*\** **—** "*" **—** *** -@- (--, --)

A는 있어도 되고 없어도 되므로 위와 같은 결과가 나온다.



RegExp : [^ \]+

대괄호 사이에 ^은 부정의 의미이고, 공백의 부정이므로 공백을 제거하고 하나 이상이면 된다.

따라서 공백을 제외한 모든 문자열을 가져올 수 있다.











1개 ~ 여러개를 가진다.

Source : aabc abc bc

RegExp : a*b

First match : **aab**c abc bc

All matches : **aab**c **ab**c bc

a를 1개 이상 가지면서 b로 끝나는 문자열을 찾는다.

a가 반드시 1개 이상이어야 한다.

