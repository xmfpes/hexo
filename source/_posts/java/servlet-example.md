---
title: Servlet과 JSP 실습
date: 2017-10-23 22:20:03
tags: 
- Servlet
- Tomcat
categories:
- Java
- Servlet
---

## Servlet 예제

![](../../images/java/java.png)

- ### Servlet을 이용한 개인정보 수정 기능 구현

기존 /user/list에서 수정 버튼이 연결될 링크를 수정한다. userId 값을 이용해 해당 유저를 찾기 위해 userId값을 파라미터로 넘겨준다.

```Html
<c:forEach items="${users}" var="user" varStatus="status">
  <tr>
    <th scope="row">${status.count}</th>
    <td>${user.userId}</td>
    <td>${user.name}</td>
    <td>${user.email}</td>
    <td><a href="/user/update?userId=${user.userId}" class="btn btn-success" role="button">수정</a>
    </td>
  </tr>
</c:forEach>
```

받은 요청에 대해서 처리할 Servlet을 만든다.

UpdateUserFromServlet.java

Get 요청에 대한 처리에서 req에 있는 userId를 받아서 DataBase에서 조회하고 req에 user 객체를 담아서 유저 정보를 수정할 뷰 파일인 /user/update.jsp로 보낸다. 

수정에 대한 요청을 받을 post에서는 req의 파라미터를 받아 유저를 찾고, 그 유저 정보를 폼에 입력된 정보로 업데이트 한 뒤 유저 리스트로 리다이렉트 한다.

```java
@WebServlet("/user/update")
public class UpdateUserFromServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		req.setAttribute("user", DataBase.findUserById(req.getParameter("userId")));
		RequestDispatcher rb = req.getRequestDispatcher("/user/update.jsp");
		rb.forward(req, resp);
	}
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		String userId= req.getParameter("userId");
		User user = DataBase.findUserById(userId);
		User updateUser = new User(req.getParameter("userId"), req.getParameter("password"), req.getParameter("name"),
                req.getParameter("email"));
		user.updateUser(updateUser);
		resp.sendRedirect("/user/list");
	}
}
```



- ### Servlet을 이용한 로그인과 로그아웃 처리 구현

로그인과 로그아웃 처리에 대한 Servlet을 구현하는 코드 작성, 로그인이 성공한다면 세션에 값을 저장하고, 로그아웃 할 시 세션의 값을 삭제하는 로직이 구현되어 있다.

LoginSessionServlet.java

```java
@WebServlet("/user/login")
public class LoginSessionServlet extends HttpServlet{
	private static final long serialVersionUID = 1L;
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		User user = DataBase.findUserById(req.getParameter("userId"));
		if(user.loginCheck(req.getParameter("password"))) {
			HttpSession session = req.getSession();
			session.setAttribute("user", user);
			resp.sendRedirect("/index.jsp");
		}else {
			resp.sendRedirect("/user/login_failed.jsp");
		}
		
	}
}
```

LogoutSessionServlet.java

```java
@WebServlet("/user/logout")
public class LogoutSessionServlet extends HttpServlet{
	private static final long serialVersionUID = 1L;
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		HttpSession session = req.getSession();
		session.removeAttribute("user");
		resp.sendRedirect("/index.jsp");
	}
}
```

그리고 로그인 여부에 따라 헤더에서 로그인 상태면 로그아웃 버튼을 보이게 처리하고, 로그인이 안된 상태면 로그인과 회원가입 버튼이 보이도록 처리해주어야 한다.

이는 JSP에서 처리해야 하는데 공통되는 메뉴 부분을 header.jsp를 만들고 로그인 시 로그아웃과 개인정보 수정, 비 로그인 상태에서는 로그인과 회원가입이 보이도록 처리해주었다.

header.jsp

```html
<div class="collapse navbar-collapse" id="navbar-collapse2">
  <ul class="nav navbar-nav navbar-right">
    <li class="active"><a href="../index.jsp">Posts</a></li>
    <c:choose>
      <c:when test="${not empty sessionScope.user}">
        <li><a href="/user/logout" role="button">로그아웃</a></li>
        <li><a href="#" role="button">개인정보수정</a></li>
      </c:when>
      <c:otherwise>
        <li><a href="../user/login.jsp" role="button">로그인</a></li>
        <li><a href="../user/form.jsp" role="button">회원가입</a></li>
      </c:otherwise>
    </c:choose>
  </ul>
</div>
```

그리고 각 jsp 파일마다 메뉴를 공통 파일로 include로 처리해주었다.

```html
<body>
<%@include file="/includes/header.jsp" %>
<div class="container" id="main">
```

- ### 사용자 목록 기능에 로그인 필터 기능 추가하기


로그인이 안된 사용자가 사용자 목록을 보려고 하면 로그인 페이지로 넘겨주도록 구현이 필요하다.

세션 정보를 통해 로그인 등을 관리하는 클래스를 생성하고, 이를 이용해 로그인 여부를 체크해서 비 로그인 상태라면 다른 페이지로 redirection 시키도록 구현한다.

HttpSessionManager.java

```Java
public class HttpSessionManager {
	public User getUserInSession(HttpSession session) {
		User user = (User) session.getAttribute("user");
		return user == null ? null : user;	
	}
	public boolean isLogin(HttpSession session) {
		return session.getAttribute("user") == null ? false : true;
	}
}
```

ListUserServlet.java

```java
@WebServlet("/user/list")
public class ListUserServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setAttribute("users", DataBase.findAll());
        HttpSession session = req.getSession();
        
        HttpSessionManager manager = new HttpSessionManager();
        RequestDispatcher rd = null;
        if(manager.isLogin(session)) {
        	 	rd = req.getRequestDispatcher("/user/list.jsp");
        }else {
        		rd = req.getRequestDispatcher("/user/login.jsp");
        } 
        rd.forward(req, resp);
    }
}
```






