---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 19(헤더 메뉴 추가)
date: 2017-10-10 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
categories: Node.js
---

## **헤더 메뉴 바 추가하기**

![node](/images/node.png)

기존에 페이지별로 나눠진 기능을, 헤더에 메뉴바를 추가해 관리한다.

뷰에 글로벌로 로그인 정보 관련 변수를 추가하고, 뷰에 nav 코드를 작성한다.

## STEP 1. app.js 글로벌 변수 셋팅하기

/app.js

```javascript

//.......flash 설정
//로그인 정보 뷰에서만 변수로 셋팅, 전체 미들웨어는 router위에 두어야 에러가 안난다
app.use(function(req, res, next) {
  app.locals.isLogin = req.isAuthenticated();
  //app.locals.urlparameter = req.url; //현재 url 정보를 보내고 싶으면 이와같이 셋팅
  //app.locals.userData = req.user; //사용 정보를 보내고 싶으면 이와같이 셋팅
  next();
});
```

애플리케이션 레벨 미들웨어를 설정해서, app.locals.isLogin = req.isAuthenticated(); 구문을 통해

로컬 변수인 isLogin에 로그인 여부를 담는다.

## STEP 2. view에 헤더 메뉴 적용하기

비로그인 시 - 로그인 / 회원가입 메뉴

로그인 시 - 로그아웃

메뉴가 헤더에 노출되도록 구성되어야 한다.

상단 head에 JQuery CDN과 bootstrap.js를 추가한다.

```html
<script
    src="https://code.jquery.com/jquery-3.2.1.min.js"
    integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
    crossorigin="anonymous"></script>
<!--bootstrap js 추가 -->
<script type="text/javascript" src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
```

nav를 추가해 헤더 메뉴를 추가한다.

```html
<body>
    <div class="container" style="padding-top: 10px;">
        <nav class="navbar navbar-inverse">
            <div class="container-fluid">
                <div class="navbar-header">
                    <!-- 오른쪽 메뉴바 -->
                    <button type="button" class="collapsed navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-9" aria-expanded="false">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a href="/" class="navbar-brand">Nodejs</a>
                </div>
                <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-9">
                    <ul class="nav navbar-nav">
                        <li class="active">
                            <a href="/">Home</a>
                        </li>
                        <li><a href="/admin/products">ADMIN</a></li>
                        <% if(isLogin){%>
                            <li><a href="/accounts/logout" onclick="return confirm('로그아웃 하시겠습니까?')">LOGOUT</a></li>
                        <%}else{%>
                            <li><a href="/accounts/join">JOIN</a></li>
                            <li><a href="/accounts/login">LOGIN</a></li>  
                        <%}%>
                    </ul>
                </div>
            </div>
        </nav>

```

## STEP 3. 로그인 이후 리다이렉팅 설정하기

일반 로그인 후 리다이렉팅 설정

routes/acconts.js

POST /accounts/login

```javascript
router.post('/login' ,
    passport.authenticate('local', {
        failureRedirect: '/accounts/login',
        failureFlash: true
    }),
    function(req, res){
        res.send('<script>alert("로그인 성공");location.href="/";</script>');
    }
);
```

페이스북 로그인 후 리다이렉팅 설정

routes/auth.js

GET /auth/facebook/callback

```javascript
//인증후 페이스북에서 이 주소로 리턴해줌. 상단에 적은 callbackURL과 일치
router.get('/facebook/callback',
    passport.authenticate('facebook',
        {
            successRedirect: '/',
            failureRedirect: '/auth/facebook/fail'
        }
    )
);
```

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/9a47ae8c96472ee641c656b17332826f104b0c70)에 커밋 로그로 남겨두었습니다.
