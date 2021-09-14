# @supports와 @media

<br>

## @supports

주어진 하나 이상의 CSS 기능을 브라우저가 지원하는지에 따라 다른 스타일을 선언할 수 있다. 이를 `기능 쿼리(feature query)`라고 한다.

```css
@supports (display: grid) {
  div {
    display: grid;
  }
}

@supports not (display: grid) {
  div {
    float: right;
  }
}
```

<br>

## @media

화면 크기에 따른 별개의 스타일을 지정해줄수있다. 이를 `미디어 쿼리(media query)`라고 한다. 기본 미디어 타입은 `all` 이다.

```css
/* @media 타입 and (기능) { 스타일 } */
/* 화면의 가로 크기가 700px 이하일 때 아래 스타일 적용 */
@media screen and (max-width: 700px) {
  .box {
    background: orange;
  }
}

/* 미디어 타입이 기본으로 all 이기 때문에 아래와 같이 작성할 수도 있다. */
@media (max-width: 700px) {
  .box {
    background: orange;
  }
}

/* 여러개의 쿼리를 작성할 수도 있다 */
@media not all and (max-width: 700px) and (min-width: 400px), tv and (display-mode: fullscreen) {
  .box {
    background: orange;
  }
}
```

- @media print ... -> 인쇄할 때 스타일
- @media (orientation: portrait) ... -> 화면의 세로 길이가 가로 길이보다 길 때의 스타일
- @media (orientation: landscape) ... -> 화면의 가로 길이가 세로 길이보다 길 때의 스타일

<br>

위와 같이 CSS파일 내부에서 분리할 수도 있지만 아예 파일단위로 분리할 수도 있다.

```html
...
<link rel="stylesheet" href="./css/main.css" />
<link rel="stylesheet" href="./css/main.css" media="all and (max-width: 700px)" />
...
```
