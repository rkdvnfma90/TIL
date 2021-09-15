# SCSS - mixin

스타일을 재활용 할 수 있는 `@mixin`

```scss
@mixin large-text {
  font-size: 30px;
  font-weight: bold;
  color: gray;
}

@mixin small-text($size: 20px) {
  font-size: $size;
  font-weight: bold;
  color: gray;
}
```

이렇게 `@mixin`을 정의해 주고 사용할 때는 `@include` 키워드를 사용하면 된다.

```scss
.box-a {
  width: 100px;
  @include large-text;
}

.box-b {
  width: 100px;
  @include small-text(10px);
}
```

자바스크립트의 함수처럼 매개변수도 사용할 수 있고, 매개변수에 기본값도 정의가능하다 (`:` 사용)

<br>

## if를 삼항연산자와 비슷하게 사용할 수 있음

조건, true일 때 실행할 내용, false일 때 실행할 내용
if(condition, true, false)

```scss
@mixin small-text($size) {
  font-size: if($size < 20px, 20px, $size);
  font-weight: bold;
  color: gray;
}
```

<br>

## 가변 인수

@mixin에 매개변수의 개수가 가변으로 들어올 경우에 처리할 수 있는 가변 인수는 `...`을 사용하면 된다. (자바스크립트의 스프레드 연산자)

```scss
@mixin test($a, $b, $c...) {
}
```

아래와 같이 스프레드 연산자로 사용할 수도 있다.

```scss
@mixin sp($a, $b, $c, $d) {
  // 생략
}

.container {
  $var: 10px 20px 30px 40px;
  @include sp($var...);
  @include sp(10px 20px 30px 40px);
}
```

<br>

## 키워드 인수

만약 mixin의 매개변수의 수가 많을 경우 변수의 순서에 따라 지정하는 과정에 헷갈릴 여지가 있을 수 있다. 그럴 경우 키워드를 사용해서 명시해 줄 수 있다.

```scss
// 이렇게 null로 초기값을 설정해주면 매개변수가 없을 경우 그 부분은 컴파일 되지 않음
@mixin sp($a: null, $b: null, $c: null, $d: null) {
  // 생략
}

.container {
  @include sp($a: 10px, $c: 20px, $b: 30px, $d: 40px) {
    // 생략
  }
}
```

<br>

## @content

믹스인 안에 @content가 선언되어 있으면 이 부분으로 스타일 블록을 전달할 수 있다.

```scss
@mixin bg($url) {
  background-image: url($url);
  @content;
}

.container {
  @include bg('/assets/images/logo.jpg') {
    position: fixed;
    top: 0;
  }
}
```

위 코드를 보면 bg 믹스인 안에 position, top 속성이 들어간다.

```css
.container {
  background-image: url('/assets/images/logo.jpg');
  position: fixed;
  top: 0;
}
```

<br>

## 미디어 쿼리의 중첩 사용

```scss
.container {
  width: 100px;
  height: 100px;

  @media all and (max-width: 1000px) {
    width: 200px;
    height: 200px;
  }
}
```

아래와 같이 컴파일 됨

```css
.container {
  width: 100px;
  height: 100px;
}
@media all and (max-width: 1000px) {
  .container {
    width: 200px;
    height: 200px;
  }
}
```

미디어 쿼리를 믹스인으로 분리해서 편하게 사용해보자

```scss
// map을 불러오기 위해 사용하는 내장 모듈이 필요하다.
@use "sass:map";

@mixin media($name) {
  $breakpoint: (
    sm: 500px,
    md: 900px,
    lg: 1400px,
  );

  @media all and (max-width: map.get($breakpoint, $name)) {
    @content;
  }
}

.container {
  width: 500px;
  height: 500px;

  @include media(sm) {
    width: 100px;
    height: 100px;
  }

  @include media(lg) {
    width: 200px;
    height: 200px;
  }
}
```

위 코드가 이렇게 컴파일 된다.

```css
.container {
  width: 500px;
  height: 500px;
}

@media all and (max-width: 500px) {
  .container {
    width: 100px;
    height: 100px;
  }
}

@media all and (max-width: 1400px) {
  .container {
    width: 200px;
    height: 200px;
  }
}
```

<br>

@content에 값도 전달할 수 있다

```scss
@use "sass:map";

@mixin media($name) {
  $breakpoint: (
    sm: 500px,
    md: 900px,
    lg: 1400px,
  );

  @media all and (max-width: map.get($breakpoint, $name)) {
    @content (map.get($breakpoint, $name));
  }
}

.container {
  width: 500px;
  height: 500px;

  @include media(sm) using($bp) {
    width: 100px;
    height: 100px;
    _debug: $bp;
  }
}
```
