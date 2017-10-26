---
title: HTTP Protocol
date: 2017-10-23 18:06:08
tags: 
- HTTP
categories:
- Network
---

# HTTP Protocol

HTTP (Hypertext Transfer Protocol)는 인터넷 (또는 The Web)에서 가장 많이 사용되는 응용 프로그램 프로토콜.

HTTP는 Stateless Protocol이다. 즉, 현재 요청은 이전 요청에서 수행 된 작업을 알지 못한다.

> ### HTTP의 동작 순서

- 클라이언트 연결
- 클라이언트 측 브라우저에서 웹 리소스를 얻기 위해 브라우저에서 URL 발급
- HTTP Request
- 서버에서 Request 메세지를 해석하고 사용자에게 요청한 자원 오류 메세지 등의 적절한 Http Response 반환

> ### HTTP Method 종류

![](/images/network/http-header-functions.jpg)

> ### URL(Uniform Resource Locator)

URL (Uniform Resource Locator)은 웹을 통해 리소스를 고유하게 식별하는 데 사용됩니다. URL의 구문은 다음과 같습니다.

```
protocol : // hostname : port / path-and-file-name
```
- 프로토콜 : HTTP, FTP 및 텔넷과 같이 클라이언트 및 서버에서 사용하는 응용 프로그램 수준 프로토콜입니다.
- 호스트 이름 : 서버의 DNS 도메인 이름 (예 www.nowhere123.com:) 또는 IP 주소 (예 : 192.128.1.2).
- 포트 : 클라이언트가 들어오는 요청을 서버가 수신 대기중인 TCP 포트 번호입니다.
- 경로 및 파일 이름 : 서버 문서 기본 디렉토리 아래에 요청 된 자원의 이름 및 위치.

- - -

>### HTTP Request 구조

![](/images/network/request.png)

### 1. Request Message Header

- ### Request Line

```
GET /docs/index.html HTTP/1.1
```

요청 메세지의 첫 줄은 Request Line이라고 하며
요청 메소드 / 경로 / HTTP 버전으로 구성된다.

- ### Request Header

```
Host: www.nowhere123.com
Accept: image/gif, image/jpeg, */*
Accept-Language: en-us
Accept-Encoding: gzip, deflate
```

그 아랫줄 부터는 Request Header로 구성되며
Request Line과 Request Header를 통틀어
Request Message Header라고 부른다.
키, 값 형태로 되어있으며 값은 여러 값을 쉼표로 구분하여 받을 수 있다.

### 2. 개행 문자(CR+LF)

바디와 헤더 구분을 위한 값

### 3. Request Message body

값을 가진 Message Body 값

Request example

```
GET /docs/index.html HTTP/1.1
Host: www.nowhere123.com
Accept: image/gif, image/jpeg, */*
Accept-Language: en-us
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
(blank line)
```

- - -

> ### HTTP Response 구조

![](/images/network/response.png)

### 1. Response Message Header

- ### Status Line

```
HTTP/1.1 200 OK
```

HTTP 버전 / Status Code / 상태 코드 설명의 구조로 이루어져 있다.

- ### Response Header

```
Content-Type: text/html
Content-Length: 35
Connection: Keep-Alive
Keep-Alive: timeout=15, max=100
```
Reponse 헤더 이름 / 값 으로 구성되어 있으며 쉼표 구분자를 통해 여러 값을 받을 수 있습니다.  

### 2. 개행 문자(CR+LF)

헤더와 바디를 구분하기 위한 문자

### 3. Response Message Body

응답 값을 가진 Message Body

```
HTTP/1.1 200 OK
Date: Sun, 18 Oct 2009 08:56:53 GMT
Server: Apache/2.2.14 (Win32)
Last-Modified: Sat, 20 Nov 2004 07:16:26 GMT
ETag: "10000000565a5-2c-3e94b66c2e680"
Accept-Ranges: bytes
Content-Length: 44
Connection: close
Content-Type: text/html
X-Pad: avoid browser bug

<html><body><h1>It works!</h1></body></html>
```

- - -

> ## 쿠키와 세션

HTTP 프로토콜은 기본적으로 무 상태 프로토콜이며, 따라서 정보 등을 저장할 수 없기에 이를 극복하기 위해 세션과 쿠키를 사용한다.

> ### 쿠키

쿠키는 클라이언트에 저장되는 값

이름 값, 만료 정보 등이 들어있다.

이 쿠키 정보를 이용해 로그인 정보를 유지할 수 있다.

서버에서 Response에 Set-Cookie 헤더로 쿠키를 설정하고 쿠키를 브라우저 측에 저장한다.
```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: logined=true; Path=/
```

위와 같이 서버측의 응답을 통해 브라우저에 logined=true라는 쿠키 값을 넘겨 저장하게 할 수 있다.

> ### 세션

세션은 쿠키와 이름은 다르지만, 세션도 쿠키를 기반으로 동작하게 된다.

브라우저에 저장되는 쿠키와 달리 서버에 저장되게 된다.

내가 이해한 개념

서버에서 세션을 생성하고 쿠키로 세션 아이디를 보낸다.

브라우저에서는 세션 아이디 값을 쿠키 형태로 저장하며

서버에서는 브라우저의 쿠키의 세션 아이디 값을 이용해 세션이 연결됐는지 여부를 판단한다.


> ### 세션 심화 내용
#### 여러대의 서버에서 로드밸런싱과 세션

1대의 서버가 아닌 10대의 서버가 돌아가는 환경에서, 1번째 톰캣 서버가 세션을 생성하고 클라이언트가 2번째 톰캣 서버에 접속한다면 어떻게 될까?

서버에 해당 세션이 없으므로 로그인 처리가 제대로 인식되지 않을 것이다.

이때 이 문제를 해결하기 위한 방법이 3가지가 있다.

- ### 1. Sticky Session

만약 한대의 웹 서버를 따로 두고, 거기서 여러 Tomcat에 요청을 보내는 경우 처음에 접속했던 서버에 계속 접속하게 만드는 방법이다.

매번 같은 서버로 접속하기 때문에 저장된 세션이 남아있게 된다.


- ### 2. Global Session(DB 이용)

여러 톰캣에서 하나의 데이터베이스를 이용해서 캐시 서버를 두고 그 데이터베이스의 값을 이용해 세션을 관리하도록 하게 하는 방법이다.

이는 RDBS를 쓸 필요는 없고 이런 작업에 특화된 데이터베이스인(Redis, Memchached) 등을 사용한다.

- ### 3. Session clustering

여러 톰캣이 존재하는 경우 여러 톰캣의 세션 정보를 다른 톰캣들이 공유해 하나로 관리하도록 하게 하는 방법이다.

톰캣 내에 세션 클러스터링을 설정하는 방법이 따로 있다고 하는데, 이는 나중에 알아보자.

- - -

> ### HTTP 요청에 대한 최적의 처리를 위한 방법

다양한 방법이 있을 수 있다.

- ### HTTP caching

Cache-Control 헤더를 이용해 캐싱을 할 수 있다.

정적인 js, image, css 등의 파일의 경우는 바뀌는 경우가 잘 없기 때문에 Cache-Control 헤더를 설정하고 Response를 해 주어서 브라우저 상에 저장하도록 하는 캐싱 기능을 활용할 수 있다.

이를 구현하기 위해서 경로별 응답 처리를 따로 해, 정적 파일 경로의 경우 Cache-Control 헤더를 설정해 Response를 하는식으로 처리를 할 수 있습니다. 아래처럼 Response 헤더에 캐시 컨트롤을 추가하고, 캐싱 유효 시간을 설정할 수 있습니다.
```
Cache-Control: max-age=31536000
```
- ### HTTP compression

압축을 이용해서도 부하를 줄일 수 있다.
압축 비용이 추가되겠지만, 네트워크 통신 비용보다는 압축 비용이 싸기 때문에 압축을 이용해서도 성능 향상을 할 수 있다.
HTTP Response에 Content-Encoding 헤더를 통해 압축 방식을 설정할 수 있다.

클라이언트 측에서는 리퀘스트 헤더로 가능한 압축 방법 목록을 알릴 수 있습니다.

```
Request Header
accept:*/*
accept-encoding:gzip, deflate, br
```

서버 측에서는 사용한 압축 방법을 Response의 Content-Encoding 헤더에 담아 알려줍니다.
```
Response Header
Content-Encoding: gzip
```

- - -

> ### TCP/IP

### 3 Way HandShaking

Client와 Server간의 연결에서 연결이 수립되는 방식

### HTTP에서의 3 Way HandShaking

![](/images/network/3wayhttp.png)

### HTTPS에서의 3 Way HandShaking

![](/images/network/3wayhttps.png)


### 기존에 구현한 HTTP 웹 서버의 문제점

- ### 스레드가 계속 생성되어 메모리 문제 발생 해결책은?

스레드 풀을 만들어, 거기서 스레드를 가지고 와서 사용 할 수 있도록 한다. 디폴트 스레드는 200개로 설정되어 있다. 갯수를 더 늘릴 수 있지만, 컨텍스트 스위칭 때문에 부하가 커져서 비효율적이다.

풀이 다 찼을 경우에는, 큐의 형태로 요청 사용자들을 넣어두고 순차적으로 처리한다.
