---
title: 리액트 튜토리얼 - 05
date: 2017-10-18 16:20:29
tags: React
categories : 
- JavaScript
- React
---

## **리액트 튜토리얼 - 05**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### React Component Lifecycle

React Component Lifecycle
[Documentation](https://reactjs.org/docs/react-component.html#the-component-lifecycle)


react에서 render()를 할 때 다음과 같은 과정이 무조건 수행된다.

### render 수행 절차
#### componentWillMount() -> render() -> componentDidMount() 사이클로 수행

컴포넌트가 존재하기 시작하면, React에서는 Willmount -> render -> didMount의 순서로 실행한다.

아래처럼 console.log를 찍어보면
```javascript
class App extends Component {
  componentWillMount(){
    console.log("will mount");
  }

  componentDidMount(){
    console.log("did mount");
  }
  render() {
    console.log("render");
    return (
      <div className="App">
        {movies.map((movie, index) => {
            return <Movie title={movie.title} poster={movie.poster} key={index}/>
        })}
      </div>
    );
  }
}
```

브라우저상에서 아래와 같이 componentWillMount() -> render() -> componentDidMount() 사이클로 수행되는 것을 알 수 있다.
```
Download the React DevTools for a better development experience: https://fb.me/react-devtools
App.js:27 will mount
App.js:34 render
App.js:31 did mount
```

### Updating 수행 절차

componentWillReceiveProps() ->
shouldComponentUpdate()(boolean)

componentWillUpdate()(true면 수행) -> render() -> componentDidUpdate()


위의 5가지 단계로 수행되는데, 각각 단계에 대해 간략히 설명하자면

#### componentWillReceiveProps()

새로운 prop를 받는 단계

#### shouldComponentUpdate()

old props와 new props를 비교해서 이전과 다르면 업데이트 == true가 되고 그러면 다음 단계인  componentWillUpdate()가 수행된다.

componentWillUpdate()에서 업데이트가 수행되고, render를 통해 적용, 마지막 componentDidUpdate() 순으로 실행되게 된다.
## **리액트 튜토리얼 - 05**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### React Component Lifecycle

React Component Lifecycle
[Documentation](https://reactjs.org/docs/react-component.html#the-component-lifecycle)


react에서 render()를 할 때 다음과 같은 과정이 무조건 수행된다.

### render 수행 절차
#### componentWillMount() -> render() -> componentDidMount() 사이클로 수행

컴포넌트가 존재하기 시작하면, React에서는 Willmount -> render -> didMount의 순서로 실행한다.

아래처럼 console.log를 찍어보면
```javascript
class App extends Component {
  componentWillMount(){
    console.log("will mount");
  }

  componentDidMount(){
    console.log("did mount");
  }
  render() {
    console.log("render");
    return (
      <div className="App">
        {movies.map((movie, index) => {
            return <Movie title={movie.title} poster={movie.poster} key={index}/>
        })}
      </div>
    );
  }
}
```

브라우저상에서 아래와 같이 componentWillMount() -> render() -> componentDidMount() 사이클로 수행되는 것을 알 수 있다.
```
Download the React DevTools for a better development experience: https://fb.me/react-devtools
App.js:27 will mount
App.js:34 render
App.js:31 did mount
```

### Updating 수행 절차

componentWillReceiveProps() ->
shouldComponentUpdate()(boolean)

componentWillUpdate()(true면 수행) -> render() -> componentDidUpdate()


위의 5가지 단계로 수행되는데, 각각 단계에 대해 간략히 설명하자면

#### componentWillReceiveProps()

새로운 prop를 받는 단계

#### shouldComponentUpdate()

old props와 new props를 비교해서 이전과 다르면 업데이트 == true가 되고 그러면 다음 단계인  componentWillUpdate()가 수행된다.

componentWillUpdate()에서 업데이트가 수행되고, render를 통해 적용, 마지막 componentDidUpdate() 순으로 실행되게 된다.
