---
title: 리액트 튜토리얼 - 03
date: 2017-10-17 16:20:21
tags: React
categories : 
- JavaScript
- React
---

## **리액트 튜토리얼 - 03**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### react의 주요 컨셉 두 가지

- state

- props

오늘의 강의에서는 props에 대해 다룬다.

메인 컴포넌트 app은 영화 데이터들을 가져올 것이고

그 영화 데이터는 카드 형태로 각각 리스트에 작성될 것이다.
이는 즉 메인 컴포넌트는 각각 칠드런 컴포넌트 각각에 정보를 전달한다는 이야기이다.

앞서 설계한 컴포넌트들은 아래와 같은데

- 무비 리스트 컴포넌트(메인 컴포넌트)
- 영화 컴포넌트(카드 컴포넌트)
- 이미지 컴포넌트

메인(무비 리스트)컴포넌트에서 자식 컴포넌트인 영화 컴포넌트로 데이터를 주기 위해

앞서 나온 개념인 props를 이용한다.

App.js 컴포넌트에서 자식 컴포넌트인 Movie로 위의 movies의 movie title을 넘겨준다고 생각해보자.
![moives](/images/react/movies.png)

위의 코드에서 무비 컴포넌트로 title이라는 데이터를 전달해 주려면 아래와 같이 작성하면 된다.

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Movie from './Movie';

const movies = [
  "Matrix",
  "OldBoy",
  "KingsMan",
  "Star Wars"
]
class App extends Component {
  render() {
    return (
      <div className="App">
        <Movie title={movies[0]}/>
        <Movie title={movies[1]}/>
        <Movie title={movies[2]}/>
        <Movie title={movies[3]}/>
      </div>
    );
  }
}

export default App;

```

이를 Movies 컴포넌트에서 어떻게 활용하냐면, 아래와 같이 활용할 수 있다.
```javascript
class Movie extends Component{
    render(){
        console.log(this.props);
        return(
            <div>
                <MoviePoster />
                <h1>Hello this is a Movie</h1>
            </div>
        )
    }
}
```

console.log를 통해 this.props의 내용을 볼 수 있는데

페이지에서 아래와 같이 데이터를 볼 수 있어야 한다.

![props](/images/react/props.png)

아래의 간단한 코드를 통해 props의 title을 가지고 와서 각 영화 제목을 출력해 줄 수 있다.
```javascript
class Movie extends Component{
    render(){
        return(
            <div>
                <MoviePoster />
                <h1>{this.props.title}</h1>
            </div>
        )
    }
}
```

#### 위처럼 리액트에서는 이용해 부모 컴포넌트에서 자식 컴포넌트로 데이터를 줄 수 있다.

### props 실습하기

movies를 조금 바꾸고, 이번에는 이전에 출력한 각각의 이미지를 메인 컴포넌트에서 넘겨서 구현해보자.

App.js에서 movieTitles, movieImages를 자식 컴포넌트로 보내 출력해보자.
```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import Movie from './Movie';

const movieTitles = [
  "Matrix",
  "OldBoy",
  "KingsMan",
  "Star Wars"
]

const movieImages = [
  "https://static.independent.co.uk/s3fs-public/thumbnails/image/2017/02/01/09/the-matrix.jpg",
  "https://resizing.flixster.com/Mq0_LntBHHLskoj122sGEFh-P2Y=/206x305/v1.bTsxMTE3NjY0NjtqOzE3NTQ0OzEyMDA7MTQwMDsyMTAw",
  "http://t1.daumcdn.net/liveboard/movie/dc38196dc7824fa083dd5a2b9ebedd8c.JPG",
  "https://lumiere-a.akamaihd.net/v1/images/lazada-starwars-1-1_741cd5d6.jpeg?region=0%2C0%2C1000%2C1000&width=320"
]
class App extends Component {
  render() {
    return (
      <div className="App">
        <Movie title={movieTitles[0]} poster={movieImages[0]}/>
        <Movie title={movieTitles[1]} poster={movieImages[1]}/>
        <Movie title={movieTitles[2]} poster={movieImages[2]}/>
        <Movie title={movieTitles[3]} poster={movieImages[3]}/>
      </div>
    );
  }
}

export default App;

```

Movie.js

무비 컴포넌트에서 title와 poster를 받고 무비에서 title을 출력하고
무비 포스터 컴포넌트로 poster 데이터를 전송해서 img src를 통해 출력해 주었다.

```javascript
import React, { Component } from 'react';
import './Movie.css';

class Movie extends Component{
    render(){
        return(
            <div>
                <MoviePoster poster={this.props.poster} />
                <h1>{this.props.title}</h1>
            </div>
        )
    }
}

class MoviePoster extends Component{
    render(){
        return(
          <img src={this.props.poster} alt="Movie Poster"/>
        )
    }
}

export default Movie;
```

정리하자면 App 컴포넌트 ->(title 정보) Movie 컴포넌트

Movie 컴포넌트에서 title 엑세스는 ({this.props.title})이다.

{}를 빼먹는다면, 제대로 실행되지 않는다 이는 JSX의 문법에서 명령을 실행시키려면 괄호를 주어야 하기 때문이다. 이는 handlebars와 유사하다고 볼 수 있겠다.


타이틀, 영화 포스터 정보를 메인 컴포넌트에서 가지고 이를 각각 컴포넌트에 props를 이용해 정보를 출력해 줄 수 있다.

한개의 데이터 소스를 메인 컴포넌트에서 가지고, 각 컴포넌트별로 출력만 하면 되는 구조를 가지고 있다.
