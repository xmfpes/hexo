---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 21(채팅 기능 구현하기 - 1)
date: 2017-10-13 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
categories: Node.js
---
## **채팅 기능 추가하기 - 01**

![node](/images/node.png)

## STEP 1. Soket.io 모듈 설치 및 채팅 페이지 라우팅 추가

```javascript
npm install --save socket.io
```

app.js

```javascript
var chat = require('./routes/chat');
//..후략
app.use('/chat', chat);
```


routes/chat.js

```javascript
var express = require('express');
var router = express.Router();

router.get('/', function(req,res){
    res.render('chat/index');
});

module.exports = router;
```

## STEP 2. Chat 뷰 파일 작성

/views/chat/index.ejs

```html
<% include ../includes/header.ejs %>

    <div class="panel panel-default" id="chatWrap">
        <div class="panel-heading">대화내용</div>
        <div class="panel-body">
            <ul id="chatBody"></ul>
        </div>
    </div>

    <form action="" method="post" id="sendForm">

        <div class="input-group">
            <input type="hidden" name="socketId">
            <input type="text" name="message" class="form-control" placeholder="대화내용을 입력해주세요.">
            <span class="input-group-btn">
                <button class="btn btn-primary">작성하기</button>
            </span>
        </div><!-- /input-group -->

    </form>  



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


})();
</script>
<% include ../includes/footer.ejs %>
```

## STEP 3. Soket.io 서버 코드 작성하기

app.js 최하단 변경

```javascript
var server = app.listen( port, function(){
    console.log('Express listening on port', port);
});

var listen = require('socket.io');
var io = listen(server);
io.on('connection', function(socket){
    socket.on('client message', function(data){
        io.emit('server message', data.message);
    });
});
```

socket.on으로 메세지를 받고, emit으로 전체에게 메세지를 전달한다.


## STEP 4. Client측 socket.io 스크립트 작성하기

기존에 비어있던 함수를 아래의 내용으로 채운다.

클라이언트쪽에서도 socket.on으로 받고, emit으로 전송한다.

```javascript
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
```

## STEP 5. chat 모듈 분리하기

app.js에 코드가 섞여있으므로, chat 모듈을 분리해서 작성한다.

libs/socketConnection.js를 생성하자

```javascript
module.exports = function(io) {
    io.on('connection', function(socket){
        socket.on('client message', function(data){
            io.emit('server message', data.message);
        });
    });
};
```

분리한 chat 모듈을, app.js에 넣고 기존의 코드를 변경한다.

app.js

```javascript
var server = app.listen( port, function(){
    console.log('Express listening on port', port);
});

var listen = require('socket.io');
var io = listen(server);
require('./libs/socketConnection')(io);
```

## STEP 6. 헤더에 chat 메뉴 추가하기

views/header.ejs
```html
<div class="collapse navbar-collapse" id="bs-example-navbar-collapse-9">
    <ul class="nav navbar-nav">
	<li class="active">
	    <a href="/">Home</a>
	</li>
	<li><a href="/admin/products">ADMIN</a></li>
	<li><a href="/chat">CHAT</a></li>
	<% if(isLogin){%>
	    <li><a href="/accounts/logout" onclick="return confirm('로그아웃 하시겠습니까?')">LOGOUT</a></li>
	<%}else{%>
	    <li><a href="/accounts/join">JOIN</a></li>
	    <li><a href="/accounts/login">LOGIN</a></li>  
	<%}%>
    </ul>
</div>
```

모두 작성하면 아래와 같은 채팅기능을 갖춘 페이지가 보일 것이다.

아직 사용자 이름 표시나, 접속자 목록 표시 등의 기능은 없는 상태이다.

![chat](/images/node/chat.png)

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/21af2c41f953c3a98ad23eb9e504cc7fe1f555a0)에 커밋 로그로 남겨두었습니다.
