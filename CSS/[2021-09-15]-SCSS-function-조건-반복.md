# function, 반복

function 흔히 알고 있는 자바스크립트의 함수와 비슷하고, 반복문도 마찬가지로 사용할 수 있다.

<br>

## @function

```scss
@function widthFunc() {
  @return 200px;
}

.container {
  width: widthFunc();
}
```

여기에 조금의 조건을 더 추가할 수 있다.

```scss
@use 'sass:list';

@function widthFunc($col: 1, $total: 12, $rest...) {
  @if ($col > $total) {
    @error 'col은 total보다 클 수 없습니다.'; // 자바스크립트의 throw와 비슷
  }

  @if (list.length($rest) > 0) {
    @error '불필요한 인자가 있습니다.';
  }

  @return $col / $total;
}

.container {
  width: widthFunc(100, 20); // col은 total보다 클 수 없습니다. 에러 발생
  height: widthFunc(100, 20, 10, 5); // 불필요한 인자가 있습니다. 에러 발생
}
```

<br>

## 반복문 @each

```scss
@use 'sass:list';

$sizes: 10px 20px 30px;
$fruits: (
  apple: 'A',
  banana: 'B',
);

// List
@each $size in $sizes {
  // 이때 index는 0부터 시작이 아니라 1부터 시작한다.
  $index: list.index($sizes, $size);
  .icon-#{$index} {
    width: $size;
    height: $size;
  }
}

// Map
@each $key, $value in $fruits {
  .fruit-#{$key}::after {
    content: $value;
  }
}
```

<br>

## 반복문 @for

through 사용

```scss
.container {
  position: fixed;
  top: 0;
  // 1에서 3까지 반복 for (let i = 1; i <= 3; i += 1>)과 동일
  @for $i from 1 through 3 {
    .item {
      width: 100px * $i;
    }
  }
}
```

<br>

to 사용

```scss
.container {
  position: fixed;
  top: 0;
  // 1에서 2까지 반복 for (let i = 1; i < 3; i += 1>)과 동일
  @for $i from 1 to 3 {
    .item {
      width: 100px * $i;
    }
  }
}
```
