---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 04(Template Engine(ejs) 공통 파일 추가하기)
date: 2017-09-19 18:03:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
categories: Node.js
---

## **Node.js Template Engine 추가(ejs)**

![node](/images/node.png)

템플릿 엔진을 추가하고 나서 header나 footer등 전체 페이지에서 공통적으로
들어가는 부분을 정리한다.

## STEP 1. footer, header 공통 파일 추가

/views/includes 폴더 생성

includes 하부에 footer.ejs, header.ejs를 추가한다.

header.ejs

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Node.js 예제</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
</head>
<body>
    <div class="container" style="padding-top:100px;">
```

footer.ejs
```html
 </div>    
</body>
</html>
```

## STEP 2. footer, header 공통 파일 inlcude 및 products.ejs 파일 수정
/views/admin/products.ejs 파일 수정
```html
<% include ../includes/header.ejs %>
 
    <table class="table table-bordered table-hover">
        <tr>
            <th>제목</th>
            <th>작성일</th>
            <th>삭제</th>
        </tr>
        <tr>
            <td>제품 이름</td>
            <td>
                2017-07-15
            </td>
            <td>
                <a href="#" class="btn btn-danger">삭제</a>
            </td>
        </tr>
    </table>
 
    <a href="/admin/products/write" class="btn btn-default">작성하기</a>
 
<% include ../includes/footer.ejs %>
```

코드 작성 후, 아래의 페이지에서 header와 footer가 정상적으로 추가되었는지, 정상적으로 페이지가 작동하는지 체크한다.

[localhost:3000/admin/products](localhost:3000/admin/products)

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/fa8642a684215a1bfa0704283a30a7b2a98bf9b8)에 커밋 로그로 남겨두었습니다.