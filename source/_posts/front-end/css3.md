---
title: CSS3 기초 정리
date: 2017-11-15 10:35:50
tags:
- CSS
categories:
- 기타
- CSS
---

![](/images/css.jpg)

### CSS Selectors and Pseudo Selectors

- pseudo selector

  가상 셀렉터는 element 상태를 지정하는 selector에 추가된 키워드이다.

  태그 이름, class, id를 쓰지 않고 선택하기 위한 셀렉터이다.

  [가상 클래스 Mozlilla 문서](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-classes)

```html
<input type="submit">
```

위와 같은 엘리먼트를 선택하려고 한다고 치자.

```css
input[type="submit"]{
  background-color: red;
}
```

위와같은 pseudo selector를 이용해 엘리먼트를 선택해 줄 수 있다.



아래와 같이 10개의 박스가 있을때, 10번째, 1번째, 2,4,6,8 등 박스의 색만 변경해 주고자 하는 경우

```html
<div class="box">1</div>
<div class="box">2</div>
<div class="box">3</div>
<div class="box">4</div>
<div class="box">5</div>
<div class="box">6</div>
<div class="box">7</div>
<div class="box">8</div>
<div class="box">9</div>
<div class="box">10</div>
```

마지막 박스의 색을 변경하려면 아래와 같은 :last-child 가상 셀렉터를 사용해 해 줄 수 있다.

```css
.box:last-child{
  background-color:pink;
}
```

첫번째 박스를 선택하려면 아래처럼,
```css
.box:first-child{
  background-color:pink;
}
```

n번째 박스를 선택하려면

```css
.box:nth-child(n){
  background-color:pink;
}
```

2,4,6,8 번째 박스를 선택하려면

```css
.box:nth-child(2n){
  background-color
}
```



부모 아래의 child 선택을 위한 가상 셀렉터, input 아래의 child인 class box를 선택할 수 있다.

```css
input > .box
```



### Element States with CSS

- :hover

마우스 커서를 해당 컴포넌트에 가져다 댔을 때 발생하는 state다.

```css
.box:hover{
  background-color:pink;
}
```

- :active

클릭 했을때 발생하는 state

```css
.box:active{
  background-color:red;
}
```

- :focus

textarea에 커서가 깜빡이고 있는 state 등에 해당한다.

```css
<textarea class="box">hahahaha</textarea>
<style>
.box:focus{
  background-color:blue;
}
</style>
```



### display 프로퍼티

- #### block

![](/images/css/block.png)

기본적인 Display 설정 element의 크기와 상관없이 그 옆에 다른 element가 있는 것을 허용하지 않는다.

display:block 옵션을 가지면 위처럼 크기가 작아도, 옆에 element들이 위치하지 못한다. 그리고 default 값으로 display:block 속성을 가짐

- #### inline-block

![](/images/css/inline-block.png)display:inline-block 옵션을 주면, 기존의 block과 다르게 element들이 서로서로 옆에 붙어서 위치하게 된다.

- #### inline


```css
<style>
.box{
  background-color:red;
  width:200px;
  height:200px;
  border: 2px solid black;
  display:inline
}
</style>

<body>
  <div class="box">hello</div>
  <div class="box">hi</div>
  <div class="box">bye</div>
</body>
```

위와 같은 설정을 준다고 가정했을때, inline 옵션은 box class의 모든 property 설정값을 지운다. 

.box에서 width:200px, height:200px을 주어도 내가 가진 box의 contents의 폭, 넓이 만큼만 스타일이 적용되게 된다.

![](/images/css/inline.png)

위처럼 box가 가진 contents의 길이만큼 hello, hi, bye만큼의 영역에만 스타일이 지정된다.



### position 프로퍼티

- #### static

position의 디폴트 프로퍼티, 기본적인 상태이다

- #### relative

static 상태를 기준으로, top,bottom,right,left로 이동하고 싶을때 사용한다. 태그의 안쪽 방향으로 이동(bottom 5px을 주면 바닥을 기준으로 안쪽으로 5px 이동)

- #### fixed

![](/images/css/fixed.png)

```css
<style>
body, html{
  height: 100%;
  margin:0;
  padding:0;
}
body{
  height:400%;
  background-color:red;
}
.boxOne{
  height:300px;
  width:300px;
  background-color:yellow;
}
.boxTwo{
  height:300px;
  width:300px;
  background-color:green;
  position:fixed;
}
</style>
```



position을 고정시킨다. 스크롤을 해도 항상 그 위치에 고정되어 위치하게 된다.

제일 처음 포지션이 노란 박스의 다음이기 때문에, 붙어있어야 하지만 스크롤을 내리자 노란 박스는 위에 있고 초록 박스는 fixed로 고정이라 화면상 위치가 고정되게 된다.

고정된 위치 상에서 top, bottom, left, right 옵션을 이용해 간격을 줄 수도 있다.

```css
position:fixed;
top:10px;
bottom:10px;
left:20px;
right:50px;
```

- #### absolute

fixed랑 비슷하다, 어디에든 고정시킬 수 있지만 스크롤한다고 보이진 않음.

position:absolute가 설정되면  position:relative가 설정된 부모 엘리먼트를 기준으로 위치가 잡히게 된다. 만약 relative가 설정된 부모 박스가 없다면, body를 기준으로 포지션을 잡을 것이다.

```java
<style>
  .abs-box{
  width:400px;
  height:400px;
  background-color:yellow;
  }
  .abs-child{
  width:100px;
  height:100px;
  background-color:green;
  position:absolute;
  right:0;
  }
</style>
<div class="abs-box">
  <div class="abs-child"></div>
</div>
```

![](/images/css/absolute.png)

위와 같이 position을 설정하면, 기준점을 잡을 relative 부모를 찾을 수 없어서 body를 기준으로 우리가 설정한 값 대로 움직이게 된다.



그렇다면, 부모 박스에 relative를 설정해서 관계를 설정해보자. 부모 박스에 relative를 설정하고, 그 기준으로 오른쪽을 기준으로 50픽셀을 잡아주자.

```css
.abs-box{
  width:400px;
  height:400px;
  background-color:yellow;
  position:relative;
}
.abs-child{
  width:100px;
  height:100px;
  background-color:green;
  position:absolute;
  right:50px;
}
```

![](/images/css/absolute2.png)

위처럼 노란색 부모 박스를 기준으로 50픽셀 이동한 위치에 차일드 박스가 위치하는것을 볼 수 있다.



### Flex

CSS의 flex는 엘리먼트들의 크기나 위치를 쉽게 잡아주는 도구이다.

플렉스는 자식 컴포넌트가 아닌, 부모 컴포넌트에만 적용하면 된다.

```Html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, inital-scale=1.0">
    <meta name="description" content="fuck you">
    <meta name="author" content="kyunam">
    <link rel="stylesheet" href="styles.css">
    <title>Inline Example</title>
    <style>
      .parent{
        display:flex;
      }
      .box{
        background-color:red;
        width:200px;
        height:200px;
        margin:5px;
      }
    </style>
  </head>
  <div class="parent">
    <div class="box"></div>
    <div class="box"></div>
    <div class="box"></div>
    <div class="box"></div>
  </div>
</html>
```

![](/images/css/flex-normal.png)

위처럼 display:flex를 적용하면 자식 클래스는 inline-block형태로 grid 형태로 만들어져서 출력되게 된다. 

다음으로 flex에 존재하는 속성들에 대해 알아보자.

- #### justify-content 속성

  - justify-content:flex-start

  ![](/images/css/flex-start.png)

  기본 값, 앞에서부터 grid를 형성한다.

  - justify-content:flex-end

  뒤에서부터 gird를 형성한다.

  ![](/images/css/flex-end.png)

  뒤에서부터 grid를 형성한다.

  - justify-content:center

  ![](/images/css/center.png)

  사진이 제대로 구분은 되지 않지만, 화면의 정 중앙에 그리드 형태로 4개의 박스들이 위치하게 된다.

  - justify-content:space-between

  ![](/images/css/space-between.png)

  컴포넌트들을 같은 간격으로 배치한다. 좌우 간격은 없는 상태

  - justify-content:space-around

  ![](/images/css/space-around.png)

  컴포넌트들을 같은 간격으로 배치하는데, 좌우 간격까지 같게 배치한다.



- #### align-item 속성
  justify-content가 수평으로 적용된다면, 수직으로 적용되는 속성이 align-item이다.

  - align-item:center

  ![](/images/css/align-item-center.png)

  수평이 아닌 수직을 기준으로 가운데에 배치한다.

  - align-item:flex-start

  수직으로 시작 부분에 배치한다.(위에서 부터)

  - align-item:flex-end

  수직으로 끝에서 부터 배치한다.(아래서 부터)

  ​



- #### flex-direction 속성
  컴포넌트들을 가로로 배치할지, 세로로 배치할지(row, column)에 대한 속성이다.

  - flex-direction:row

    ![](/images/css/flex-direction-row.png)

    요소들을 컴포넌트들의 방향과 동일하게 배치합니다.

  - flex-direction:row-reverse

    ![](/images/css/flex-direction-row-reverse.png)

    요소들을 컴포넌트 반대 방향으로 배치합니다. 1,2,3 -> 3,1,2

  - flex-direction:column

    ![](/images/css/flex-direction-column.png)

    요소들을 세로로 컴포넌트 방향대로 배치합니다.

  - flex-direction:column:reverse

    ![](/images/css/flex-direction-column-reverse.png)

    요소들을 세로로 컴포넌트 반대 방향대로 배치합니다.


flex-direxction을 column으로 변경하면, 기존에 적용된 justify-content가 반대로 적용된다. 수평이 아니라 수직에 center가 적용되고, align-item은 수평에 적용된다.

- #### flex-wrap

10개의 컴포넌트가 있다고 하면, 한 줄에 10개가 크기가 조절되면서 표시된다, 근데 만약 크기가 줄어드는 시점에 컴포넌트들을 다음 줄에 출력하도록 하고 싶다면?

```css
flex-wrap:wrap;
```

위와 같은 설정으로 해결해 줄 수 있다.

위에서 먼저 6개가 출력되고 밑에 4개가 출력될텐데, 반대로 하고 싶다면

```css
flex-wrap:wrap-reverse;
```

위와 같이 설정해주면 된다.