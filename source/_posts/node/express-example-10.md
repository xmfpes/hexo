---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 10(Mongoose 이용 MongoDB 데이터 삭제하기(remove))
date: 2017-09-20 21:14:41
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: Node.js
thumbnail: /images/node.png
---

## **Node.js Mongoose를 이용해 글 삭제하기(remove)**

## STEP 1. API 작성
/routes/admin.js 수정하기

아래의 경로로 접근시 해당 아이디의 게시글을 삭제하도록 매핑한다.
```javascript
router.get('/products/delete/:id', function(req, res){
    ProductsModel.remove({ id : req.params.id }, function(err){
        res.redirect('/admin/products');
    });
});
```
## STEP 2. view 파일 코드 수정
/views/admin/products.ejs 뷰 파일의 코드를 수정한다.
기존 삭제버튼 코드를 아래의 코드로 수정한다.
a 태그에 삭제와 매핑된 url로 get 요청을 보내도록 수정한다.

```html
<td>
	<a href="/admin/products/delete/<%=product.id%>" class="btn btn-danger" onclick="return confirm('삭제하시겠습니까?')">삭제</a>
</td>
```

정상적으로 삭제가 되는지 체크한다.