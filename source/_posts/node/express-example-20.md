---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 20(핀터레스트 UI 적용 / 로그인 미들웨어 적용)
date: 2017-10-11 21:25:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
categories: Node.js
---

## **Pinterest UI 적용 / 로그인 미들웨어 생성**

![node](/images/node.png)

올린 상품들 리스트를 Pinterest UI를 적용해 표시해보자.

그리고 로그인을 해야 글을 등록 할 수 있도록 미들웨어를 추가하자.

## STEP 1. 글 작성자 필드 추가하기

다른 작업에 앞서, 상품 모델에 작성자가 없으므로 작성자 필드를 추가하자.

models/ProductsModel.js

```javascript
//생성될 필드명을 정한다.
var ProductsSchema = new Schema({
    name : { //제품명
        type : String,
        required: [true, '제목은 입력해주세요']
    },
    thumbnail : String, //이미지 파일명
    price : Number, //가격
    description : String, //설명
    created_at : { //작성일
        type : Date,
        default : Date.now()
    },
    username : String  //작성자추가
});
```


## STEP 2. 로그인 확인 미들웨어 작성 및 적용하기

로그인을 해야 글을 쓸 수 있도록 하기 위해서 로그인을 체크하는 미들웨어를 작성하자.

로그인이 안되어 있다면 로그인 페이지로 이동시킨다.

모듈 작성 libs/loginRequired.js

로그인이 되어있지 않다면, 로그인 페이지로 redirect 하는 모듈을 작성한다.

```javascript
module.exports = function(req, res, next) {
    if (!req.isAuthenticated()){
        res.redirect('/accounts/login');
    }else{
        return next();
    }
};
```

모듈 로드 routes/admin.js 상단

```javascript
var loginRequired = require('../libs/loginRequired');
```

POST /admin/products/write 미들웨어 추가 및 작성자 정보 추가

```javascript
router.post('/products/write', upload.single('thumbnail'),loginRequired, csrfProtection, function(req,res){
    var product = new ProductsModel({
        name : req.body.name,
        thumbnail : (req.file) ? req.file.filename : "",
        price : req.body.price,
        description : req.body.description,
        username : req.user.username
    });
    //......
});
```

글작성시 로그인 체크

GET /admin/products/write
```javascript
router.get('/products/write',loginRequired, csrfProtection, function(req,res){
```

글 수정시 로그인 체크

GET /admin/products/edit/:id
```javascript
router.get('/products/edit/:id',loginRequired, parseForm, csrfProtection, function(req, res){
```

POST /admin/products/edit/:id

```javascript
router.post('/products/edit/:id',loginRequired, upload.single('thumbnail') , csrfProtection, function(req, res){
```

## STEP 3. 메인페이지 라우팅 변경하기

기존에 아무 내용이 없던 /경로의 메인페이지 라우팅을 없애고, home 라우팅을 작성하자

app.js

```javascript
var home = require('./routes/home.js');
//.. 중략
app.use('/', home);
```

routes/home.js
```javascript
var express = require('express');
var router = express.Router();
var ProductsModel = require('../models/ProductsModel');

/* GET home page. */
router.get('/', function(req,res){
    ProductsModel.find( function(err,products){ //첫번째 인자는 err, 두번째는 받을 변수명
        res.render( 'home' ,
            { products : products } // DB에서 받은 products를 products변수명으로 내보냄
        );
    });
});

module.exports = router;
```


## STEP 4. 뷰 파일 작성하기

masonry ui(Pinterest UI)가 적용된 뷰 파일을 작성하자.

views/home.ejs

```html
<% include ./includes/header.ejs %>
    <div class="container" id="masonry_container">
        <% products.forEach(function(product){ %>
        <div class="masonry-grid">
            <%if(product.thumbnail){%>
                <img src="/uploads/<%=product.thumbnail%>">
            <%}%>
            <p>
                <%=product.title%><br />
                by <%=product.username%>(
                <%=product.getDate.year%>.
                <%=product.getDate.month%>.
                <%=product.getDate.day%>
                )
            </p>
        </div>
        <%});%>
    </div>
<style type="text/css">
.masonry-grid img { max-width: 260px; }
</style>
<script src="https://unpkg.com/masonry-layout@4/dist/masonry.pkgd.min.js"></script>
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery.imagesloaded/4.1.1/imagesloaded.pkgd.min.js"></script>
<script type="text/javascript">
    var $masonry_container = $('#masonry_container');
    $masonry_container.imagesLoaded(function(){
      $masonry_container.masonry({
        itemSelector : '.masonry-grid',
        columnWidth : 270
      });
    });
</script>
<% include ./includes/footer.ejs %>
```

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/bb1d13c9bd2344db8078c664c4ee208190630a2f)에 커밋 로그로 남겨두었습니다.
