---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 17(로그인 기능 추가)
date: 2017-09-22 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
- passport
categories: 
- JavaScript
- Node.js
---

## **로그인 기능 추가하기**

![node](/images/node.png)

passport 모듈을 이용해서 로그인 기능을 추가한다.

## STEP 1. 모듈 설치 및 적용

express-session

passport

passport-local

connect-flash

네개의 모듈을 설치한다.

```bash
$ npm install --save express-session passport passport-local connect-flash
```

/app.js에서 모듈을 적용한다.

cookieParser 아래 부분에 정의한다.

```javascript
//var cookieParser = require('cookie-parser');
//-------------아래 적용----------------------

//flash  메시지 관련
var flash = require('connect-flash');

//passport 로그인 관련
var passport = require('passport');
var session = require('express-session');
```
## STEP 2. 세션 적용하기

```javascript
//업로드 path 추가 코드
//app.use('/uploads', express.static('uploads'));
//..uploads path 패스 추가 코드 아래에 세션 설정 추가하기

//session 관련 셋팅
app.use(session({
    secret: 'fastcampus',
    resave: false,
    saveUninitialized: true,
    cookie: {
      maxAge: 2000 * 60 * 60 //지속시간 2시간
    }
}));

//passport 적용
app.use(passport.initialize());
app.use(passport.session());

//플래시 메시지 관련
app.use(flash());
```

## STEP 3. routes/에서 passport 설정 추가하기

/routes/accounts.js

Passport 정책 생성하기

상단에 모듈 추가하는 코드 추가

```javascript
var passport = require('passport');
var LocalStrategy = require('passport-local').Strategy;
```

바로 밑 하단부에 passport 정책 선언하기
```javascript
passport.serializeUser(function (user, done) {
    console.log('serializeUser');
    done(null, user);
});

passport.deserializeUser(function (user, done) {
    var result = user;
    result.password = "";
    console.log('deserializeUser');
    done(null, result);
});

passport.use(new LocalStrategy({
        usernameField: 'username',
        passwordField : 'password',
        passReqToCallback : true
    },
    function (req, username, password, done) {
        UserModel.findOne({ username : username , password : passwordHash(password) }, function (err,user) {
            if (!user){
                return done(null, false, { message: '아이디 또는 비밀번호 오류 입니다.' });
            }else{
                return done(null, user );
            }
        });
    }
));
```

라우팅 코드 추가하기

GET /accounts/login 로그인뷰

POST /accounts/login 로그인액션

GET /accounts/success 성공시 이동

GET /accounts/logout 로그아웃

```javascript
router.get('/login', function(req, res){
    res.render('accounts/login', { flashMessage : req.flash().error });
});

router.post('/login' ,
    passport.authenticate('local', {
        failureRedirect: '/accounts/login',
        failureFlash: true
    }),
    function(req, res){
        res.send('<script>alert("로그인 성공");location.href="/accounts/success";</script>');
    }
);

router.get('/success', function(req, res){
    res.send(req.user);
});


router.get('/logout', function(req, res){
    req.logout();
    res.redirect('/accounts/login');
});
```

## STEP 4. 뷰에서 로그인 실패시 출력할 플래시 메세지 적용

/views/login.ejs

```html
<%if(typeof flashMessage !=='undefined') {%>
    <div class="alert alert-danger" role="alert"><%=flashMessage%></div>
<%}%>
```

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/16df731ff28b52f2b803da5633ccdd2c35e19936)에 커밋 로그로 남겨두었습니다.

