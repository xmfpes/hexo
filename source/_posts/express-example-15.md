---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 15(Multer를 이용한 게시글 사진 업로드)
date: 2017-09-20 21:19:47
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
- FileUpload
- Multer
categories: Node.js
---

## **파일 업로드 기능 추가하기**

![express](/images/node.png)

파일 업로드 적용을 위한 단계

- multer 모듈 설치
- 파일을 저장할 /uploads 디렉토리 생성
- DB에 파일 경로를 저장할 필드 추가
- routes 처리 write, edit 부분의 post
- view 부분 처리

## STEP 1. multer 모듈 설치

```bash
  $ npm install --save multer
```

## STEP 2. /uploads 디렉토리 생성

최상위 디렉토리(routes랑 같은 레벨)
에 /uploads 폴더 생성

이후 uploads path를 static 경로 설정 추가

/app.js
```javascript
//...cookieParser 부분
//cookieParser 아래에 업로드 path 추가
app.use('/uploads', express.static('uploads'));
```
/uploads로 들어오는 경로는 express.static('uploads')); 로 들어온다는 것

/uploads로 접근이 가능하도록 static 경로 설정 추가

## STEP 3. Model에 파일의 경로를 저장할 thumnail 필드 추가

/model/ProductsModel.js에 파일 경로 지정할 필드 추가

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
    }
});
```
## STEP 4. view 코드 수정 multipart 옵션 및 thumnail 필드 추가
/views/admin/form.ejs

multipart 옵션 추가해주기
```html
...
<form action="" method="post" enctype="multipart/form-data">
...
```

thumnail이 들어갈 필드 제품명 필드 아래에 추가

```html
//---제품명---
<tr>
    <th>섬네일</th>
    <td>
        <input type="file" name="thumbnail" />
    </td>
</tr>
```

## STEP 5. routes 코드 작성

/routes/admin.js
```javascript
//이미지 저장되는 위치 설정
var path = require('path');
var uploadDir = path.join( __dirname , '../uploads' ); // 루트의 uploads위치에 저장한다.
var fs = require('fs');
```

바로 아랫 부분에 multer 셋팅 추가

```javascript
//multer 셋팅
var multer  = require('multer');
var storage = multer.diskStorage({
    destination : function (req, file, callback) { //이미지가 저장되는 도착지 지정
        callback(null, uploadDir );
    },
    filename : function (req, file, callback) { // products-날짜.jpg(png) 저장
        callback(null, 'products-' + Date.now() + '.'+ file.mimetype.split('/')[1] );
    }
});
var upload = multer({ storage: storage });
```

post 요청 /admin/products/write 에서 thumbnail 필드명 저장하도록 routes 수정

```javascript
router.post('/products/write', upload.single('thumbnail'), csrfProtection, function(req,res){
    var product = new ProductsModel({
        name : req.body.name,
        thumbnail : (req.file) ? req.file.filename : "",
        price : req.body.price,
        description : req.body.description,
    });
    //이 아래는 수정되지 않았음
    var validationError = product.validateSync();
    if(validationError){
        res.send(validationError);
    }else{
        product.save(function(err){
            res.redirect('/admin/products');
        });
    }
    //이 위는 수정되지 않았음
});
```

post 요청 /admin/products/edit:id 에서 수정 시 thumbnail 필드명 저장하도록 routes 수정
```javascript
router.post('/products/edit/:id', upload.single('thumbnail'), csrfProtection, function(req, res){
    //그전에 지정되 있는 파일명을 받아온다
    ProductsModel.findOne( {id : req.params.id} , function(err, product){
        //넣을 변수 값을 셋팅한다
        var query = {
            name : req.body.name,
            thumbnail : (req.file) ? req.file.filename : product.thumbnail,
            price : req.body.price,
            description : req.body.description,
        };

        ProductsModel.update({ id : req.params.id }, { $set : query }, function(err){
            res.redirect('/admin/products/detail/' + req.params.id);
        });
    });
});
```

## STEP 6. view 파일 수정

views/admin/productsdetail.ejs

작성일 아래, description 윗부분에 추가

```html
<div style="padding-bottom: 10px">
작성일 :
    <%=product.getDate.year%> -
    <%=product.getDate.month%> -
    <%=product.getDate.day%>
</div>

<% if(product.thumbnail){%>
<p>
<img src="/uploads/<%=product.thumbnail%>" style="max-width: 100%"/>
</p>
<% } %>
```
views/admin/form.ejs
에서 thumbnail 이미지 가져오도록 수정하기
```html
<tr>
	<th>섬네일</th>
	<td>
	    <input type="file" name="thumbnail" />
	    <% if(product.thumbnail){ %>
		<a href="/uploads/<%=product.thumbnail%>" target="_blank">업로드 이미지 보기</a>
	    <% } %>
	</td>
</tr>
```

여기까지 하면 파일 업로드가 정상적으로 완료 되고, db에 thumbnail 경로가 저장되며
글 클릭시 이미지가 출력된다.

## STEP 7. 글 수정 시 이전 파일 삭제하기

글을 수정해서 새로운 이미지를 넣었을 경우에는 이전에 저장했던 이미지를 삭제하고
새로운 이미지를 저장하도록 수정한다.

글의 삭제는 내장모듈인 fs를 이용한다.

post 요청의 routes 수정 /admin/products/edit/:id

아래의 코드에서 주석처리 if 구문을 추가하여
요청 파일이 존재 할 시, 이전의 파일을 삭제한다.

조건에 req.file만 넣는다면

이미지가 없는 글에 이미지를 수정하여 넣는 경우, 없는 파일을 삭제하려고 해 에러가 발생한다.

```javascript
router.post('/products/edit/:id', upload.single('thumbnail'), csrfProtection, function(req, res){
    //그전에 지정되 있는 파일명을 받아온다
    ProductsModel.findOne( {id : req.params.id} , function(err, product){
        //아래의 코드만 추가되면 된다.
        if(req.file && product.thumbnail){  //요청중에 파일이 존재 할시 이전이미지 지운다.
            fs.unlinkSync( uploadDir + '/' + product.thumbnail );
        }
        //위의 코드만 추가되면 된다.

        //넣을 변수 값을 셋팅한다
        var query = {
            name : req.body.name,
            thumbnail : (req.file) ? req.file.filename : product.thumbnail,
            price : req.body.price,
            description : req.body.description,
        };

        ProductsModel.update({ id : req.params.id }, { $set : query }, function(err){
            res.redirect('/admin/products/detail/' + req.params.id);
        });
    });
});
```

위의 삭제는 동기식으로 삭제하는데

비동기식으로 삭제하는 경우는 아래와 같이 처리해주면 된다.
```javascript
fs.unlink( 경로 ,  function(err) => {
	//추가 부분
});
```

현재는 글 삭제시 이미지를 삭제하지 않는데, 원한다면 delete 라우트에 추가하면 된다.
