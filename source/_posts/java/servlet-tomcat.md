---
title: Servlet 개발환경으로 웹 서버 구현해보기
date: 2017-10-23 22:20:03
tags: 
- Servlet
- Tomcat
categories:
- Java
- Servlet
---


## Servlet이란?

HTML을 활용해 웹 페이지를 개발하면, 정적인 웹 페이지만을 제공할 수 밖에 없는데,
이런 HTML의 한계를 극복하고, 사용자의 상태나 데이터를 동적으로 제공하는 웹페이지를 위해 사용하는 서버측 프로그램 혹은 그 사양중의 하나가 Java의 Servlet입니다.

 서블릿은 JSP와 비슷한 점이 있지만, JSP가 HTML 문서 안에 Java 코드를 포함하고 있는 반면, 서블릿은 자바 코드 안에 HTML을 포함하고 있다는 차이점이 있다.


## Tomcat

Servlet을 이용해 프로그램을 구현하고, 사용자 요청을 받아 Path를 추출하고 그에 해당하는 Servlet을 실행하고 작업이 끝난 뒤 응답을 하면 응답을 받아 클라이언트에 해당하는 브라우저에 데이터를 전송해주는 역할을 한다.

이런 Tomcat 등을 WAS라고 하며 또는, Servlet Container라고도 한다.



## Servlet과 Tomcat의 요청 처리 과정

Tomcat(Servlet Container)는 시작할 때 해당 프로젝트 내의 Servlet을 인스턴스화 해서 가지고 있는다.



- Client에서 HTTP Request 
- Tomcat(Servlet Container)가 요청이 들어오면 HttpServletRequest, HttpServletResponse 객체를 생성한다.
- Client의 요청을 분석해서, 어떤 Servlet에 대한 요청인지 판별하고 Servlet 메소드에 두 객체를 전달한다. Servlet은 Tomcat의 특정 스레드 하나에 Servlet이 배정이 되어서 실행이 되게 된다.
- Client 요청에 대한 처리작업이 끝나면 Servlet에서 Response를 보낸다.
- Tomcat은 Reponse를 받아 Client로 보내준다.
- 지금까지 사용했던 스레드가 해제되고, 생성한 HttpServletRequest, HttpServletResponse가 소멸된다.




이 때 사용한 Servlet은 사라지지 않는다. Tomcat이 시작되면서 만들어 진 다음에 Tomcat(Servlet Container)이 종료되기 전까지 계속 사용된다.



## Tomcat(Servlet Container)와 스레드



서블릿 컨테이너(대부분의 웹 서버/웹 애플리케이션 서버)는 서버가 생성할 수 있는 Thread 수를 제한한다. Thread Pool을 사용해 제한된 Thread를 재사용하는 방식으로 동작한다.

Tomcat의 Thread 수 설정(TOMCAT_HOME/conf/server.xml)

```
<Connector port="8080" protocol="HTTP/1.1" 
             connectionTimeout="10000" 
             redirectPort="8443"  
             maxThreads="400"   
             acceptCount ="150" />
```

maxThreads - Connector에 의해 처리할 수 있는 최대 요청 수. 기본 값은 200

acceptCount - Maximum queue length. 기본 값은 100



- - -



## Servlet과 Tomcat으로 웹 서버 구축해보기

![](../../images/java/java.png)



### Embedded 톰캣 설치

[다운로드 링크](https://tomcat.apache.org/download-90.cgi)

Embedded Tomcat의

tar.gz 버전을 다운로드 하고 압축을 푼다.



### 자바 프로젝트 생성 및 설정

자바 프로젝트를 생성하고 제일 외부에 lib 폴더를 추가한다.

이후 Embedded Tomcat 하부의 jar 파일을 전부 lib에 복사한다.

이후에 프로젝트 오른쪽 클릭 후 Properties -> Java Build Path -> Libraries

현재 프로젝트 내부에 jar 파일이 있으므로 add jar를 이용해 lib안의 모든 jar를 추가한다.



### 웹 서버를 실행할 클래스 파일 생성

src 하부에 com.kyunam 등 패키지를 하나 생성하고 WebServerLauncher 클래스 파일을 생성하고 아래의 코드를 작성한다.

```java
public class WebServerLauncher {
	public static void main(String[] args) throws Exception {
        String webappDirLocation = "webapp/";
        Tomcat tomcat = new Tomcat();
        String webPort = System.getenv("PORT");
        if(webPort == null || webPort.isEmpty()) {
            webPort = "8080";
        }

        tomcat.setPort(Integer.valueOf(webPort));
        Connector connector = tomcat.getConnector();
        connector.setURIEncoding("UTF-8");
        tomcat.addWebapp("/", new File(webappDirLocation).getAbsolutePath());
        System.out.println("configuring app with basedir: " + new File("./" + webappDirLocation).getAbsolutePath());
        tomcat.start();
        tomcat.getServer().await();
    }
}

```

제일 처음에, 디폴트로 사용할 경로를 설정해주었는데 webapp의 폴더 경로가 없으므로

최상단에 webapp 폴더를 생성하고 메인 페이지를 설정하기 위해 index.html 파일을 생성한다.

완성된 디렉토리 구조는 아래와 같다.

![](/images/java/tomcat.png)

 WebServerLauncher를 실행하고 [localhost:8080](localhost:8080)으로 접속하면 index.html이 출력된다.




## Servlet 설정 추가를 통해 Hello World 출력해보기
이전에 제작한 com.kyunam 패키지에 HelloWord를 출력하기 위한 HelloWorldServlet 클래스 파일 생성

HelloWorldServlet는 HttpServlet을 상속하는 구조이다.

get요청에 대해 응답하기 위해 doGet을 오버라이딩 해보자.

hello로 오는 요청에 대해서 Reponse의 PrintWriter를 받아 Hello World를 출력해 응답하는 코드이다.

```java
@WebServlet("/hello")
public class HelloWorldServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		PrintWriter out = resp.getWriter();
		out.print("Hello World");
	}
}

```

위의 코드는 문제가 없지만, Embedded Tomcat 서버가 자바 클래스를 인식하는 디폴트 디렉토리가 있기 때문에, 찾지 못해서 작동하지 않는다.

표준 디렉토리는 Root(/webapp) -> WEB-INF -> classes 아래에 위치해야 디폴트로 인식하게 된다.

하지만 지금 구현된 HelloWorldServlet은 컴파일 되어서 bin/net/kyunam에 생성되게 된다. 이 경로를 위의 표준 디렉토리로 바꾸어 주어야 정상적으로 요청에 대해 응답을 할 수 있다.

### 이를 위해 클래스 파일이 위치하는 Output 디렉토리를 바꾸어 주어야 한다

Project 오른쪽 클릭 -> Properties -> Java Build Path -> source로 들어가서

![output](/images/java/servlet.png)

위의 Defualt output folder를 변경해준다.

java-servlet/webapp/WEB-INF/classes로 변경하고 서버를 재시작하고

localhost:8080/hello로 요청을 보내면

기존에 작성했던 doGet에 대한 코드가 실행되게 된다.




### Servlet을 이용해 클라이언트에서 서버로 데이터 전달해보기

localhost:8080/hello?name=kyunam 이런 Get 요청을 이용해서 서버에 데이터를 전달할 수 있다.

이를 Servlet에서 어떻게 받을 수 있을까.

파라미터를 받아서, 받은 파라미터 이름을 응답해 출력해보자.

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	// TODO Auto-generated method stub
	String value = req.getParameter("name");

	PrintWriter out = resp.getWriter();
	out.print(value + "Hello World");
}
```

위의 코드를 이용하면 name 파라미터를 받아서 출력해 응답으로 보내 줄 수 있다.



## HTML 형태로 데이터 클라이언트에게 데이터 보내기

```java
@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		String value = req.getParameter("name");
		PrintWriter out = resp.getWriter();
		resp.setContentType("text/html");
        out.println("<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.0 "
                + "Transitional//EN\">" 
                + "<html>"
                + "<head><title>Hello WWW</title></head>" + "<body>"
                + "<h1>Hello 1234</h1>" 
                + "<h1>" + value + "</h1"
                + "</body></html>");
	}
```

위와 같은 코드를 이용하면, HTML 형태로 클라이언트에 메세지를 보낼 수 있다. 하지만 현재는 정적인 HTML 코드 형태만 보낼 수 있다.



이런 한계점을 극복하기 위해서 JSP를 이용해 동적인 HTML을 생성해 주어야 한다.

최근에는 JSP 대신에 handlebars, thymeleaf등의 template engine을 사용한다.



### JSP의 문제점



jsp 페이지 내에 스크립트릿이 들어가면 자바 코드가 직접 들어가 html과 혼재되어 혼란을 주게 된다. 이 때문에 JSTL(JSP Standard Tag Library)와 EL(Expression Language)이 등장했고, 앞으로는 JSTL과 EL을 활용해 JSP에서 자바 코드를 사용하지 않도록 변경할 것이다.



## JSTL 사용 설정

- <http://central.maven.org/maven2/javax/servlet/jstl/1.2/jstl-1.2.jar> 에서 jstl을 다운로드 한다.
- 다운로드한 jar 파일을 “프로젝트 디렉토리/webapp/WEB-INF/lib” 디렉토리에 복사한다.
- WEB-INF/lib 디렉토리에 jar 파일을 복사하면 WAS가 시작할 때 자동으로 클래스패스에 추가된다.





## 자바빈(Java bean)

- Expression Language에서 객체의 값을 사용할 때 객체의 필드(전역변수)에 접근하는 것이 아니라 getter 메서드를 호출한다.

- 예를 들어 앞의 ${user.name}은 User 객체의 getName() 메서드를 호출한다.

- 자바 빈 규약에서 getter 메서드의 이름은 “get + 첫글자대문자 + 두번째글자 이후”로 생성한다.

- 자바 빈 규약에 익숙하지 않으면 통합 개발 도구의 setter/getter 메서드 자동 생성 기능 활용한다.

  ​

## Servlet/JSP 사이의 이동

- forward 방식
- redirect 방식