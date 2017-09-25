---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 08(Mongoose 이용 MongoDB 특정 데이터 가져오기(findOne))
date: 2017-09-19 18:07:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: Node.js
---

## **Node.js Mongoose를 이용해 특정 데이터를 가져와 Detail 페이지 제작하기(findOne)**

![node](/images/node.png)

Mongoose를 이용해 MongoDB에서 특정 데이터(id를 이용)를 가져와서 상세 페이지를 제작한다.

## STEP 1. API 작성
/routes/admin.js 파일에 routes 추가

```javascript
router.get('/products/detail/:id' , function(req, res){
    //url 에서 변수 값을 받아올떈 req.params.id 로 받아온다
    ProductsModel.findOne( { 'id' :  req.params.id } , function(err ,product){
        res.render('admin/productsDetail', { product: product });  
    });
});
```
## STEP 2. detail view 파일 작성

views/admin/productsDetail.ejs

```html
<% include ../includes/header.ejs %>

    <div class="panel panel-default">
        <div class="panel-heading">
            <%=product.name%>
        </div>
        <div class="panel-body">
            <div style="padding-bottom: 10px">
                작성일 : 
                <%=product.getDate.year%> - 
                <%=product.getDate.month%> - 
                <%=product.getDate.day%>
            </div>
            <%=product.description%>
        </div>
    </div>
 
    <a href="/admin/products" class="btn btn-default">목록으로</a>
    <a href="/admin/products/edit/<%=product.id%>" class="btn btn-primary">수정</a>

<% include ../includes/footer.ejs %>
```

글 리스트에서 해당 글을 클릭했을 경우, 해당 글의 상세페이지를 보여주는지를 체크한다.

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/6af84540676d2826a795cf2d6c8ff910211d858a)에 커밋 로그로 남겨두었습니다.