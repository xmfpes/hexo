---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 09(Mongoose 이용 MongoDB 데이터 수정하기(update))
date: 2017-09-20 16:37:04
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: Node.js
thumbnail: /images/node.png
---

## **Node.js Mongoose를 이용해 글 내용 수정하기(update)**

## STEP 1. API 작성
/routes/admin.js 수정하기

아래의 경로로 수정할 뷰 파일을 매핑한다.
```javascript
router.get('/products/edit/:id' ,function(req, res){
    //기존에 폼에 value안에 값을 셋팅하기 위해 만든다.
    ProductsModel.findOne({ id : req.params.id } , function(err, product){
        res.render('admin/form', { product : product });
    });
});
```
## STEP 2. view 파일 코드 수정
/views/form.ejs 뷰 파일의 코드를 약간 수정한다.
위의 경로로 요청이 날아왔을 때, product 데이터를 받아
해당 폼에 내용을 로드해서 채워주어야 한다.
변경되는 부분은 각 input form의 value 부분이다.

```html
<% include ../includes/header.ejs %>
    <form action="" method="post">
        <table class="table table-bordered">
            <tr>
                <th>제품명</th>
                <td><input type="text" name="name" class="form-control"  value="<%=product.name%>"/></td>
            </tr>
            <tr>
                <th>가격</th>
                <td><input type="text" name="price" class="form-control" value="<%=product.price%>"/></td>
            </tr>
            <tr>
                <th>설명</th>
                <td><input type="text" name="description" class="form-control" value="<%=product.description%>"/></td>
            </tr>
        </table>
        <button class="btn btn-primary">작성하기</button>
    </form>
<% include ../includes/footer.ejs %>
```

## STEP 3. 수정 API routes 구현

위에서 get으로 뷰 페이지에 매핑을 해주었는데, 이번에는
데이터를 업데이트 해 줄 post api에 매핑을 해주어야 한다.

```javascript
router.post('/products/edit/:id', function(req, res){
    //넣을 변수 값을 셋팅한다
    var query = {
        name : req.body.name,
        price : req.body.price,
        description : req.body.description,
    };
 
    //update의 첫번째 인자는 조건, 두번째 인자는 바뀔 값들
    ProductsModel.update({ id : req.params.id }, { $set : query }, function(err){
        res.redirect('/admin/products/detail/' + req.params.id ); //수정후 본래보던 상세페이지로 이동
    });
});
```

## STEP 4. 기존 글 작성 에러 수정
글 작성과 수정 모두 같은 /admin/form.ejs를 사용하면서
에러가 발생한다.
기존의 글 작성시에는 받아올 데이터가 없어서 
```
value=<%=product.name%>
```
위와 같은 value값을 받아오는 과정에서 에러가 발생한다.

위와 같은 에러를 해결하기 위해, 작성할 때에는 아무것도 들어있지 않은 product를 넘겨준다
routes/admin.js 의 기존 write 매핑 부분을 아래와 같이 수정한다.

```javascript
router.get('/products/write', function(req,res){
    //edit에서도 같은 form을 사용하므로 빈 변수( product )를 넣어서 에러를 피해준다
    res.render( 'admin/form' , { product : "" }); 
});
```