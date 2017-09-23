---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 12(ajax를 이용한 댓글 삭제 기능 제작)
date: 2017-09-20 21:16:47
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

## **댓글 삭제 기능 추가하기**

## STEP 1. 댓글 삭제 버튼 추가하기

삭제 버튼은 ejs에서 forEach로 추가해주는 부분과
js에서 append 하는 부분 두가지 모두에 추가해야 한다.

ejs 파일 수정

```html
<div id="comment_area">
    <% comments.forEach(function(comment){ %>
        <div>
            <%=comment.content%>
            ( <a class='comment_delete' comment_id='<%=comment.id%>'>삭제</a> )
        </div>
    <% }); %>
</div>
```

js 파일 수정(append 구문에도 삭제 버튼 추가)

```javascript
$.ajax({
    url: '/admin/products/ajax_comment/insert',
    type: 'POST',
    data: $(this).serialize(),
})
.done(function(args) {
    if(args.message==="success"){
        $('#comment_area').append(
            '<div>' + args.content +
            " ( <a class='comment_delete' comment_id='"+ args.id +"'>삭제</a> ) </div>"
        );
        $('#commentForm textarea[name=content]').val("");
    }
})
.fail(function(args) {
    console.log(args);
});
```

## STEP 2. 댓글 삭제 기능 구현하기
라우팅과 js에서 삭제 기능을 구현한다.

js 파일 수정

```javascript
$(document).on('click' , '.comment_delete' , function(){
    if(confirm('삭제하시겠습니까?')){ //확인창 예 눌렀을 시만 진행
        var $self = $(this);
        $.ajax({
            url: '/admin/products/ajax_comment/delete',
            type: 'POST',
            data: { comment_id : $self.attr('comment_id') },
        })
        .done(function() {
            $self.parent().remove();
            alert("삭제가 완료되었습니다.");
        })
        .fail(function(args) {
            console.log(args);
        });
    }
});
```

/routes/admin delete 라우팅 추가

```javascript
router.post('/products/ajax_comment/delete', function(req, res){
    CommentsModel.remove({ id : req.body.comment_id } , function(err){
        res.json({ message : "success" });
    });
});
```
