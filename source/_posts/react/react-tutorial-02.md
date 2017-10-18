---
title: 리액트 튜토리얼 - 02
date: 2017-10-03 23:27:11
tags: React
categories : React
---

## **리액트 튜토리얼 - 02**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

우선 아래와 같은  영화 앱을 만들기 위해서 필요한 컴포넌트들을 먼저 구상하고 프로젝트를 시작한다.
![react](/images/react/movie.png)

- 무비 리스트 컴포넌트
- 영화 컴포넌트(카드 컴포넌트)
- 이미지 컴포넌트

우선 첫번째로, 무비 리스트 컴포넌트를 생성해야 한다.

무비 리스트들이 카드 레이아웃 형태로 들어가있는 전체 큰 틀을 말한다.

두번째는 각각의 영화를 카드 컴포넌트 형태로 만들어야 한다.
카드 컴포넌트는 영화에 대한 자세한 정보를 담고 있다.

마지막으로는 영화의 이미지를 담는 이미지 컴포넌트를 제작해야 한다.

세개의 컴포넌트를 하나에 만들수도 있지만, 관리를 위해서 제각각 다른 파일로 나누어 컴포넌트 제작할 것이다.
## JSX
리액트에서 작성하는 html 코드
![react](/images/react/jsx.png)
위의 이미지에서 볼수 있듯, 자바스크립트 코드 내에 html 코드가 들어있다.
이를 JSX라고 한다. 이는 리액트 컴포넌트를 만들기 위해 사용하는 언어

우선 제작하기 앞서 App.js에서 기존의 코드를 삭제한다.
```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">

      </div>
    );
  }
}

export default App;

```

이제 다 지워져 기존의 페이지는 보이지 않을것이다.

그리고 App.css도 아래의 코드만 남기고 다 삭제한다.
```css
.App {
  text-align: center;
}
```

그리고 다시 App.js로 가서 App 컴포넌트를 살펴보자.

컴포넌트는 각각의 다른 function과 method를 가지고 있다.

### 모든 컴포넌트는 render function을 가지고 있다. 이는 뭔가를 보여주고, 출력하는 기능을 한다. 이 컴포넌트가 나에게 보여주는것이 무엇인가, 하는것이 render의 기능이다.


## STEP 1. React의 구조 파악하기

/public/index.html 폴더를 보면
```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
    <title>React App</title>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
  </body>
</html>

```
위와 같은 HTML 코드가 존재한다. 우선 title을 Movie App으로 수정하고
아래를 보면 root id를 가진 div가 있을 것이다.

우선 /src/index.js로 가 보자.
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker();
```

위와 같은 코드가 있을 것이다.

index.js에서는 ReactDOM, React, App 컴포넌트를 import하고 있으며

ReactDOM에서 App 컴포넌트를 아이디가 root인 곳에 render하고 있는 것을 확인할 수 있다.

정리하자면, index.js에서 App 컴포넌트를 받아와서 컴포넌트를 렌더해서 index.html에 출력해주고 있는 것이다.

## STEP 2. React 컴포넌트 만들어보기
/src/Movie.js

/src/Movie.css

두개의 파일을 생성후 작성하자
Movie.js
```javascript
import React, { Component } from 'react';
import './Movie.css';

class Movie extends Component{
    render(){
        return(
            <h1>Hello this is a Movie</h1>
        )
    }
}

export default Movie;
```

App.js에서 Movie 컴포넌트를 import하고 사용할 것이다.

App.js
```javascript
import Movie from './Movie';
//Movie 컴포넌트 import
...

<div className="App">
  <Movie/>
  //Movie 컴포넌트 추가
</div>
```

과정을 간략히 설명하자면
Movie 컴포넌트 생성 -> render 작성 -> export
컴포넌트 import -> <Component /> 로 호출

컴포넌트는 아래처럼 여러번 호출할 수 있다.
```
<div className="App">
  <Movie/>
  <Movie/>
  <Movie/>
</div>
```

조금 더 나아가서, 컴포넌트 안에서 컴포넌트를 삽입해보자
Movie.js
```javascript
import React, { Component } from 'react';
import './Movie.css';

class Movie extends Component{
    render(){
        return(
            <div>
                <MoviePoster />
                <h1>Hello this is a Movie</h1>
            </div>
        )
    }
}

class MoviePoster extends Component{
    render(){
        return(
            <img src="http://t1.daumcdn.net/liveboard/movie/dc38196dc7824fa083dd5a2b9ebedd8c.JPG"/>
        )
    }
}

export default Movie;
```

이렇게 컴포넌트 안에 컴포넌트를 배치할 수도 있다.

여튼, 모든 컴포넌트는 render와 return을 통해 이런 방식으로 이루어 진다는 것을 기억하자.

오늘 학습에 사용된 코드는 깃허브 [리액트 레파지토리](https://github.com/xmfpes/react-tutorial/commit/c4214e6c5608fcd2da892e16907fd72a49a89996)에 커밋 로그로 남겨두었습니다.
