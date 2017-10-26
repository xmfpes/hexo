---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 14(csurf를 이용한 csrf 공격 방어)
date: 2017-09-20 21:19:47
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
- Csrf
- Csurf
categories: 
- JavaScript
- Node.js
---
## **CSRF 방어 적용하기**

![node](/images/node.png)

#### CSRF(Cross-Site Request forgery) 공격

웹사이트 취약점 공격의 하나로, 사용자가 자신의 의지와는 무관하게 공격자가 의도한 행위(수정, 삭제, 등록 등)를 특정 웹사이트에 요청하게 하는 공격을 말한다.

CSRF 공격을 막기 위해서 서버에서 토큰을 발행해 클라이언트로 보내주고
클라이언트에서 글 작성 시 서버에서 생성한 토큰과 맞는지 값을 비교해준 후에 토큰이 일치하는 경우에만 글을 작성하도록 구현한다.

## STEP 1. 모듈 다운로드

csurf csrf 관련 설정 모듈

cookie-parser 웹 사이트 쿠키 사용을 위한 모듈

```bash
  $ npm install --save csurf cookie-parser
```


## STEP 2. Cookie 사용 가능 설정 추가

express에서 쿠키 사용 설정을 추가한다.

/app.js
```javascript
var cookieParser = require('cookie-parser');

app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
//bodyParser 아랫부분에 cookieParser 추가
```


##STEP 3. CSRF 설정
/routes/admin.js에 csrf 설정 추가

true로 준 뒤에 sessionKey 옵션을 사용할 수 있다고 하는데, 뭔지 잘 모르겠다.
자세한 건 아래의 csurf Github 문서 참고
[https://github.com/expressjs/csurf]

```javascript
// csrf 셋팅
var csrf = require('csurf');
var csrfProtection = csrf({ cookie: true });
```

각각의 write 라우팅에 get, post 모두에 csrfProtection 미들웨어 추가
get에서는 토큰을 생성해 보내주고 post에서는 미들웨어를 추가해 주기만 하면 알아서 토큰을 비교한다.

뷰로 내보내줄 csrfToken과 체크할 csrfToken 설정 추가
```javascript
router.get('/products/write', csrfProtection, function(req,res){
    //edit에서도 같은 form을 사용하므로 빈 변수( product )를 넣어서 에러를 피해준다
    res.render( 'admin/form' , { product : "" , csrfToken : req.csrfToken() } );
});

router.post('/products/write', csrfProtection, function (req, res) {
    var product = new ProductsModel({
        name: req.body.name,
        price: req.body.price,
        description: req.body.description
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
/views/form.ejs에 csrf 토큰 hidden 태그로 추가
```html
...
<form action="" method="post">
        <input type="hidden" name="_csrf" value="<%=csrfToken%>" />
...
```

/rotues/admin.js 의 수정 페이지에서도 csrfProtection 미들웨어 적용
```javascript
router.get('/products/edit/:id' , csrfProtection, function(req, res){
    //기존에 폼에 value안에 값을 셋팅하기 위해 만든다.
    ProductsModel.findOne({ id : req.params.id } , function(err, product){
        res.render('admin/form', { product : product, csrfToken : req.csrfToken() });
    });
});

router.post('/products/edit/:id', csrfProtection, function(req, res){
```

오늘 학습에 사용된 코드는 깃허브 [레파지토리](https://github.com/xmfpes/node-project/commit/cbd586df31015868178733f47da07a2ec87ae979)에 커밋 로그로 남겨두었습니다.