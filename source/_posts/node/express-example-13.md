---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 13(Validation을 이용한 데이터 검증하기)
date: 2017-09-20 21:17:47
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
- Validation
categories: Node.js
thumbnail: /images/node.png
---

## **Validation 추가하기**

Validation을 추가해서 데이터를 넣을 때 체크하도록 기능을 추가한다.

## STEP 1. ProductsModel에서 validation 체크 코드 추가

제목에 대한 validation을 모델에 정의한다.
```javascript
//생성될 필드명을 정한다.
var ProductsSchema = new Schema({
    name : { //제품명
        type : String,
        required: [true, '제목은 입력해주세요']
    },
    price : Number, //가격
    description : String, //설명
    created_at : { //작성일
        type : Date,
        default : Date.now()
    }
});
```

## STEP 2. routes에서 validation 체크 코드 추가

router/admin.js

POST /admin/products/write

에서 validation 체크하는 코드를 추가한다.

```javascript
router.post('/products/write', function(req,res){
    var product = new ProductsModel({
        name : req.body.name,
        price : req.body.price,
        description : req.body.description,
    });
    var validationError = product.validateSync();
    if(validationError){
        res.send(validationError);
    }else{
        product.save(function(err){
            res.redirect('/admin/products');
        });
    }
});

```

위처럼 validation을 추가하고, 제품 등록 중에 제목을 입력하지 않으면
아래와 같은 validationError를 발생시킨다.

![validationError](/images/validatorError.png)
