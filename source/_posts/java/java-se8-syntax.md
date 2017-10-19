---
title: 자바 8 문법 - Lambda Expression And Stream, ::(Double colon Operation)
date: 2017-10-18 18:06:08
tags: 
- Lambda
- Stream
categories:
- Java
---
## Java SE 8 Lambda Express / Stream

![java](https://www.3pillarglobal.com/wp-content/uploads/2016/03/java8_600x600-300x300.png)

### Lambdas Expression에서 ::(Double colon Operation) 과 Comparator 살펴보기

[참고 문서](http://www.baeldung.com/java-8-double-colon-operator)
```java
Comparator c = (Computer c1, Computer c2) -> c1.getAge().compareTo(c2.getAge());
```

```java
Comparator c = (c1, c2) -> c1.getAge().compareTo(c2.getAge());
```
위의 코드는 Comparator의 comparing 메소드와 Double Colon Operation(::)으로 더 간단하게 만들 수 있습니다.

Comparator의 comparing 메소드

![comparing](/images/java/comparing.png)

위의 메소드와 ::를 이용해서 아래와 같이 간단히 표현할 수 있습니다.

```java
Comparator c = Comparator.comparing(Computer::getAge);
```
Double colon Operation(::)에 대해 간단히 설명하자면
메소드 참조를 사용할 때 타겟의 참조(객체)는 구분 기호 앞, 메소드 이름은 콜론 뒤에 오게 됩니다.




#### 예제

double colon을 이용해 getAge에 대한 메소드 참조를 받아와 사용하는 예제

```java
Computer::getAge;

Function<Computer, Integer> getAge = Computer::getAge;
Integer computerAge = getAge.apply(c1);
```

System.out 참조 안의 print 메소드에 파라미터로 list 안의 값을 하나씩 넘겨 출력하는 :: 예제

```java
Computer c1 = new Computer(2015, "white");
Computer c2 = new Computer(2009, "black");
Computer c3 = new Computer(2014, "black");
Arrays.asList(c1, c2, c3).forEach(System.out::print);
```

#### 여튼 ::(Double colon) 연산자에서 간단히 말하면 타겟의 참조(객체) 가 :: 앞, 메소드 이름이 :: 뒤에 온다는 것만 기억하면 됨.

## Stream 예제

- ### Stream을 이용해 List<Object> 정렬해보기

stream의 sorted 메소드를 이용해 정렬하고, 값을 출력하는 예제를 구현해보겠습니다.

sorted 메소드는 stream을 리턴해주고, 파라미터가 없어도 되고, Comparator를 받을수도 있습니다.

파라미터가 없으면 아마 디폴트로 정렬 할 거 같고, 객체를 정렬할것이기 때문에 정렬하는 기준을 설정해해줍니다. 이를 위해 Comparator를 생성해 넣어줍니다.

![sorted](/images/java/sorted.png)

Comparator의 comparing 메소드를 이용해 Comparator를 받아옵니다.

위의 comparing 메소드를 통해 Comparator를 받아와 넣어주면 그 Comparator를 이용해 기준을 설정하게 됩니다.

아래의 코드는, 스트림을 이용해 리스트를 정렬하고 forEach를 통해 바로 출력하는 예제입니다.

```java
list.stream().sorted(Comparator.comparing(Car::getPosition)).forEach(car -> System.out.println(car.getCarName()));
```



- ### Stream을 이용해 List<Object>의 최대값 뽑아오기
Stream에 max라는 메소드를 이용합니다.

max와 min 메소드는 아래의 형태를 가지고 있습니다.

![max](/images/java/max.png)

Optional은 Object를 extends하는 클래스입니다. 여튼, max의 파라미터로 Comparator를 넣어줘야 하는데, 이를 생성하기 위해 위에서 사용했던 Comparator의 comparing 메소드를 이용합니다.





아래는 포지션을 기준으로, Car 객체 중 최대값을 가진 객체를 얻어내는 코드입니다.

```java
List<Car> list = car1, car2....;

Car maxCar = list.stream().max(Comparator.comparing(Car::getPosition)).get();
System.out.println(car.getCarName());
```
