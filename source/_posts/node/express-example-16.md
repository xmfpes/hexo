---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 16(회원가입 기능 추가)
date: 2017-09-21 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: Node.js
thumbnail: /images/node.png
---

## **회원가입 기능 추가하기**

회원가입 기능을 추가한다.

유저 정보를 저장할 모델을 생성하고, 회원 비밀번호는 암호화하여 저장한다.

## STEP 1. 회원 정보를 저장할 User Model 생성

models/UserModel.js 파일 생성

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var autoIncrement = require('mongoose-auto-increment');

var UserSchema = new Schema({
  username : {
      type : String,
      required: [true, '아이디는 필수입니다.']
  },
  password : {
      type : String,
      required: [true, '패스워드는 필수입니다.']
  },
  displayname : String,
  created_at : {
      type : Date,
      default : Date.now()
  }
});

UserSchema.plugin( autoIncrement.plugin , { model : "user", field : "id" , startAt : 1 } );
module.exports = mongoose.model('user' , UserSchema);
```

## STEP 2. 회원가입을 위한 routes 추가하기

가입 폼에 해당하는 라우팅과, 로그인을 위한 라우팅 추가

routes/account.js

```javascript
var express = require('express');
var router = express.Router();
var UserModel = require('../models/UserModel');


router.get('/', function(req, res){
    res.send('account app');
});

router.get('/join', function(req, res){
    res.render('accounts/join');
});

router.get('/login', function(req, res){
    res.render('accounts/login');
});

module.exports = router;
```

app.js에 라우팅을 사용하는 부분 추가

```javascript
var accounts = require('./routes/accounts');
//..중략
app.use('/accounts', accounts);
```

## STEP 3. 회원가입을 위한 View 코드 작성

회원가입 폼 생성
/views/account/join.ejs

```html
<% include ../includes/header.ejs %>
<div class="row">
    <div class="col-md-4 col-md-offset-4">
        <div class="login-panel panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title">회원가입</h3>
            </div>
            <div class="panel-body">
                <form role="form" action="" id="join_form" method="post">
                    <fieldset>
                        <div class="form-group">
                            <input class="form-control" placeholder="ID" name="username" type="text" autofocus="" required="">
                        </div>
                        <div class="form-group">
                            <input class="form-control" placeholder="Password" name="password" type="password" value="" required="">
                        </div>
                        <div class="form-group">
                            <input class="form-control" placeholder="Password 확인" name="password2" type="password" value="" required="">
                        </div>
                        <div class="form-group">
                            <input class="form-control" placeholder="이름" name="displayname" type="text" value="" required="">
                        </div>
                        <!-- Change this to a button or input when using this as a form -->
                        <input type="submit" class="btn btn-lg btn-success btn-block" value="가입하기">
                    </fieldset>
                </form>
            </div>
        </div>
    </div>
</div>
<script type="text/javascript">
(function(){
    $(document).ready(function() {
        $('#join_form').submit(function(){
            var $usernameInput = $('#join_form input[name=username]');
            var $passwordInput = $('#join_form input[name=password]');
            var $passwordInput2 = $('#join_form input[name=password2]');
            var $displayname = $('#join_form input[name=displayname]');

            if(!$usernameInput.val()){
                alert("아이디를 입력해주세요.");
                $usernameInput.focus();
                return false;
            }
            if(!$passwordInput.val()){
                alert("패스워드를 입력해주세요.");
                $passwordInput.focus();
                return false;
            }
            if(!$passwordInput2.val()){
                alert("확인 패스워드를 입력해주세요.");
                $passwordInput2.focus();
                return false;
            }
            if(!$displayname.val()){
                alert("이름을 입력해주세요.");
                $displayname.focus();
                return false;
            }
            if($passwordInput.val() !== $passwordInput2.val()){
                alert("패스워드와 확인용패스워드를 똑같이 입력해주세요.");
                return false;
            }
            return true;
        });
    });
})();
</script>
<% include ../includes/footer.ejs %>
```

로그인 폼 생성
/views/account/join.ejs
```html
<% include ../includes/header.ejs %>
<div class="row">
    <div class="col-md-4 col-md-offset-4">
        <div class="login-panel panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title">로그인</h3>
            </div>
            <div class="panel-body">
                <form role="form" action="" id="login_form" method="post">
                    <fieldset>
                        <div class="form-group">
                            <input class="form-control" placeholder="ID" name="username" type="text" autofocus="" required="">
                        </div>
                        <div class="form-group">
                            <input class="form-control" placeholder="Password" name="password" type="password" value="" required="">
                        </div>
                        <!-- Change this to a button or input when using this as a form -->
                        <input type="submit" class="btn btn-lg btn-success btn-block" value="로그인">
                    </fieldset>
                </form>
            </div>
        </div>
    </div>
</div>
<script type="text/javascript">
(function(){
    $(document).ready(function() {
        $('#login_form').submit(function(){
            var $usernameInput = $('#login_form input[name=username]');
            var $passwordInput = $('#login_form input[name=password]');

            if(!$usernameInput.val()){
                alert("아이디를 입력해주세요.");
                $usernameInput.focus();
                return false;
            }
            if(!$passwordInput.val()){
                alert("패스워드를 입력해주세요.");
                $passwordInput.focus();
                return false;
            }

            return true;
        });
    });
})();
</script>
<% include ../includes/footer.ejs %>
```

## STEP 4. 회원 비밀번호 암호화를 위한 모듈 생성

루트 디렉토리에서 libs 폴더 생성

패스워드 암호화를 위한 작업을 진행할 libs/passwordHash.js 파일생성
```javascript
var crypto = require('crypto');
var mysalt = "fastcampus";

module.exports = function(password){
    return crypto.createHash('sha512').update( password + mysalt).digest('base64');
};
```

## STEP 5. 라우팅에 암호화 모듈을 이용한 암호화 기능 추가

상단에서 암호화 모듈을 불러온다.
```javascript
var passwordHash = require('../libs/passwordHash');
```

post 요청의 join에 암호화 기능 추가
```javascript
router.post('/join', function(req, res){
    var User = new UserModel({
        username : req.body.username,
        password : passwordHash(req.body.password),
        displayname : req.body.displayname
    });
    User.save(function(err){
        res.send('<script>alert("회원가입 성공");location.href="/accounts/login";</script>');
    });
});
```
