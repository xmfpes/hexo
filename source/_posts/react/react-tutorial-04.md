---
title: 리액트 튜토리얼 - 04
date: 2017-10-17 16:20:24
tags: React
categories : 
- JavaScript
- React
---

## **리액트 튜토리얼 - 04**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### array 구조 수정하기

저번에 props 예제를 만들면서, movieTitles와 movieImages를 만들었는데,

Movie컴포넌트를 5번 호출한다거나 하는 비효율적인 부분을 효율적으로 고쳐보자.

우선 movies array를 만들어 title과 poster데이터를 담아보자.

```javascript
const movies = [
  {
    title : "Matrix",
    poster : "https://static.independent.co.uk/s3fs-public/thumbnails/image/2017/02/01/09/the-matrix.jpg"
  },
  {
    title : "OldBoy",
    poster : "https://resizing.flixster.com/Mq0_LntBHHLskoj122sGEFh-P2Y=/206x305/v1.bTsxMTE3NjY0NjtqOzE3NTQ0OzEyMDA7MTQwMDsyMTAw"
  },
  {
    title : "KingsMan",
    poster : "http://t1.daumcdn.net/liveboard/movie/dc38196dc7824fa083dd5a2b9ebedd8c.JPG"
  },
  {
    title : "Star Wars",
    poster : "https://lumiere-a.akamaihd.net/v1/images/lazada-starwars-1-1_741cd5d6.jpeg?region=0%2C0%2C1000%2C1000&width=320"
  }
]
```

이를 이 array를 map() 메소드를 활용해서 컴포넌트를 생성해보자

우선 map 메소드에 대해 간략히 설명하자면

### Array.prototype.map()
map() 메소드는 배열 내의 모든 요소 각각에 대하여  제공된 함수(callback)를 호출하고, 그 결과를 모아서, 새로운 배열을 반환하는 메소드이다.

문법은 아래와 같다.

arr.map(callback[, thisArg])

자세한 문법에 대해 보려면 오른쪽의 documentation을 참고하자.
[Documentation](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

이를 Arrow function을 이용해 간단히 표현해서, 각각이 array 값에 대한 컴포넌트를 생성해보자

```javascript
{
  movies.map(movie => {
    return <Movie title={movie.title} poster={movie.poster}/>
  })
}
```

이렇게 작업을 하고 나면 아래의 에러가 발생하는데

```bash
proxyConsole.js:54 Warning: Each child in an array or iterator should have a unique "key" prop.

Check the render method of `App`. See https://fb.me/react-warning-keys for more information.
    in Movie (at App.js:30)
    in App (at index.js:7)
```

내용은, array의 각 차일드들은 고유한 key prop를 가져야 한다는 내용의 에러다.

위의 코드를 통해 여러 Movie 컴포넌트가 생성되는데, 리액트에서는 여러 엘리멘트들을 가질 때

각각의 고유한 key 값을 부여해주어야 한다.

이 유니크한 키 값을 map의 index 값을 이용해 부여한다.

아래와 같이 수정하자.

```javascript
<div className="App">
  {movies.map((movie, index) => {
      return <Movie title={movie.title} poster={movie.poster} key={index}/>
  })}
</div>
```

위와같이 키 값을 부여하고, props에 대해서 validation을 부여해보자.

예를 들어 props title에 대해서, Number 같은 타입의 값을 받고 싶지 않거나, poster값이 Number나 Boolean 값이 되는걸 원치 않는다고 가정하자.

여기서 propTypes를 통해 타입을 지정해 줄 수 있다.

propTypes를 사용하기 위해서, 모듈 설치가 필요하다.

Install Prop-Types with npm:

```bash
npm install --save prop-types
```
Install Prop-Types with Yarn:

```bash
yarn add prop-types
```

설치한 이후에 props를 받는 Movie 컴포넌트에 코드를 추가하자
```javascript
import PropTypes from 'prop-types';
```

우선 최 상단에 모듈을 추가한 후

```javascript
class Movie extends Component{
    static propTypes = {
        title: PropTypes.string,
        poster: PropTypes.string
    }
```

위처럼 propTypes를 추가해주자

이제 위처럼 추가하고 나면, 원하는 string 값이 아닐 경우 아래처럼 브라우저의 콘솔에 에러가 나타나게 된다.
```
Warning: Failed prop type: Invalid prop `title` of type `number` supplied to `Movie`, expected `string`.
in Movie (at App.js:30)
in App (at index.js:7)
```

아래와 같이 isRequired를 추가하면, 해당 props가 없으면 브라우저의 콘솔에 해당 props가 없다고 에러가 발생하게 된다.

이를 통해 부모 컴포넌트에서 받는 props의 종류, 타입, 유무를 알 수 있게 된다.

```javascript
class Movie extends Component{
    static propTypes = {
        title: PropTypes.string.isRequired,
        poster: PropTypes.string.isRequired
    }
```

그리고 Movie의 자식 컴포넌트인 MoviePoster 컴포넌트에도 아래의 내용을 추가하자
```javascript
class MoviePoster extends Component{
    static propTypes = {
        poster : PropTypes.string.isRequired
    }
```
