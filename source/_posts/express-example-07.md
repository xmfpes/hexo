---
title: Express 프레임워크를 이용한 웹 페이지 제작 - 07(Mongoose Virtual attributes 추가하기)
date: 2017-09-19 18:06:00
tags: 
- Node.js
- JavaScript
- JQuery
- Express
- Mongoose
categories: Node.js
---

## **Node.js Mongoose Virtual attributes 추가하기**

![express](/images/express.jpg)

Mongoose를 이용해 정의한 모델에 Virtual attribute를 추가한다.
virtual attributes를 이용해 collection에 정의 되지 않은 filed 이지만 정의된 field 처럼 사용할 수 있다.

## STEP 1. 만들어둔 Model에 Virtual attributes 추가하기

virtual 변수는 호출되면 실행하는 함수의 개념
Object create 의 get과 set과 비슷하다.
set은 변수의 값을 바꾸거나 셋팅하면 호출
get은 getDate변수를 호출하는 순간 날짜의 년,월,일이 리턴된다.

/models/ProductsModel.js에 아래의 가상변수 내용 코드 추가하기
```javascript
ProductsSchema.virtual('getDate').get(function(){
    var date = new Date(this.created_at);
    return {
        year : date.getFullYear(),
        month : date.getMonth()+1,
        day : date.getDate()
    };
});
```
## STEP 2. view 페이지에 년,월,일 출력 적용하기
/views/products.ejs에 년, 월, 일 출력 부분 변경
```html
<td><%=product.created_at%></td>
위의 코드를 아래의 코드로 변경한다.
<td>
    <%=product.getDate.year%> -
    <%=product.getDate.month%> -
    <%=product.getDate.day%>
</td>

```