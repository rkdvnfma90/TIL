# Grid Functions

<br>

display: grid 에서 사용할 수 있는 함수들!

<br>

## fit-content

그리드 컨테이너에서 사용할 수 있고, 그리드 아이템이 가지고 있는 컨텐트 크기에 맞춰준다. 여기에 지정해 주는 값은 최대의 값을 의미한다.

```css
.container {
  display: grid;
  grid-template-columns: fit-content(200px);
}
```

<br>

## min-content, max-content

만약 그리드 아이템 내부에 텍스트가 있고 주어진 공간보다 텍스트가 넘칠 경우 단어를 기준으로 줄바꿈을 한다.

만약 주어진 width가 100px이고 내부에 단어가 `IlikeStrawberry` 가 있을경우 브라우저는 띄어쓰기가 없는 한 단어로 인식하기 때문에 줄바꿈 되지 않고 텍스트가 넘친다.

이 때 사용할 수 있는 `단위 값`은 `min-content`이다. 이 속성은 가장 긴 단어의 크기에 맞게 아이템의 크기가 조절된다.

한글의 경우 글자단위로 단어가 인식되기 때문에 원하는 결과가 나오지 않을 수 있다.

<br>

`max-content`의 경우 아이템이 최대한 활용할 수 있는 크기까지 커진다

**한글에도 min-content를 잘 적용하려면..**
그리드 아이템에 `word-break: keep-all;` 속성을 주자!

<br>

## auto-fill, auto-fit

그리드 컨테이너의 크기에 맞게 행과 열의 개수를 자동으로 조정해줄수있는 단위들 이다. 이 두 단위는 `repeat` 함수에서 사용할 수 있다.

- auto-fit : 지정할 수 있는 최대의 너비(높이)를 우선해서 적용
- auto-fill : 지정할 수 있는 최소의 너비(높이)를 우선해서 적용
