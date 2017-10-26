---
title: 리액트 튜토리얼 - 07
date: 2017-10-22 10:30:15
tags: React
categories : 
- JavaScript
- React
---

## **리액트 튜토리얼 - 07**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### React에서의 Ajax

Ajax(Asynchronous JavcaScript and XML) 최근 XML 형태는 잘 사용하지 않고, JSON(JavaScript Object Notation) 형태를 많이 사용한다

### Ajax를 React에 적용하는 방법

Fetch Request를 이용해서 간단하게 수행할 수 있다.

일단, 데이터를 가져오기 위해 아래의 사이트에 접속한다.

[영화 api](https://yts.ag/api#list_movies)

https://yts.ag/api/v2/list_movies.json

위의 링크를 열면, 자바스크립트 오브젝트가 웹에 표시된다.

필터를 통해 정렬하거나 할 수 있는데

이번 예제에서는 평점을 통해 정렬하는 필터를 설정하고 데이터를 가져온다.

https://yts.ag/api/v2/list_movies.json?sort_by=rating

이제 위의 데이터를 가지고 적용할 것이기 때문에
기존의  componentDidMount()에 있던 영화 데이터들을 삭제하자.

```javascript
componentDidMount(){
   fetch('https://yts.ag/api/v2/list_movies.json?sort_by=rating')
 }
```

위와 같이 호출하면, Ajax를 통해 데이터를 가져 올 수 있다.

만약 위에서 console.log를 통해 출력하면?
```
Promise
__proto__ : Promise
[[PromiseStatus]] : "resolved"
[[PromiseValue]] : Response
```

위와 같이 Promise 형태로 출력된다.

### Promise

새로운 자바스크립트 컨셉


Promise는 JavaScript Es6 표준 내장 객체.

Promise 객체는 비동기 계산을 위해 사용하고 Promise는 아직은 아니지만 나중에 완료될 것으로 기대되는 연산을 표현한다.
Promise를 이용하면 콜백 헬을 개선할수 있다.

```javascript
componentDidMount(){
   fetch('https://yts.ag/api/v2/list_movies.json?sort_by=rating')
   .then(response => console.log(response))
   .catch(err => console.log(err))
 }
```

fetch의 결과가 성공적으로 수행되면 then의 내용을 실행하고, 에러가 발생하면 catch로 분기한다. then은 하나의 오브젝트만 파라미터로 넘길 수 있다.

여러개의 fetch를 하면, 각각 다 비동기로 실행되며 결과가 완료되면 then 구문이 실행되게 된다.

정상적으로 완료되면 아래와 같은 코드를 볼 수 있다.
![response](/images/react/promise_response.png)

바디 정보를 보면 아래와 같이

body : ReadableStream 이라고 나오는데 이는 바이트(01010)으로 이루어져있다는 뜻이고 이후 JSON 데이터 형태로 바꾸어 주어야 한다.

fetch, then, json 형태로 변경 이후 json을 console.log로 출력해보자

```javascript
componentDidMount(){
  fetch('https://yts.ag/api/v2/list_movies.json?sort_by=rating')
  .then(response => response.json())
  .then(json => console.log(json))
  .catch(err => console.log(err))
}
```

json 형태로 정상적으로 출력되는 것을 확인할 수 있다.

### call back Hell 개선하기

위의 코드는 componentDidMount()안에 코드가 길게 있고, 여러 기능이 늘어나면 .then() 안에 then으로 이어지면서 callback hell에 빠지게 된다. 이를 개선하기 위해 분리해보자.

일단 componentDidMount의 코드를 최소화하자.
```javascript
componentDidMount(){
 this._getMovies();
}
```

그리고 Asynchronous function인 getMovies를 만들자.

```javascript
_getMovies = async () => {
   const movies = await this._callApi()
   this.setState({
     movies
   })
 }
```

그리고 _callApit()를 정의하자.

```javascript
_callApi = () => {
   return fetch('https://yts.ag/api/v2/list_movies.json?sort_by=rating')
   .then(response => response.json())
   .then(json => json.data.movies)
   .catch(err => console.log(err))
 }
```

getMovies 함수는 비동기이기 때문에 _callApi가 완료 될 때 까지 await 하다가 완료되면 콜백으로 실행되게 된다.

만약 아래의 형태라면 console.log가 먼저 실행되게 된다.
componentDidMount(){
 this._getMovies();
 console.log("haha");
}

위처럼 모든 코드를 작성하고 나면, poster를 찾을 수 없다고 나오는데 기존에 movie 오브젝트가 다르기 때문이다.

title은 가지고 있지만, poster가 아닌 large_cover_image를 가지고 있기 때문에 값을 변경해주어야 한다.


```javascript
_renderMovies = () => {
  const movies = this.state.movies.map((movie, index) => {
    return <Movie title={movie.title} poster={movie.large_cover_imageter} key={index}/>
  })
  return movies;
}
```

위처럼 poster를 large_cover_image로 변경하면 정상적으로 포스터가 출력되게 된다.

그리고 영화 각각에는 id 값이 있기 때문에 key 값을 index로 사용하지 말고 영화의 아이디 값을 사용하자.

```javascript
_renderMovies = () => {
  const movies = this.state.movies.map((movie) => {
    return <Movie title={movie.title} poster={movie.large_cover_image} key={movie.id}/>
  })
  return movies;
}
```
