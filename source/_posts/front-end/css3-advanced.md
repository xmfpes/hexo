---
title: CSS3 고급 기능들
date: 2017-11-15 10:35:50
tags:
- CSS
categories:
- 기타
- CSS
---

![](/images/css.jpg)

### Transitions

CSS 속성을 변경할 떄 애니메이션 속도를 조절하는 방법을 제공하는 속성, 즉시 영향을 미치지 않고 일정 기간에 걸쳐 일어나도록 조정한다. 어떤 state에서 다른 state로 넘어갈때의 효과를 조절한다.

```css
.box{
  background-color: green;
  transition:background-color 5s ease-in-out;
}
.box:hover{
  background-color: blue;
}
```

위와 같이 transition 설정을 해 두면, background-color 변경 시 5초동안 천천히 변경되는 효과가 적용된다.

만약 background-color뿐만 아니라 다른 효과도 천천히 적용하고 싶다면

```css
.box{
  background-color: green;
  color:white;
  transition:all 5s ease-in-out;
}
.box:hover{
  background-color: blue;
  color:blue;
}
```

위와같이 all로 설정하면 모든 설정값 변경에 대해 transition이 적용된다.



### TransFormations

컴포넌트를 회전하거나, 변형을 주거나 할 수 있다.

transition 옵션과 결합해서, 부드럽게 효과를 줄 수 있다.

```css
.box{
  width: 500px;
  height: 500px;
  background: red;
  transition: ransform .5s ease-in-out;
}
.box:hover{
   transform: rotate(20deg);
}
```

[TransFormations 문서](https://developer.mozilla.org/ko/docs/Web/API/Canvas_API/Tutorial/Transformations) Mozilla의 문서에 transform과 관련된 여러 라이브러리들이 있다. 참고해서 사용하자.



### Animation

위에서 적용한 트랜스포메이션, 트랜지션 효과를 hover할 필요없이 계속 발생시키고 싶은 경우라면 Animation을 생성해서 적용하면 된다.

우선, 애니메이션 이름을 정해서 생성해주어야 한다. @keyframes를 통해 KeyFrame을 생성하자 

keyframes는 css로 하여금 애니메이션을 생성했다는 것을 알려주는 역할을 한다.

@keyframes는 from, to의 2가지 스텝으로 진행된다.

```css
.box{
  width: 100px;
  height: 100px;
  background: red;
  animation: 1.5s scaleAndRotateSquare ease-in-out;
}
@keyframes scaleAndRotateSquare {
  from{
    transform:none;
  }
  to{
    transform: rotate(1turn) scale(.5, .5);
  }
}
```

위와같이 실행하면, animation이 1.5초동안 한번만 실행될 것이다.

from에서 none 상태를 가지다가, to로 가면서 rotate, scale을 1.5초동안 수행한다.

이걸 만약 계속 반복하고 싶다면?

```css
animation: 1.5s scaleAndRotateSquare infinite ease-in-out;
```

이렇게, infinite 옵션을 줄 수 있다.

그리고 0%, 50%, 100% 프레임 스테이지 별로 옵션을 줄 수도 있다.

```css
.box{
  width: 100px;
  height: 100px;
  background: red;
  animation: 1.5s scaleAndRotateSquare infinite ease-in-out;
}
@keyframes scaleAndRotateSquare {
  0%{
    transform:none;
  }
  50%{
    transform:rotate(1turn) scale(.5, .5);
    background-color:blue;
  }
  100%{
    transform:none;
    background-color:red;
  }
}
```

위처럼 옵션을 주면, 회전하면서 줄어들었면서 파란색으로 변했다가, 다시 원래대로 가면서 빨간색으로 변하는 애니메이션이 실행되게 된다. 스테이지가 2개면 from-to를 사용하면 된다.



### Media Queries

유저가 모바일 환경 인 경우 320-640 크기를 가진다고 가정하고, 그때의 화면 구성을 다르게 해 줄 수 있다.

```css
<style>
body{
  background-color:green;
}
@media screen and (min-width:320px) and (max-width:640px){
  body{
    background-color:red;
  }
}
</style>
```

기본적으로 페이지를 보면 green으로 보이다가, 페이지의 크기가 320-640사이라면 빨간 배경으로 보이게 된다.



