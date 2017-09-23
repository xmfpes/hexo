---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 11(ajax를 이용한 댓글 추가 기능 제작)
date: 2017-09-20 21:15:47
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
- Ajax
categories: Node.js
thumbnail: /images/node.png
---

## **댓글 기능 추가하기**

## STEP 1. CommentsModel 작성하기
```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var autoIncrement = require('mongoose-auto-increment');

var CommentsSchema = new Schema({
    content : String,
    created_at : {
        type : Date,
        default : Date.now()
    },
    product_id : Number
});

CommentsSchema.plugin( autoIncrement.plugin , { model: "comments", field : "id", startAt : 1 });
module.exports = mongoose.model( "comments", CommentsSchema);
```

## STEP 2. CommentsModel.js 불러오기

/routes/admin.js

```
var CommentsModel = require('../models/CommentsModel');
```

## STEP 3. footer.ejs에 JQuery CDN 추가하기

```javascript
<script
  src="https://code.jquery.com/jquery-3.2.1.min.js"
  integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
  crossorigin="anonymous"></script>
```

## STEP 4. 제품 디테일 페이지에 댓글 입력 폼 작성 / 테스트

/views/admin/productsDetail.ejs
```html
<!-- 댓글영역  -->
<div>
    댓글작성하기
    <form id="commentForm" action="" method="post">
        <input type="hidden" name="product_id" value="<%=product.id%>" />
        <textarea class="form-control" name="content"></textarea>
        <button class="btn btn-primary" style="margin-top: 10px">댓글작성</button>
    </form>
</div>
<!-- 댓글영역  -->
```

하단부에 script 삽입을 통해, 폼 데이터를 제대로 인식하는지 체크
```javascript
<script>
(function(){
    $(document).ready(function() {
        $('#commentForm').submit(function(){
            var $contentVal = $(this).children('textarea[name=content]').val();
            alert($contentVal);
            return false;
        });
    });
})();
</script>
```

위의 코드를 통해서, 정상적으로 화면에 댓글 내용이 출력되는 것을 확인하고
ajax를 통해 요청을 보낼 코드를 작성한다.

ajax 코드 작성
```javascript
(function(){
    $(document).ready(function() {
        $('#commentForm').submit(function(){
            var $contentVal = $(this).children('textarea[name=content]').val();
            if($contentVal){
                $.ajax({
                    url: '/admin/products/ajax_comment/insert',
                    type: 'POST',
                    data: $(this).serialize(),
                })
                .done(function(args) {
                    console.log(args);
                })
                .fail(function(args) {
                    console.log(args);
                });

            }else{
                alert('댓글 내용을 입력해주세요.')
            }
            return false;
        });
    });
})();
```

## STEP 5. 댓글을 받아서 저장할 라우팅 작성하기

POST /admin/products/ajax_comment/insert

정상적으로 요청이 가는지 체크하기 위해 간단한 라우팅 작성을 통해 테스트

```javascript
router.post('/products/ajax_comment/insert', function(req,res){
    res.json(req.body);
});
```
라우팅 작성 후, 뷰로 돌아와 댓글 작성 버튼 클릭 후 개발자 도구에서 console.log 확인

![commentsResponse](/images/commentsResponse.png)

전송한 댓글이 출력되는 것을 확인할 수 있다.

이제 DB에 저장되도록 코드를 수정한다.

```javascript
router.post('/products/ajax_comment/insert', function(req,res){
    var comment = new CommentsModel({
        content : req.body.content,
        product_id : parseInt(req.body.product_id)
    });
    comment.save(function(err, comment){
        res.json({
            id : comment.id,
            content : comment.content,
            message : "success"
        });
    });

});
```

개발자 도구의 console.log와 mongodb의 콜렉션을 확인해 정상적으로 저장된 것을 확인한다.

## STEP 6. 댓글을 append할 div 영역 생성하기

아래와 같이 작성한 댓글을 추가할 div 영역 생성
```html
<!-- 댓글영역  -->
<hr />
<div id="comment_area"></div>
```

댓글 작성 후 append하는 코드 js에 추가
```javascript
$.ajax({
    url: '/admin/products/ajax_comment/insert',
    type: 'POST',
    data: $(this).serialize(),
})
.done(function(args) {
    if(args.message==="success"){
        $('#comment_area').append(
            '<div>' + args.content + '</div>'
        );
        $('#commentForm textarea[name=content]').val("");
    }
})
.fail(function(args) {
    console.log(args);
});
```

## STEP 7. 디테일 페이지 로딩 시, 댓글을 불러오도록 수정

라우팅과 ejs 뷰 파일 두개를 수정해야 한다.

라우팅 수정
제품 디테일 정보를 가져온 뒤에, 그 안에서 댓글 정보를 가져온다.
```javascript
router.get('/products/detail/:id' , function(req, res){
    //url 에서 변수 값을 받아올떈 req.params.id 로 받아온다
    ProductsModel.findOne( { 'id' :  req.params.id } , function(err ,product){
        //제품정보를 받고 그안에서 댓글을 받아온다.
        CommentsModel.find({ product_id : req.params.id } , function(err, comments){
            res.render('admin/productsDetail', { product: product , comments : comments });
        });        
    });
});
```
아까 추가한 댓글 영역의 #comment_area div 수정
```html
<div id="comment_area">
    <% comments.forEach( function(comment){ %>
        <div><%=comment.content%></div>
    <%});%>
</div>
```
