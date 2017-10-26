---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 06(Mongoose 이용 MongoDB Data 가져오기)
date: 2017-09-19 18:05:00
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

## **Node.js Mongoose를 이용해 MongoDB Data 가져오기**

![node](/images/node.png)

Mongoose를 이용해 MongoDB에서 원하는 값을 가져오는 작업을 한다.

## STEP 1. DeprecationWarning 제거

```bash
DeprecationWarning: Mongoose: mpromise (mongoose's default promise library) is deprecated, plug in your own promise library instead: http://mongoosejs.com/docs/promises.html
```
위와 같은 Warning이 발생했었다.

default promise가 deprecate 되었으므로 교체하라는 그런 내용인것 같다.

app.js의 mongoose 모듈을 가져오는 부분 바로 아래 코드에 promise 관련 코드를 추가한다.
```javascript
var mongoose = require('mongoose');
//위의 셋팅 아랫 부분에 추가하기
mongoose.Promise = global.Promise;
```

## STEP 2. products의 리스트를 받아오는 코드 추가

/routes/admin.js의
get, /products 부분의 라우트에서 출력할 리스트를 받아
뷰로 넘겨주는 코드를 작성한다
```javascript
router.get('/products', function(req, res){
    ProductsModel.find(function(err, products){
        res.render('admin/products', 
            { products : products }    
            //ProductModel의 products를 받아서
            //admin/products로 response를 보낸다.
        );
    });
});
```

이후, 리스트를 출력해주는 뷰 코드를 ejs 템플릿 엔진을 사용해 작성한다.
/views/admin/products.ejs 수정
```html
<% include ../includes/header.ejs %>

    <table class="table table-bordered table-hover">
        <tr>
            <th>제목</th>
            <th>작성일</th>
            <th>삭제</th>
        </tr>
        <%products.forEach(function(product){%>
        <tr>
            <td>
                <a href="/admin/products/detail/<%=product.id%>"><%=product.name%></a>
            </td>
            <td><%=product.created_at%></td>
            <td>
                <a href="#" class="btn btn-danger">삭제</a>
            </td>
        </tr>
        <% }); %>
    </table>
 
    <a href="/admin/products/write" class="btn btn-default">작성하기</a>
 
<% include ../includes/footer.ejs %>
```

화면에 MongoDB에 저장된 값들이 출력되는것을 확인할 수 있다.

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/0c4b27d2fbbf870f99fe3abdee161cae8442df75)에 커밋 로그로 남겨두었습니다.