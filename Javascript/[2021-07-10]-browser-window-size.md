# 봐도봐도 헷갈리는 브라우저 창 사이즈

브라우저 창 관련 컨트롤은 볼때마다 헷갈리는것 같다. 다시 한번 리마인드!

## 브라우저 창의 너비와 높이

창이 차지하는 너비와 높이는 `document.documentElement`의 `clientWidth`와 `clientHeight`를 사용하면 된다.

이 때, `window`객체가 아닌 `document.documentElement`를 쓰는 이유는 무엇일까? 브라우저의 `window`객체도 `innerWidth`와 `innerHeight`프로퍼티가 있다. 하지만 스크롤바가 차지하는 공간까지 포함한다.

정리하자면

- `document.documentElement` : clientWidth, clientHeight는 스크롤바 공간을 제외한 너비나 높이!
- `window` : innerWidth, innerHeight는 스크롤바 공간 포함한 너비나 높이!

보통 창 사이즈가 필요한 경우는 스크롤 바 안쪽에 배치하거나 그릴 때 필요하기 때문에 `clientWidth`, `clientHeight`를 써야한다.

> DOCTYPE을 꼭 써야한다. `<!DOCTYPE HTML>`가 명시되어 있지 않으면 이상하게 동작할 수 있다.

<br/>
<br/>

## 문서의 너비와 높이

정확한 `문서 전체 높이`를 구하려면 아래 프로퍼티중 최댓값을 골라야 한다.

```javascript
let scrollHeight = Math.max(
  document.body.scrollHeight,
  document.documentElement.scrollHeight,
  document.body.offsetHeight,
  document.documentElement.offsetHeight,
  document.body.clientHeight,
  document.documentElement.clientHeight
);

console.log(`문서 최대의 높이 : ${scrollHeight}`);
```

왜 이런방식으로 문서 전체 높이를 구하는지에 대한 이유는... [이곳에](https://ko.javascript.info/size-and-scroll-window)

> 그렇다면 왜 이런 방식으로 문서 전체 높이를 구해야 하는 걸까요? 이유는 알아보지 않는 게 낫습니다. 이런 이상한 계산법은 아주 오래 전부터 있었고 그다지 논리적이지 않은 이유로 만들어졌기 때문입니다.

<br/>
<br/>

## 스크롤 정보

대부분의 브라우저에서 DOM요소의 스크롤 정보는 `document.documentElement`의 `scrollLeft`와 `scrollTop`프로퍼티를 통해 구할 수 있다. 다만 구버전 Webkit을 기반으로 하는 브라우저는 `document.body`로 구해야 한다. 그렇다면 브라우저 별로 다 예외처리를 해야할까? 다행히 `window`객체의 `pageXOffset`과 `pageYOffset`으로 구할수 있다.

<br/>
<br/>

## IntersectionObserver 사용하지 않고 무한스크롤!

무한스크롤을 구현할 때 `IntersectionObserver`를 사용하면 별도로 창 크기를 구하지않아도 되고, 쓰로틀링 처리를 하지 않아도 된다. 그렇다면 `IntersectionObserver`를 사용하지 않고 무한스크롤을 구현하려면 어떻게 해야할까?

```javascript
// 현재 스크롤한 높이 (현재 보이는 영역의 높이)
const scrollTop = window.pageYOffset || document.documentElement.scrollTop;

// 현재 문서의 전체 높이
const documentHeight = Math.max(
  document.body.scrollHeight,
  document.body.offsetHeight,
  document.body.clientHeight,
  document.documentElement.scrollHeight,
  document.documentElement.offsetHeight,
  document.documentElement.clientHeight
);

window.addEventListener('scroll', () => {
  // 현재 스크롤한 높이보다 (문서 전체 높이 - 현재 브라우저의 창 크기)
  if (scrollTop < documentHeight - window.innerHeight) return;

  fetch();
});
```
