# transform

<br>

요소의 크기 조절, 기울기, 이동, 회전등의 효과를 부여할 수 있는 속성이다.

또한 transform에서 사용할 수 있는 함수는 여러개가 있는데 띄어쓰기로 구분하여 한번에 사용할 수 있다.

```css
container {
  transform: perspective(300px) rotate(45deg);
}
```

transform 으로 사용할 수 있는 세부 속성들에 대해 더 알아보자!

<br>

## transform-origin

```css
container {
  /* 기본값 */
  transform-origin: 50% 50%;
}
```

x, y 축의 원점을 변경할 수 있다. 첫 번째 값은 x축, 두 번째 값은 y축의 값.
기본값은 `50% 50%` 이기 때문에 원점이 가운데임

**transition-duration: 1s -> 애니메이션 효과**

<br>

## transform: perspective(수치)

원근효과를 주는 속성이다. 하지만 일반 2차원 요소에서는 적용된 모습이 보이지 않을 것이다. 확인하려면 3차원 요소에서 확인할 수 있는데 요소에 `rotateY` 속성을 줘서 확인해 볼 수 있다.

이 perspective 함수는 효과를 주고자 하는 아이템에 사용하면 되고, 변환효과가 일어나는 요소의 부모에는 `perspective 함수`가 아니라 `perspective 속성`을 사용해야 한다.

```css
/* 부모 요소 */
container {
  perspective: 300px;
}
```

<br>

## transform-style: preserve-3d

이 속성을 부모요소에 주면, 자식요소의 3D 변환 효과를 볼 수 있다. 주의할 점은 후손 요소까지 모두 적용되는 것이 아니라 `자식`요소만!

```css
container {
  transform: rotateY(45deg);
  transform-style: preserve-3d; /* 자식요소에만 */
}

item {
  transform: rotateX(45deg);
}
```

<br>

## backface-visibility: hidden

요소가 회전했을 때 뒷면을 보이지 않게!
