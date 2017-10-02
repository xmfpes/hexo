---
title: 리액트 튜토리얼 - 01
date: 2017-10-02 16:32:52
tags: React
categories : React
---

## **리액트 튜토리얼 - 01**

이 포스팅은 https://academy.nomadcoders.co의 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

## STEP 1. 개발 환경 세팅

시작 전에 앞서 아래의 세가지를 설치해야 한다.

- [npm](https://www.npmjs.com/)
- [Node.js](https://nodejs.org/en/)
- [Yarn](https://yarnpkg.com/lang/en/)

```bash
$ brew install yarn
$ yarn --version
```

Yarn은 Facebook에서 만든 새로운 자바스크립트 패키지 매니저이다.

version이 정상적으로 출력되면 설치가 완료된 것이다.

## STEP 2. 개요

React는 프레임워크가 아닌 라이브러리이다.

React를 통해 프론트엔드를 개발하는데, 데이터는 https://yts.ag/api의 api를 활용해
json 데이터를 받아서 개발한다.

![api](/images/react/api.png)

위와 같은 명세를 가지고 있고 이 데이터를 받아와 프론트엔드를 개발할 예정이다.

우리는 리액트 코드를 이용해 프론트엔드를 개발 할 것이고, 이를 JavaScript로 바꾸어주는 툴이 필요하다.
이를 트랜스파일러, 트랜스포머등으로 부르며 이번 강의에서는 '웹 팩'을 사용할 예정이다

### - 웹팩

![webpack](/images/react/webpack.png)

웹팩은 리액트 코드를 브라우저가 이해할 수 있는 코드로 변경해주는 역할을 한다.
최근 버전의 JavaScript는 es6, ecmascript6 버전인데, 이는 모든 브라우저가 이해할 수 없기 때문에 웹팩을 이용해서 브라우저가 이해할 수 있게 변경해준다.

리액트로 작업 하려면, 웹팩과 같은 모듈 번들러가 필요하다.
하지만 웹팩은 복잡하므로, 시간 절약을 위해 facebook에서 제공하는 create react app을 이용해 웹팩 설정들을 생략하고 바로 진행할 것이다.

## STEP 3. 설치 및 진행

https://github.com/facebookincubator/create-react-app

위의 문서를 참고해 셋팅을 진행하자

```bash
npm install -g create-react-app

create-react-app movie_app
cd movie_app
```

패키지 설치를 yarn으로 진행하였기 때문에

기존의 npm start로 실행되지 않는다.
```bash
$ cd movie_app
$ yarn start
```

정상적으로 서버가 실행되었다면 아래와 같은 화면을 볼 수 있다.

![webpack](/images/react/reactmain.png)

create-react-app에서는 코드의 수정이 생기면 자동으로 컴파일이 되고
수정 사항이 바로 새로고침되어 반영된다.

메인 페이지에 뜨는 타이틀을 변경하고 싶으면
App.js의 아래 텍스트를 수정하면 바로 적용이 된다.
```html
<h1 className="App-title">Welcome to React</h1>
```
