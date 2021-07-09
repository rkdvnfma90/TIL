# 모달창 띄우기 & 끄기

보통 모달창을 띄웠을 때 모달창을 다시 닫는 방법은 크게 3가지로 나눌 수 있다.

1. esc 키 누른다.
2. 모달창 내 닫기 버튼 누른다.
3. 모달창 외 영역 클릭하여 닫는다.

지금은 3번 `(모달창 외 영역 클릭하여 닫는다.)`방식을 한번 리마인드 해보도록 하겠다.

<br/>
<br/>

## 구조

보통 모달창을 만들 때에는 전체 화면을 차지하는 `container`로 한번 감싸고 그 안에 실질적인 모달창(`contents`)을 그린다. 그 후 `container`에 클릭이벤트를 달아줘서 클릭하면 모달창이 닫히게 하면 되는데, 이 때 `contents`를 클릭하여도 버블링이 발생하기 때문에 팝업창이 닫히게 된다.

<br/>
<br/>

## 해결책

1. `event.stopPropagation()`을 사용하여 이벤트 전파를 강제로 막을 수 있다.
   - 이 방식은 자신의 상황에 맞게 사용해야 한다. 그 이유는 나중에 이벤트 전파를 활용해야 하는 경우가 생길 경우 문제가 될 수 있기 때문이다.
2. 이벤트 전파는 형제들끼리는 일어나지 않기 때문에 그 특성을 이용한다.
   - `container` 안에 화면을 전부 다 덮는 `overlay`를 만들고 여기에 클릭이벤트를 다는 것이다. 그럼 `contents`와 `overlay`는 형제이므로 `contents`를 클릭하여도 모달창이 닫히지 않는다.

```css
.container {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  z-index: 99;
  display: flex;
  justify-content: center;
  align-items: center;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgb(0, 0, 0, 0.8);
}

.contents {
  position: relative;
  width: 40%;
  height: 20%;
}
```
