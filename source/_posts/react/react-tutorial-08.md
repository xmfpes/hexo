---
title: 리액트 튜토리얼 - 08
date: 2017-10-22 10:40:15
tags: React
categories : React
---

## **리액트 튜토리얼 - 08**

이 포스팅은 https://academy.nomadcoders.co 강의를 참고하여 만들어졌습니다.

![react](/images/react/react.jpeg)

### 하위 컴포넌트로 데이터 전달하기

우선 CSS 적용에 앞서서

Movie 컴포넌트로 보낼 데이터들을 정리해, 전달하고 Movie 컴포넌트에서는 데이터들을 받아
출력해주어야 한다.

```javascript
_renderMovies = () => {
   const movies = this.state.movies.map((movie) => {
     return <Movie
       title={movie.title}
       poster={movie.medium_cover_image}
       key={movie.id}
       genres={movie.genres}
       synopsis={movie.synopsis}
     />
   })
   return movies;
 }
```
title은 그대로 poster는 medium_cover_image로 변경하고 그 이외에 genre, synopsis를 추가하고 무비 컴포넌트에서 출력하도록 수정하자.

이제 Movie.js로 이동해서 props를 받아서 처리하도록 수정하자.

우선

우선 Movie functional Component를 아래와 같이 수정한다.
```javascript
function Movie({title, poster, genres, synopsis}){
    return (
        <div className="Movie">
            <div className="Movie__Columns">
                <MoviePoster poster={poster} alt={title} />
            </div>
            <div className="Movie__Columns">
                <h1>{title}</h1>
                <div className="Movie__Genres">
                    {genres.map((genre, index) => <MovieGenre genre={genre} key={index} />)}
                </div>
            </div>
            <p className="Movie__Synopsis">
                {synopsis}
            </p>
        </div>
    )
}
```

그리고 propTypes와 각각의 하위 컴포넌트를 수정한다.

```javascript
function MovieGenre({genre}){
    return (
        <span className="Movie__Genre">{genre}</span>
    )
}
function MoviePoster({poster, alt}){
    return (
        <img src={poster} alt={alt} title={alt} className="Movie__Poster"/>
    )
}

MovieGenre.propTypes = {
    genre: PropTypes.string.isRequired
}

Movie.propTypes = {
    title: PropTypes.string.isRequired,
    poster: PropTypes.string.isRequired,
    genres: PropTypes.array.isRequired,
    synopsis: PropTypes.string.isRequired
}

MoviePoster.propTypes = {
    poster: PropTypes.string.isRequired,
    alt: PropTypes.string.isRequired
}
```


### CSS 적용하기

CSS 적용과 함께 조금씩 구조가 바뀐 것들이 있는데, 수정한다.

### App.css
```css
.App {
  padding: 50px;
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  font-size: 14px;
}

.App--loading{
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%
}

```

### App.js

```javascript
render() {
  console.log("render");
  const { movies } = this.state;
  //this.state.movies
  return (
    <div className={movies ? "App" : "App--loading"}>
      {movies ? this._renderMovies() : 'Now Loading...'}
    </div>
  );
}
```

### index.css
```css
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  background-color:#EFF3F7;
  height: 100%;
}

html, #root{
  height:100%;
}
```

### Movie.css
```css
.Movie{
    background-color:white;
    width:40%;
    display: flex;
    justify-content: space-between;
    align-items:flex-start;
    flex-wrap:wrap;
    margin-bottom:50px;
    text-overflow: ellipsis;
    padding:0 20px;
    box-shadow: 0 8px 38px rgba(133, 133, 133, 0.3), 0 5px 12px rgba(133, 133, 133,0.22);
}

.Movie__Column{
    width:30%;
    box-sizing:border-box;
    text-overflow: ellipsis;
}

.Movie__Column:last-child{
    padding:20px 0;
    width:60%;
}

.Movie h1{
    font-size:20px;
    font-weight: 600;
}

.Movie .Movie__Genres{
    display: flex;
    flex-wrap:wrap;
    margin-bottom:20px;
}

.Movie__Genres .Movie__Genre{
    margin-right:10px;
    color:#B4B5BD;
}

.Movie .Movie__Synopsis {
    text-overflow: ellipsis;
    color:#B4B5BD;
    overflow: hidden;
}

.Movie .Movie__Poster{
    max-width: 100%;
    position: relative;
    top:-20px;
    box-shadow: -10px 19px 38px rgba(83, 83, 83, 0.3), 10px 15px 12px rgba(80,80,80,0.22);
}

@media screen and (min-width:320px) and (max-width:667px){
    .Movie{
        width:100%;
    }
}

@media screen and (min-width:320px) and (max-width:667px) and (orientation: portrait){
    .Movie{
        width:100%;
        flex-direction: column;
    }
    .Movie__Poster{
        top:0;
        left:0;
        width:100%;
    }
    .Movie__Column{
        width:100%!important;
    }
}
```

### Movie.js

```bash
npm install --save react-lines-ellipsis
```

Movie.js 코드를 수정하기 전에 위의 모듈을 설치하자.

synopsis 부분이 너무 길어지면, css가 이상하게 되기 때문에 길어지면 ...으로 처리해주는 모듈이다.

```js
import LinesEllipsis from 'react-lines-ellipsis' 제일 위에 추가하기

function Movie({title, poster, genres, synopsis}){
    return (
        <div className="Movie">
            <div className="Movie__Column">
                <MoviePoster poster={poster} alt={title} />
            </div>   
            <div className="Movie__Column">
                <h1>{title}</h1>
                <div className="Movie__Genres">
                    {genres.map((genre, index) => <MovieGenre genre={genre} key={index} />)}
                </div>
                <div className="Movie__Synopsis">
                <LinesEllipsis
                    text={synopsis}
                    maxLine='3'
                    ellipsis='...'
                    trimRight
                    basedOn='letters'
                    />   
                </div>
            </div>
        </div>
    )
}
```
