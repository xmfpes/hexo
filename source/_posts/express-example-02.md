---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 02(Mongoose ODM 연동하기)
date: 2017-09-19 18:01:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: Node.js
---

## **Node.js Mongoose ODM 사용**

![express](/images/node.png)

Mongoose는 MongoDB 기반 ODM(Object Data Mapping) Node.js 라이브러리

Mongoose는 MongoDB 객체 모델링 도구, ODM은 데이터베이스와 객체지향 프로그래밍 언어 사이 호환되지 않는 데이터를 변환하는 프로그래밍 기법, 즉 MongoDB 에 있는 데이터를 Application에서 JavaScript 객체로 사용 할 수 있도록 해줌

## STEP 1. Mongoose 패키지 / autoIncrement를 위한 패키지 다운로드
```bash
    $ npm install --save mongodb mongoose
    $ npm install --save mongoose-auto-increment
```
## STEP 2. app.js에 Db Connection 코드 작성

DB 연결 부분을 주의하자, 이후 admin routes에서 Model을 불러오는데 MongoDB가 먼저 연결되지 않는다면 initialize과정에서 에러가 발생한다.

admin 모듈을 가져오는 코드 위에 db connect 부분을 넣도록 한다.

```javascript
var mongoose = require('mongoose');
//auto-increment를 위한 패키지
var autoIncrement = require('mongoose-auto-increment');

var db = mongoose.connection;
db.on('error', console.error);
db.once('open', function(){
    console.log("mongo db Connection");
});

var connect = mongoose.connect('mongodb://127.0.0.1:27017/myDbName', { useMongoClient: true });
autoIncrement.initialize(connect);
```

## STEP 3. Model 작성

최상위 디렉토리에서 models 폴더 생성
models 하부에 ProductsModel.js 생성

```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var autoIncrement = require('mongoose-auto-increment');

//생성될 필드명을 정한다.
var ProductsSchema = new Schema({
    name : String, //제품명
    price : Number, //가격
    description : String, //설명
    created_at : { //작성일
        type : Date,
        default : Date.now()
    }
});

// 1씩 증가하는 primary Key를 만든다
// model : 생성할 document 이름
// field : primary key , startAt : 1부터 시작
ProductsSchema.plugin( autoIncrement.plugin , { model : 'products' , field : 'id' , startAt : 1 });
module.exports = mongoose.model('products', ProductsSchema);
```

DB Connection 코드와 DB 모델을 먼저 추가해 주었다. 이후 Template Engine(Ejs, HandleBars, Pug)을 추가하고 View파일을 만든 뒤 DB 테스트를 진행한다.