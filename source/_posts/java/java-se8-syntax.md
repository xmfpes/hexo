---
title: 자바 함수형 프로그래밍 - Lambda Expression And Stream
date: 2017-10-18 18:06:08
tags: 
- Lambda
- Stream
categories:
- Java
- Functional
---
## Java 함수형 프로그래밍

![](/images/java/java.png)

### 함수형 프로그래밍

함수형 프로그래밍에서는 side effect(부수효과)가 없는 프로그래밍을 보장해 준다.

```java
List<Integer> list = {1,2,3,4,5....}

List.add(6);
List.remove(5);
```

위와 같은 코드에서는, list가 보장되지 않는다.

list에 add, remove를 한다고 하면 원본의 리스트가 변형되게 된다.

하지만, 함수형 프로그래밍에서는 새로운 list를 생성해서 기존의 list가 변형되지 않는다.

즉 side effect가 없는 것이 장점이라고 할 수 있다.


기존의 함수형 프로그래밍 개념이 없을때의 리스트의 값 출력

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

for (int number : numbers) {
    System.out.println(number);
}

Iterator<Integer> iterator = list.iterator();
while(iterator.hasNext()){
  String name = iterator.next();
  System.out.println(name);
}

numbers.forEach(new Consumer<Integer>() {
    public void accept(Integer value) {
        System.out.println(value);
    }
});
```

람다가 없을 때는 위와 같은 방법으로 리스트의 값을 출력해주었다.


### 람다를 이용한 리스트의 값 출력

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

numbers.forEach((Integer value) -> System.out.println(value));
numbers.forEach(value -> System.out.println(value)); // Type 추론이 가능해 Type 생략 가능
numbers.forEach(System.out::println); // :: 연산자 활용 가능
= numbers.forEach(x -> System.out.println(x));
```

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



### 예제

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


## Java Stream

Stream은 내부 반복자를 사용하므로 병렬 처리가 쉽다.
기존의 while문에서 외부의 값을 이용해 반복을 하는 그런 처리가 모두 외부 반복자를 이용하는 것이다.

스트림은 컬렉션의 요소에 대해 중간 처리(매핑, 필터링, 정렬), 최종 처리(반복, 카운팅, 평균, 총합 구하기)를 수행 할 수 있다.

스트림에서는 중간 처리가 바로 되는 것이 아니라, 최종 처리가 시작되기 전까지는 중간 처리는 지연된다.

### Stream
객체 요소 처리 스트림
### IntStream
Int형 데이터 요소 처리 스트림
### LongStream
Long형 데이터 요소 처리 스트림
### DoubleStream
Double형 데이터 요소 처리 스트림

## Stream의 기능들

### 1. 필터링(distinct(), filter())

- #### distinct()

중복을 제거한다. Object.equals()가 true이면 동일 객체로 판단하고 제거한다.

```java
names.stream()
.distinct()
.forEach(System.out::println)
//객체 내에서 같은 중복 값 제거
```

- #### filter()

주어진 Predicate가 true를 리턴하는 요소만 필터링한다. filter에서 false인 경우는 필터링 된다.
```java
names.stream()
.filter(n -> n.startsWith("신"))
.forEach(n -> System.out.println(n));
//객체 내에서 신 이라는 이름으로 시작하는 사람만 출력하도록 필터링
```

### 2. 매핑 (flatMapXXX(), mapXXX(), asXXXStream(), boxed())

- #### flatMapXXX() 메소드

요소를 대체하는 복수개의 요소들로 구성된 새로운 스트림을 리턴한다.
```java
List<String> inputList = Arrays.asList("java8 lambda", "stream mapping");
inputList.stream()
.flatMap(data -> Arrays.stream(data.split(" ")))
.forEach(word -> System.out.println(word));
//split으로 잘린 4개의 요소를 가진 스트림을 반환한다.
// java8, lambda, stream, mapping 출력
```

- #### mapXXX() 메소드

요소를 대체하는 요소로 구성된 새로운 스트림을 리턴한다.

아래에서 Student는 getScore라는 int형 점수를 반환하는 메소드를 가진다고 가정한다.
학생 2명이 들어있고, 각각 10점과 20점의 점수를 가진다면 점수를 가지는 Int형 스트림을 반환하게 된다.

```java
List<Student> studentList = Arrays.asList(new Student(), new Student());
studentList.stream()
.mapToInt(Student::getScore)
.forEach(System.out::println);
//10, 20 출력
```

- #### asDoubleStream(), asLongStream(), boxed() 메소드

asDoubleStream() 메소드는 다른 타입의 스트림 요소(long, int)를 변환해서 Double형 스트림을 생성해 반환합니다.

마찬가지로 asLongStream()도 Long형 스트림으로 변환해 반환합니다.

boxed() 메소드는 long, double 원시 타입 요소를 Integer, Double, Long으로 Boxing해서 Stream에 생성합니다.

```java
int[] intArray = {1,2,3,4,5,6}
IntStream intStream = Arrays.stream(intArray);
IntStream
.asDoubleStream()
.forEach(d -> System.out.println(d));
// 1.0, 2.0, 3.0, 4.0 ... 출력

intStream = Arrays.stream(intArray);
intStream
.boxed() // Stream<Integer> 생성
.forEach(obj -> System.out.println(obj.intValue()))
//intValue를 사용할 수 있다.
```
### 3. 정렬(sorted())

객체 요소인 경우 sorted()를 사용하려면 객체 내부에 Comparable을 구현하거나, Compator를 파라미터로 넣어서 정렬을 사용할 수 있다.

sorted 메소드는 stream을 리턴해주고, 파라미터가 없어도 되고, Comparator를 받을수도 있습니다.

파라미터가 없으면 디폴트로 정렬, 객체를 정렬하는 경우 정렬 기준을 위해 Comparator를 파라미터로 넣어주어야 합니다.

![sorted](/images/java/sorted.png)

Comparator의 comparing 메소드를 이용해 Comparator를 받아옵니다.

위의 comparing 메소드를 통해 Comparator를 받아와 넣어주면 그 Comparator를 이용해 기준을 설정하게 됩니다.

아래의 코드는, 스트림을 이용해 객체 리스트를 position을 기준으로 정렬하고 forEach를 통해 바로 출력하는 예제입니다.

```java
list.stream().sorted(Comparator.comparing(Car::getPosition)).forEach(car -> System.out.println(car.getCarName()));
```

### 4. 루핑(peek(), forEach())

루핑은 요소 전체를 반복하는 것을 말하며 peek(), forEach() 두가지 존재.
peek()는 중간 처리 메소드 forEach()는 최종 처리 메소드이다.

peek()는 중간 처리 단계에서 전체 요소를 루핑하면서 추가 작업을 위해 사용한다.
최종 처리 메소드가 실행되지 않으면 지연되기 때문에 반드시 최종 처리 메소드가 호출되어야 동작한다.

```java
intStream
  .filter( a -> a%2 == 0)
  .peek( a -> System.out.println(a))
//최종 처리가 일어나지 않아 동작하지 않음
//위와 같은 상황에서는 peek()가 최종 처리 메소드가 아니므로 스트림이 동작하지 않는다.
int sum = Arrays.stream(arr).filter(a -> a%2 == 0).peek(System.out::println).sum();
		System.out.println("총합 : " + sum);
//최종 처리인 sum이 구현되어 정상 작동함
```

peek와 달리 forEach는 최종 처리 메소드이므로, 이후에 sum같은 다른 최종 메소드를 호출할 수 없다.

### 5. 매칭(allMatch(), anyMatch(), noneMatch())

스트림에서는 최종 처리 단계에서 요소들이 특정 조건을 만족하는지 조사할 수 있도록 세가지 매칭 메소드를 제공한다.

allMaych()는 모든 요소들이 매개값으로 주어진 Predicate의 조건을 만족하는지 조사하고

anyMatch()는 최소한 한개의 요소가 매개값으로 주어진 Predicate의 조건을 만족하는지 조사한다.

noneMatch()는 모든 요소들이 매개값으로 주어진 Predicate의 조건을 만족하지 않는지 조사한다.

```java
int[] arr = {1,2,3,4,5,6}
boolean result = Arrays.stream(arr)
				.allMatch(a -> a%2 == 0);
System.out.println("모두 2의 배수니? : " + result);
//true

result = Arrays.stream(arr)
		.anyMatch(a -> a%3 == 0);
System.out.println("하나라도 3의 배수인가? : " + result);
//true

result = Arrays.stream(arr)
		.anyMatch(a -> a%3 == 0);
System.out.println("3의 배수가 없는가? : " + result);
//false
```

### 6. 기본 집계(sum(), count(), average(), max(), min())

count()와(long) sum()(double) 메소드를 제외한 집계 메소드들은 각각 Optional, OptionalXXX 클래스 타입을 리턴한다.

이는 값을 저장하는 값 기반 클래스 들이며, 이 객체에서 값을 얻기 위해선 get(), getAsDouble(0), getAsInt(), getAsLong()을 호출 해야 한다.

```java
long count = Arrays.stream(new int[] {1,2,3,4,5})
      .filter(n -> n%2 == 0)
      .count();

double avg = Arrays.stream(new int[] {1,2,3,4,5})
      .filter(n -> n%2 == 0)
      .average()
      .getAsDouble();
```

### Optional 클래스

값을 저장할 뿐만 아니라, 존재하지 않는 경우 디폴트 값을 설정하거나 집계 값을 처리하는 Consumer도 등록할 수 있다.

만약 average의 값을 출력하려고 하는데, 요소가 0인 경우라면? 예외가 발생할 것이다.
아래와 같이 Optional 클래스에 값을 담아 예외 처리를 해 줄 수 있다.

```java
double avg = Arrays.stream(new int[] {1,2,3,4,5})
			.filter(n -> n%2 == 0)
			.average()
			.getAsDouble();

List<Integer> intList = new ArrayList<>();
double avgDouble = intList.stream()
			.mapToInt(Integer::intValue)
			.average()
			.getAsDouble();
System.out.println(avgDouble);
// 요소가 없어 평균이 없다. 예외 발생

//첫번째 예외처리 방법
OptionalDouble optional = intList.stream()
		.mapToInt(Integer::intValue)
		.average();
if(optional.isPresent()) {
	System.out.println(optional.getAsDouble());
}else {
	System.out.println("no val");
}

//두번째 예외처리 방법. 디폴트 값을 설정한다
double avg = Arrays.stream(new int[] {1,2,3,4,5})
			.filter(n -> n%2 == 0)
			.average()
			.orElse(0.0);

//세번째 예외처리 방법 값이 있을때만 출력한다.
Arrays.stream(new int[] {1,2,3,4,5})
			.filter(n -> n%2 == 0)
			.average()
			.ifPresent(a -> System.out.println(a));
```

### 7. 커스텀 집계(reduce())

다양한 집계 결과물을 위한 reduce() 메소드를 제공한다.

아래는 학생들의 총 점을 구하는 예제 코드이다.

```java
int sum = studentList.stream()
.map(Student::getScore)
.reduce((a, b) -> a+b)
.get();
//요소가 없다면 NoSuchElementException 발생

int sum = studentList.stream()
.map(Student::getScore)
.reduce(0, (a, b) -> a+b)
//요소가 없다면 디폴트로 0 리턴
```

### 8. 수집(collect())

스트림은 요소들을 필터링하거나 매핑한 후 요소를 수집하는 최종 처리 메소드인 collect()를 제공한다.
이를 이용해 필요한 요소만 컬렉션에 담거나 그룹핑 할 수 있다.

```java
Stream<Student> totalStream = totalList.stream();//전체 리스트에서 스트림을 얻는다.
Stream<Student> maleStream = totalStream.filter(s -> s.getSex() == Student.Sex.MALE);
//남학생만 필터링해서 스트림을 얻는다.
Collector<Student, ?, List<Student>> collector = Collectors.toList();
//List에서 Student를 수집하는 Collector를 얻는다.
List<Student> maleList = maleStream.collect(collector);
//collect 메소드를 이용해 Student 리스트를 수집한다.

//위의 코드를 하나로 합친 코드
List<Student> maleList = totalList.stream()
.filter(s -> s.getSex() == Student.Sex.MALE)
.collect(Collectors.toList());

//전체 리스트에서 여학생 리스트를 HashSet으로 받아오는 코드
Set<Student> femaleSet = totalList.stream()
.filter(s -> s.getSex() == Student.Sex.FEMALE)
.collect(Collectors.toCollection(HashSet::new));
```

위처럼 컬렉션에 담는 것 뿐만 아니라, 사용자 정의 컨테이너 객체에도 매핑할 수 있다.

그 방법은 알아서 찾아보도록 하자. 어렵다.

```java
//관련 검색 키워드
//Supplier<Type>
//BiConsumer<Type, Type>

MaleStudent maleStudent = totalList.stream()
.filter(s -> s.getSex() == Student.Sex.MALE)
.collect(MaleStudent::new, MaleStudent::accumulate, MaleStudent::combine);
```
첫번째 메소드에서 컨테이너 객체를 생성하고, 두번째에서 요소를 수집하며, 세번째에서 결합한다(병렬 처리에서 나누어서 처리할 경우) 자세한 건 알아서 찾아보도록 하자.

### collect의 grouping
collect에서 단순히 요소를 수집하는것 이외에도 grouping이 가능하다.

#### 그룹핑 예제

다음은 학생들을 성별로 그룹핑하고 그룹에 속하는 학생 리스트를 만들어 enum인 성별을 키로 학생 리스트를 값는 Map을 생성하는 코드이다.

```java
Map<Student.Sex, List<Student>> mapBySex = totalList.stream()
.collect(Collectors.groupingBy(Student::getSex));
```

#### 그룹핑 후 리덕션 예제

```java
//성별로 평균 점수 계산하는 Map 얻기
Map<Student.Sex, Double> mapBySex = totalList.stream()
.collect(
Collectors.groupingBy(
  Student::getSex, Collectors.averagingDouble(Student :: getScore)));
System.out.println("남학생 평균 점수 : " + mapBySex.get(Student.Sex.MALE));

//성별을 쉼표로 구분한 이름을 저장하는 Map 얻기
Map<Student.Sex, String> mapByName = totalList.stream()
.collect(
Collectors.groupingBy(Student::getSex,
Collectors.mapping(Student::getName, Collectors.joining(","))));
System.out.println("남학생 전체 이름 : " + mapByName.get(Student.Sex.MALE));
```

### Stream 예제

- ### 배열로부터 스트림 얻기

```java
String[] strArray = { ... };
Stream<String> strStream = Arrays.stream(strArray);
```
- ### Stream을 이용해 학생들의 점수의 평균 구하기

```java
  List<Student> studnetList = Arrays.asList(....);
  double avg = studentList.stream()
  .mapToInt(Student::getScore) //중간 처리 학생 객체를 학생 점수로 매핑
  .average() //최종 처리(평균 점수 구하기)
  .getAsDouble();
```

- ### Stream을 이용해 List<Object> 단순 출력하기

단순히 출력만 하는거라면, 아래와 같이 list의 스트림을 받아 forEach로 출력해주면 된다.

```java
list.stream().forEach(System.out::println);
```

- ### Stream을 이용해 조건에 맞는 값만 출력하기

filter를 이용해 조건에 맞는 값만 출력한다 filter 메소드는 아무런 결과를 리턴하지 못하므로 중개 연산이라고 한다.

아래 코드에서는 i>8보다 큰 값만을 출력해주는 예제 코드이다.

```java
list.stream().filter(i -> i>8).forEach(System.out::println);
```

- ###  Map을 이용해 List<Object> 객체의 특정 get 메소드의 리턴값을 리스트로 내보내기

forEach는 리턴 타입이 없는 void 형태라 값을 가져오기 곤란하다.
객체의 이름을 리스트 형태로 내보내거나 하는 경우, forEach에서는 처리하기 힘들 것이다.

그래서 아래의 예제에서 쓰는 map을 이용한다.

map을 이용하면 스트림에서 처리하는 값들을 중간에 변경할 수 있다.

```java
List<String> nameList = studentList.stream().map(Student::getName).collect(Collectors.toList());
```

collect 메소드에 파라미터로 Collector<? super T,A,R> collector를 넣어주어, 리스트 형태로 반환받을 수 있다.


- ### Stream을 이용해 List<Object> 정렬해보기





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