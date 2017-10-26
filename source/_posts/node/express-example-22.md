---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 22(채팅 기능 구현하기 - 2)
date: 2017-10-14 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Socket.io
categories: 
- JavaScript
- Node.js
---

## **채팅 기능 추가하기 - 02**

![node](/images/node.png)

메세지 출력시, 사용자 아이디를 출력하고 접속 유저 리스트를 확인하는 코드를 작성한다.

## STEP 1. 뷰 파일 수정

/views/chat/index.ejs

```html
<% include ../includes/header.ejs %>
<div class="row">
    <div class="col-sm-10">
        <div class="panel panel-default" id="chatWrap">
            <div class="panel-heading">대화내용</div>
            <div class="panel-body">
                <ul id="chatBody"></ul>
            </div>
        </div>
    </div>
    <div class="col-sm-2">
        <div class="panel panel-default" id="userWrap">
            <div class="panel-heading">User</div>
            <div class="panel-body">
                <ul id="userList"></ul>  
            </div>
        </div>
    </div>    
</div>

<div>
    <form action="" method="post" id="sendForm">

        <div class="input-group">
            <input type="hidden" name="socketId">
            <input type="text" name="message" class="form-control" placeholder="대화내용을 입력해주세요.">
            <span class="input-group-btn">
                <button class="btn btn-primary">작성하기</button>
            </span>
        </div><!-- /input-group -->

    </form>  

</div>

<style type="text/css">
.panel-default ul { padding-left:0px; }
.panel-default ul li { list-style:none; padding-left:0px;}
.panel-default .panel-body {min-height:350px; max-height:350px;  overflow-y:scroll; }
#chatWrap ul li strong::after { content: " : "; }
@media (max-width: 768px) {
    #userWrap { display:none; }
    #chatWrap .panel-body { min-height:250px; }
}
</style>
<script src="/socket.io/socket.io.js"></script>  
<script>
(function(){
    var socket = io();

    socket.on('server message', function(data){
        $('#chatBody').append('<li>' + data + '</li>');
    });

    $(document).ready(function() {
        $('#sendForm').submit(function(){
            var $massage = $('#sendForm input[name=message]');
            socket.emit('client message', { message : $massage.val()});
            $massage.val('');
            return false;
        });
    });

})();
</script>
<% include ../includes/footer.ejs %>
```

## STEP 2. 라우팅 수정

로그인 사용자만 /chat -> URL에 접근할수 있게 수정

/routes/chat.js

```javascript
var express = require('express');
var router = express.Router();

router.get('/', function(req,res){
    if(!req.isAuthenticated()){
        res.send('<script>alert("로그인이 필요한 서비스입니다.");location.href="/accounts/login";</script>');
    }else{
        res.render('chat/index');
    }
});

module.exports = router;
```

## STEP 3. 현재 접속 유저 정보 가져오기
접속 유저 정보에 접근해, 데이터를 가져와야 한다.
이를 위해 socket.io에서 passport의 req.user에 접근해야하는데 관련 미들웨어를 작성한다.



#### connect-mongo 모듈 다운로드

```
npm install --save connect-mongo
```
세션을 mongoDB에 저장하고, 지정한 시간동안 유지하는 모듈

app.js의 아래 세션 처리 부분을 수정한다.
```javascript
//아래의 코드 부분을 수정한다.
app.use(session({
  secret: 'fastcampus',
  resave: false,
  saveUninitialized: true,
  cookie: {
    maxAge: 2000 * 60 * 60 //지속시간 2시간
}
//....
var listen = require('socket.io');
var io = listen(server);
require('./libs/socketConnection')(io);
```
위의 세션 설정 부분을 아래와 같이 connectMongo를 이용해 세션 정보를 저장하도록 수정한다.

```javascript
//session 관련 셋팅
var connectMongo = require('connect-mongo');
var MongoStore = connectMongo(session);

var sessionMiddleWare = session({
    secret: 'fastcampus',
    resave: false,
    saveUninitialized: true,
    cookie: {
      maxAge: 2000 * 60 * 60 //지속시간 2시간
    },
    store: new MongoStore({
        mongooseConnection: mongoose.connection,
        ttl: 14 * 24 * 60 * 60
    })
});
app.use(sessionMiddleWare);
//...

var listen = require('socket.io');
var io = listen(server);
//socket io passport 접근하기 위한 미들웨어 적용
io.use(function(socket, next){
  sessionMiddleWare(socket.request, socket.request.res, next);
});
require('./libs/socketConnection')(io);
```

## STEP 4. SocketConnection 부분 수정하기

세션에서 유저 데이터를 가져와서 전송 데이터에 포함시킨다.

libs/socketConnection.js
```javascript
module.exports = function(io) {
    io.on('connection', function(socket){

        //아래 두줄로 passport의 req.user의 데이터에 접근한다.
        var session = socket.request.session.passport;
        var user = (typeof session !== 'undefined') ? ( session.user ) : "";

        //사용자 명과 메시지를 같이 반환한다.
        socket.on('client message', function(data){
            io.emit('server message', { message : data.message , displayname : user.displayname });
        });

    });
};
```

## STEP 5. View의 Socket.io 부분 수정하기

view의 출력 메세지에서 사용자 명 보이게 설정

/views/chat/index.ejs

```javascript
socket.on('server message', function(data){
    $('#chatBody').append('<li><strong>'+ data.displayname +'</strong>' + data.message + '</li>');
});
$(document).ready(function() {
    $('#sendForm').submit(function(){
        var $massage = $('#sendForm input[name=message]');
        socket.emit('client message', { message : $massage.val()});
        $massage.val('');
        return false;
    });
});
```

## STEP 6. View에서 채팅방에 접속한 유저 리스트 출력하기

접속시 userList에 접속한 유저의 id를 담고

emit으로 join 메세지에 userList를 담아서 클라이언트들에게 뿌려준다.

서버쪽 코드 작성


/libs/socketConnection.js

```javascript
var userList = []; //사용자 리스트를 저장할곳
module.exports = function(io) {
    io.on('connection', function(socket){

        var session = socket.request.session.passport;
        var user = (typeof session !== 'undefined') ? ( session.user ) : "";

        // userList 필드에 사용자 명이 존재 하지 않으면 삽입
        if(userList.indexOf(user.displayname) === -1){
            userList.push(user.displayname);
        }
        io.emit('join', userList);

        //사용자 명과 메시지를 같이 반환한다.
        socket.on('client message', function(data){
            io.emit('server message', { message : data.message , displayname : user.displayname });
        });

    });
};
```

상단에 userList를 선언하고, emit을 통해 join에 userList를 담아 전송한다.


클라이언트 쪽 코드 작성

/views/chat/index.ejs
```html
<script>
(function(){
   var socket = io();

    function updateUserList(userList){
        $('#userList').html("");
        for(var key in userList){
            $('#userList').append('<li>' + userList[key] + '</li>');
        }
    }

    socket.on('join', function(data){
        updateUserList(data);
    });
    socket.on('server message', function(data){
        $('#chatBody').append('<li><strong>'+ data.displayname +'</strong>' + data.message + '</li>');
    });
    $(document).ready(function() {
        $('#sendForm').submit(function(){
            var $massage = $('#sendForm input[name=message]');
            socket.emit('client message', { message : $massage.val()});
            $massage.val('');
            return false;
        });
    });
})();
</script>
```

## STEP 7. 방을 나갔을 때, 유저 리스트에서 삭제하기

서버 쪽 코드 작성

배열에서 key값을 찾아서 삭제해주는 라이브러리 생성

libs/removeByValue.js 생성

```javascript
module.exports = function(){
    Array.prototype.removeByValue = function (search) {
        var index = this.indexOf(search);  
        if (index !== -1) {
            this.splice(index, 1);
        }
    };
};
```

libs/socketConnection.js

모듈을 불러오고 기존 코드 이외에 leave 메세지 추가
```javascript
require('./removeByValue')();

module.exports = function(io) {
//...

socket.on('disconnect', function(){            
    userList.removeByValue(user.displayname);
    io.emit('leave', userList);
});
```

클라이언트 측 코드 작성(뷰)

/views/chat/index.ejs

```javascript
socket.on('leave', function(data){
    updateUserList(data);
});
```

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/1752aa62d017f46c5fa59958ce98d2fa84349b1c)에 커밋 로그로 남겨두었습니다.