---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 18(페이스북 소셜 로그인 기능 추가)
date: 2017-09-27 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
- passport
- facebook
categories: Node.js
---

## **페이스북 로그인 기능 추가하기**

이전에 사용한 passport를 이용해 페이스북을 이용한 소셜 로그인 기능을 추가합니다.

![node](/images/node.png)

## STEP 1. 모듈 설치

passport-facebook 모듈을 설치한다.

```bash
$ npm install --save passport-facebook
```

## STEP 2. 로그인 뷰 페이스북 로그인 및 회원가입 버튼 추가

로그인 뷰에 페이스북 아이콘 및 로그인 버튼을 추가한다.

font-awesome을 사용해서 페이스북 아이콘을 추가한다.

views/header.ejs에 아래의 내용을 추가한다.

```html
<link rel="stylesheet" type="text/css" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">
   <!--폰트어섬추가 -->
```

views/accounts/login.ejs 에 페이스북 로그인 버튼 추가하기
기존의 input 로그인 버튼 아래에 페이스북 로그인 버튼을 추가
```html
<!-- Change this to a button or input when using this as a form -->
    <!--기존 로그인 버튼 <input type="submit" class="btn btn-lg btn-success btn-block" value="로그인"> -->
    <div style="margin-top: 10px">
        <a href="/auth/facebook" class="btn btn-lg btn-primary btn-block">
            <i class="fa fa-facebook" aria-hidden="true"></i> 페이스북 로그인
        </a>
    </div>
```
views/accounts/join.ejs 에 기존 회원가입 버튼 아래에 페이스북 회원가입 버튼 추가하기
```html
<div style="margin-top: 10px">
    <a href="/auth/facebook" class="btn btn-lg btn-primary btn-block">
	<i class="fa fa-facebook" aria-hidden="true"></i> 페이스북 회원가입
    </a>
</div>
```

## STEP 3. 페이스북 로그인을 담당할 auth.js 라우팅 작성하기

우선 app.js에 추가할 라우팅을 등록하낟.

```javascript
var auth = require('./routes/auth');
//... 후략
app.use('/auth', auth);
```

routes/auth.js 생성

아래의 라우팅을 작성해 추가한다.

GET /auth/facebook -> 인증 링크 생성

GET /auth/facebook/callback -> 페이스북에서 로그인 시도후 이동하는 URL

GET /auth/facebook/success -> 로그인 성공시 이동하는 링크

GET /auth/facebook/fail -> 실패시 이동하는 링크

```javascript
var express = require('express');
var router = express.Router();
var UserModel = require('../models/UserModel');
var passport = require('passport');
var FacebookStrategy = require('passport-facebook').Strategy;

passport.serializeUser(function (user, done) {
    done(null, user);
});

passport.deserializeUser(function (user, done) {
    done(null, user);
});

passport.use(new FacebookStrategy({
        // https://developers.facebook.com에서 appId 및 scretID 발급
        clientID: "앱 ID 입력", //입력하세요
        clientSecret: "앱 시크릿 코드 입력", //입력하세요.
        callbackURL: "http://localhost:3000/auth/facebook/callback",
        profileFields: ['id', 'displayName', 'photos', 'email'] //받고 싶은 필드 나열
    },
    function(accessToken, refreshToken, profile, done) {
        //아래 하나씩 찍어보면서 데이터를 참고해주세요.
        //console.log(profile);
        //console.log(profile.displayName);
        //console.log(profile.emails[0].value);
        //console.log(profile._raw);
        //console.log(profile._json);
        UserModel.findOne({ username : "fb_" + profile.id }, function(err, user){
            if(!user){  //없으면 회원가입 후 로그인 성공페이지 이동
                var regData = { //DB에 등록 및 세션에 등록될 데이터
                    username :  "fb_" + profile.id,
                    password : "facebook_login",
                    displayname : profile.displayName
                };
                var User = new UserModel(regData);
                User.save(function(err){ //DB저장
                    done(null,regData); //세션 등록
                });
            }else{ //있으면 DB에서 가져와서 세션등록
                done(null,user);
            }

        });
    }
));

// http://localhost:3000/auth/facebook 접근시 facebook으로 넘길 url 작성해줌
router.get('/facebook', passport.authenticate('facebook', { scope: 'email'}) );


//인증후 페이스북에서 이 주소로 리턴해줌. 상단에 적은 callbackURL과 일치
router.get('/facebook/callback',
    passport.authenticate('facebook',
        {
            successRedirect: '/auth/facebook/success',
            failureRedirect: '/auth/facebook/fail'
        }
    )
);

//로그인 성공시 이동할 주소
router.get('/facebook/success', function(req,res){
    res.send(req.user);
});

router.get('/facebook/fail', function(req,res){
    res.send('facebook login fail');
});


module.exports = router;
```

## STEP 4. 페이스북 api 등록 및 앱 Id, 시크릿 Id 발급하기

우선 페이스북 개발자로 등록하여서, 앱을 등록하여야 한다.

https://developers.facebook.com/

위의 링크로 접속하여 개발자 등록을 진행하자.

정보를 작성하고 개발자로 등록을 한 다음에 앱을 생성하자.

- 앱 이름을 입력하고 앱 ID 만들기
- 왼쪽 상단 대시보드 클릭 -> 플랫폼 선택 -> 웹 사이트 선택
- 오른쪽 상단 Skip Quick Start 클릭
- 왼쪽 앱 검수 클릭 -> 앱 공개 설정 예로 변경 후 카테고리 선택
- 설정 -> 기본 설정 -> 앱 도메인 localhost 입력
- 설정 -> 기본 설정 -> 플랫폼 추가 -> 웹 사이트 추가 웹 사이트 URL http://localhost:3000/ 입력


이후 발급받은 앱 ID 및 시크릿 ID를 추출하여 routes에 추가해 주면 된다.

이미지를 바꾸려면 설정 - 기본 설정 - 웹 아이콘 변경


오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/4fff42fe7c7529e3ac341325bed4bb7cae4373a4)에 커밋 로그로 남겨두었습니다.