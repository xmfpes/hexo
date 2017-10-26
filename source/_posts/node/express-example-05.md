---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 05(Mongoose 이용 MongoDB Data Insert하기)
date: 2017-09-19 18:04:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: 
- JavaScript
- Node.js
---

## **Node.js Mongoose를 이용해 MongoDB에 Data Insert**

![node](/images/node.png)

Mongoose를 이용해 MongoDB에 값을 넣는 작업을 실행한다.

## STEP 1. body-parser, morgan 패키지 다운로드
form에서 넘어온 데이터를 javascript 객체로 매핑해주기 위해서 body-parser 패키지 추가

get, post 요청에 대한 로깅 기능을 수행하기 위해 morgan 패키지도 같이 설치한다.
```bash
    $ npm install --save body-parser morgan
```
## STEP 2. app.js에 body-parser, morgan 모듈 추가
모듈을 추가하고, 템플릿 엔진 셋팅 코드 하부에 셋팅 추가
```javascript
var logger = require('morgan');
var bodyParser = require('body-parser');

//-- 템플릿 엔진 셋팅 코드... --
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

## STEP 3. routes/admin.js 코드 수정

기존 라우트 코드 아래, exports 부분 위에 아래 코드 추가

```javascript
router.get('/products/write', function(req,res){
    res.render( 'admin/form');
});
// exports 부분
```

## STEP 4. /views/admin/form.ejs 뷰 파일 추가 및 수정

글을 등록할때 필요한 뷰 파일을 등록한다.

/views/admin/form.ejs
```html
<% include ../includes/header.ejs %>
    <form action="" method="post">
        <table class="table table-bordered">
            <tr>
                <th>제품명</th>
                <td><input type="text" name="name" class="form-control"/></td>
            </tr>
            <tr>
                <th>가격</th>
                <td><input type="text" name="price" class="form-control"/></td>
            </tr>
            <tr>
                <th>설명</th>
                <td><input type="text" name="description" class="form-control"/></td>
            </tr>
        </table>
        <button class="btn btn-primary">작성하기</button>
    </form>
<% include ../includes/footer.ejs %>
```

/views/admin/products.ejs 내부의 글쓰기 링크도 수정

```html
<a href="/admin/products/write" class="btn btn-default">작성하기</a>
```

## STEP 5. /routes/admin.js에 DB 저장 코드 추가

/routes/admin.js 상단

mongoose 셋팅 초기에 만든 모델을 불러온다.

DB가 커넥트 되기 전에 admin 모듈을 불러온다면 에러가 발생할 수 있으니 주의.
```javascript
var ProductsModel = require('../models/ProductsModel');
```
post로 받을 경로를 매핑한다.
```javascript
router.post('/products/write', function(req,res){
    var product = new ProductsModel({
        name : req.body.name,
        price : req.body.price,
        description : req.body.description,
    });
    product.save(function(err){
        res.redirect('/admin/products');
    });
});
```

이후에 /admin/products 페이지에서 작성 버튼을 눌러

form에서 값을 입력하고 Robo Mongo나 콘솔에서 데이터가 정상적으로 들어갔는지 확인한다.

```
DeprecationWarning: Mongoose: mpromise (mongoose's default promise library) is deprecated, plug in your own promise library instead: http://mongoosejs.com/docs/promises.html
```
위와 같은 Warning이 출력되는데, 다음장에서 체크한다.

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/7cd5f650eb7e66083b6f37a403fce946c2a41fbb)에 커밋 로그로 남겨두었습니다.