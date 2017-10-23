---
title: HTTP Protocol
date: 2017-10-23 18:06:08
tags: 
- HTTP
categories:
- Network
---
## HTTP Protocol

HTTP (Hypertext Transfer Protocol)는 인터넷 (또는 The Web)에서 가장 많이 사용되는 응용 프로그램 프로토콜.

HTTP는 Stateless Protocol이다. 즉, 현재 요청은 이전 요청에서 수행 된 작업을 알지 못한다.

### HTTP의 동작 순서

![http](/images/network/http.png)

- 클라이언트 연결
- 클라이언트 측 브라우저에서 웹 리소스를 얻기 위해 브라우저에서 URL 발급
- HTTP Request
- 서버에서 Request 메세지를 해석하고 사용자에게 요청한 자원 오류 메세지 등의 적절한 Http Response 반환

### URL(Uniform Resource Locator)

URL (Uniform Resource Locator)은 웹을 통해 리소스를 고유하게 식별하는 데 사용됩니다. URL의 구문은 다음과 같습니다.

```
protocol : // hostname : port / path-and-file-name
```
- 프로토콜 : HTTP, FTP 및 텔넷과 같이 클라이언트 및 서버에서 사용하는 응용 프로그램 수준 프로토콜입니다.
- 호스트 이름 : 서버의 DNS 도메인 이름 (예 www.nowhere123.com:) 또는 IP 주소 (예 : 192.128.1.2).
- 포트 : 클라이언트가 들어오는 요청을 서버가 수신 대기중인 TCP 포트 번호입니다.
- 경로 및 파일 이름 : 서버 문서 기본 디렉토리 아래에 요청 된 자원의 이름 및 위치.

### HTTP 메세지의 구조

### Request Message

```
GET /docs/index.html HTTP/1.1
Host: www.nowhere123.com
Accept: image/gif, image/jpeg, */*
Accept-Language: en-us
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
(blank line)
```

### Response Message
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

### 1. 메세지 헤더

리퀘스트

### 2. 개행 문자(CR+LF)

### 3. 메세지 바디


![java](/images/network/http-header-functions.jpg)
