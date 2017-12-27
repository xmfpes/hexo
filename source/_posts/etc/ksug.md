---
title: KSUG 세미나
tags: 
- Spring
- KSUG
categories:
- Java
- Spring
---

11/26 KSUG 

![](/images/spring/ksug.png)

## Java 9 new feature

### 세미나 이후 내용을 정리해야하는데..?? 미완성


Javadocs HTML5 지원

Unicode 7.0/8.0 추가

PKCS12 규격 key chain store

SHA 3 hash algorithm 추가



XML catalog API 지원

DRBG-Based SecureRandom 키 생성 지원(SHA 1 기반에서 변경)



- ### Major Featrues

  - Modular source code

  package 위에 module의 개념이 등장한다. module들이 패키지를 가지는 형태로 바뀌게 된다.

  module 시스템화가 되면, 기존의 패키지 구조의 프로젝트들은 다 변경이 필요하게 된다.

  module 구조가 등장하면서 depth가 더 깊게 바뀜, module 내부엔 module.info 파일이 생김.

  jlink가 추가, module에 대한 version numbering 지원 maven, grade 기반에서 버전관리 가시성을 지원해주지 않았는데, 이번에 jlink를 통해 지원

  ​

  - Multi-Release Jar File

  ​

  - jshell : REPL(Read-Eval-Print-Loop) Feature 지원

  ​

  - More Concurrency updates

  ​

  - Enhanced Deprecation

  Deprecated method에서 어노테이션이나 메세징 기능을 지원한다.

  - G1 Default Garbage Collector

  GC의 디폴트가 G1이라는 GC로 변경되었다. JVM상 GC 발생시 모든 스레드가 올 스톱되기 때문에 GC 줄이는 것이 큰 이슈였음.

  - Batter String Performance

  String 관련 성능 이슈, 퍼포먼스 상승 Compact strings 타입 추가

  ​

- ### Smaller Featrues

  - Unified VM logging(JEP 158)

  JVM상에서 logging이 가능하다.

  The logging framework defines a set of *tags* in the JVM






- ### ETC

  - JDK/JRE File Structure 변경

  JDK8, JDK9의 구조가 모듈 때문에 달라진다. 


SHA-1 certificates Disable

Deprecate the Applete APi





Major Featrue

- Big new featrue is modularity

- Interactive developer fetatures(REPL/jshell)

  ​

가장 큰 변화는 modularity, REPL/jshell 

GC 변경





------

## HTTP/2

------

2015 / 5 RFC 7240 스펙 공표



### 특징

- Binary protocol



### 데이터 전송 단위



Keep-Alive 관련 헤더 무시



### 헤더

- Pseudo-header 필드



h2 : HTTP/2 (with TLS)

h2c : HTTP/2 (without TLS) 지원 브라우저 x





HTTP/2로 가는 이유

더 나은 성능

- 헤더 압축
- Multiplexing
- Server Push



Java의 HTTP/2 지원

Servlet 4.0 : Server push

Tomcat 9 , Jetty 10에서 지원



Java9

HttpClient(HTTP2 지원 클라이언트) 지원

ALPN 지원 Java 서버에서 TLS offloading을 할 때 사용한다.

Java7,8에서 ALPN - jetty-alpn-agent



이상적인 서버 구성

레거시

1.1 / L4 Witch Fornt web server Nginx / Apache Backend Tomcat or Jetty



브라우저에서 h2 / Front / Back에서는 h2c로 사용

[h2 demo](https://github.com/benelog/h2demo)



Nginx

Application Server Server push 제약



Java 9 7-8 보다 고려할 점이 많아짐

Nginx Server push를 못씀

- 안정된 구성

Server push 포기

Nginx(http/2) 구성 - http/1.1 - tomcat



어차피 Servewr pusg를 못쓰니까

h2를 위해 Tomcat 9 , Spring 5 올릴 필요가 없다.



HTTP/2 시대에 대비

HTTPS 전환 / Nginx 전환

앞단은 h2, 벡단에는 Http/1.1 적용하는게 현재로선 안정적





Serverpush를 하면 브라우저가 dom 파싱 할 필요없이 큐잉해서 이해하고 jpg 파일등을 미리 내어줄 수 있다.



현실적 적용

Server push 없이 구성하기

- 앞단 Nginx

h2가 persistent connection이기 때문에 Nginx 유리

Backend 서버는 Http 1.1 동일하게 한다. 그냥



Server push를 지원하는 서버 구성

HaProxy -> Tomcat 서버 바로 연결한다.

Nginx Apache를 구성하면 안됨



L 4 Switch -> Tomcat 바로 연결

TLs offloading Forked Tomcat native로





Server push의 최적 활용

무작정 쓰는것이 이득이 아님



HTTP/2 지원 여부까지 포함하면 경우의 수가 엄청 많아짐

Server push를 안 쓰고 앞단만 HTTP/2로 구성하거나

Server push를 쓰려면 다양한 시도가 필요할 것.





바이너리 웹 세대



이제 프로토콜도 binary, javascript도 컴파일해서 binary 실행 코드로 실행

웹브라우저에서 아예 binary 프로토콜, 바이너리 실행 코드가 되는 어플리케이션 실행 환경에서의 웹브라우저가 됨

바이너리로 통신 / 실행하는 바이너리 웹 이 될 것 같음



브라우저위에서 바이너리 RPC 프로토콜 실행할 수 있는 여지가 커짐



REST스러운 방식은, 인간이 보기엔 효율적이지만, 기계 입장에서는 효율적이지 않다.

앞으론 h2의 바이너리 특징을 활용할 수 있는 방법으로 응용될 여지가 커질 것



Appendix























------

## JSON-B JSON-P / JPA 2.2

------

### JPA 2.2

- @Repeatable annotation

namedQueryes 부분 반복 annotation 제거



Stream result



메모리 적재 관련 최적화 활용

- CDI Injection

CDI가 enabled라면 AttributeConverter에 Context 주입 가능





### JSON-B / JSON-P

jackson-databind -> JSON-B

Jackson-core -> JSON-P



JSON Binding



JSON Processing



JSON Patch

- The original document

  ```
  {
      "bibidi": "babidi",
      "foo": "bar"
  }
  ```

- The patch

  ```
  [
      { "op": "add", "path": "/hello", "value": ["majin"] },
      { "op": "replace", "path": "/bibidi", "value": "buu" },
      { "op": "remove", "path": "/foo" }
  ]
  ```

- The result

  ```
  {
      "bibidi": "buu",
      "hello": ["majin"]
  }
  ```

JSON 부분 변경이 가능하다.

아래와 같은 인터페이스 제공

```< T extends JsonStructure > T apply(T target);```









## Spring 5 new feature

