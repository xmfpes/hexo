---
title: 리액트 튜토리얼 - 06
date: 2017-10-20 21:30:15
tags: React
categories : 
- JavaScript
- React
---

## **리액트 튜토리얼 - 06**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### React Component State

State는 리액트 컴포넌트 안에 있는 오브젝트이다.

State가 바뀔 때 마다 render가 발생한다.

이를 테스트 해 주기 위해서, state를 생성하고, 값을 바꾼 후 render 되는 것을 테스트한다.


App.js
```javascript
  state = {
    greeting: 'Hello'
  }

componentDidMount(){
  setTimeout(() => {
    this.setState({
      greeting: "Hello again"
    })
  }, 5000)
}

<div className="App">
  {this.state.greeting}
  {movies.map((movie, index) => {
      return <Movie title={movie.title} poster={movie.poster} key={index}/>
  })}
</div>

```

위와같이 적고, 저장하면 5초 후 state가 변경 되면서 페이지가 새로고침 되고
this.state.greeting 값이 변경되어 출력되게 된다.

### React State 연습해보기

기존의 Movies 정보를 state 안으로 옮겨보자.

```javascript
state = {
  greeting: 'hello',
  movies: [
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
}

....

componentDidMount(){
  setTimeout(() => {
    this.setState({
      movies : [
        ...this.state.movies,
        {
          title: 'Star kings Man',
          poster: 'http://t1.daumcdn.net/liveboard/movie/dc38196dc7824fa083dd5a2b9ebedd8c.JPG'
        }
      ]
    })
  }, 5000)
}

....

{this.state.movies.map((movie, index) => {
    return <Movie title={movie.title} poster={movie.poster} key={index}/>
})}
```

...this.state.movies, 의 의미는

기존의 영화 리스트를 그대로 유지하고, 하나의 영화를 추가하는 것을 의미한다.

정상적으로 코드를 작성하고 실행하면, 5초 후에 새로운 영화가 아래에 추가되어 입력된다.

순서를 바꾸면 위에 출력된다.


### React Loading States 생성하기

영화 정보를 5초 후 받아오게 설정하고, 영화정보를 받아오기 전에는 Loading을 출력하고, 정보를 받아온 뒤에 출력하는 코드를 State를 이용해 작성해보자

```javascript
componentDidMount(){
   setTimeout(() => {
     this.setState({
       movies : [
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
         },
         {
           title: 'Star kings Man',
           poster: 'http://t1.daumcdn.net/liveboard/movie/dc38196dc7824fa083dd5a2b9ebedd8c.JPG'
         }
       ]
     })
   }, 5000)
 }

.....

_renderMovies = () => {
   const movies = this.state.movies.map((movie, index) => {
     return <Movie title={movie.title} poster={movie.poster} key={index}/>
   })
   return movies;
 }

.....

render() {
  console.log("render");
  return (
    <div className="App">
      {this.state.movies ? this._renderMovies() : 'Now Loading...'}
    </div>
  );
}
```

위와 같이 코드를 작성하면, this.state.movies가 있을 때 무비 리스트를 출력하고, 없으면 Now Loading을 출력하게 된다.

생성한 함수에서 _ 를 앞에 붙여주는 이유는, 리액트 자체 함수 등이 많기 때문에 함수를 구분하기 위해 사용한다.

## Stateless Component

state가 없는 컴포넌트도 존재한다.

#### Smart Component(Class 형태)

State를 가지는 컴포넌트

#### Dump Component(Function 형태)

State를 가지지 않는 멍청한 컴포넌트(Props 만을 가진다.)

기존의 Movie 포스터는, 딱히 State를 가질 필요가 없고 단순 출력만을 하기 때문에 state가 필요 없다.

어떤 컴포넌트는 Return만을 수행하는데, 이 컴포넌트는 state, componentWillMount, update 등이 필요 없고 props 만 있으면 된다.

/Movie.js

```javascript
// class MoviePoster extends Component{
//     static propTypes = {
//         poster : PropTypes.string.isRequired
//     }
//     render(){
//         return(
//             <img src={this.props.poster} alt="Movie Poster"/>
//         )
//     }
// }
function MoviePoster({poster}){
    return (
        <img src={poster} alt="Movie Poster"/>
    )
}
```

위의 Class 컴포넌트 형태의 주식 부분을 제거하고, 아래의 function 형태의 컴포넌트 형태로 바꾼다.

#### validation을 위한 PropTypes 추가하기

```javascript
MoviePoster.propTypes = {
    poster: PropTypes.string.isRequired
}
```


이제 Movie 컴포넌트도 function 형태의 클래스로 변경해보자.

그 전에 위의 임포트 한 부분을 수정한다.

```javascript
//import React, { Component } from 'react';
import React from 'react';
```

컴포넌트를 사용하지 않으므로 변경

### functional 컴포넌트에서는 this를 삭제해주어야 한다. 클래스가 아니니까

기존의 this.props.title 에서 this.props를 삭제하자.

```javascript
function Movie({title, poster}){
    return (
        <div>
            <MoviePoster poster={poster} />
            <h1>{title}</h1>
        </div>
    )
}

Movie.propTypes = {
    title: PropTypes.string.isRequired,
    poster: PropTypes.string.isRequired
}
```

위와 같이 단순 리턴을 하는 컴포넌트에는, 단순한 functional 컴포넌트를 사용해도 무방하다.
