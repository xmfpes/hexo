---
title: Servlet 개발환경으로 웹 서버 구현해보기
date: 2017-10-23 22:20:03
tags: 
- Servlet
- Tomcat
categories:
- Java
---

## Servlet 개발환경으로 웹 서버 구현해보기

![java](https://www.3pillarglobal.com/wp-content/uploads/2016/03/java8_600x600-300x300.png)

### 1. Embedded 톰캣 설치

[다운로드 링크](https://tomcat.apache.org/download-90.cgi)

Embedded Tomcat의

tar.gz 버전을 다운로드 하고 압축을 푼다.

### STEP 1. 자바 프로젝트 생성 및 설정

자바 프로젝트를 생성하고 제일 외부에 lib 폴더를 추가한다.

이후 Embedded Tomcat 하부의 jar 파일을 전부 lib에 복사한다.

이후에 프로젝트 오른쪽 클릭 후 Properties -> Java Build Path -> Libraries

현재 프로젝트 내부에 jar 파일이 있으므로 add jar를 이용해 lib안의 모든 jar를 추가한다.

### STEP 2. 웹 서버를 실행할 클래스 파일 생성

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


## 2. Servlet 설정 추가를 통해 Hello World 출력해보기
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


### STEP 3. Servlet을 이용해 클라이언트에서 서버로 데이터 전달해보기

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