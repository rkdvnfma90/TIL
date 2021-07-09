# IntersectionObserver

다시 한번 상기시키기 위해 작성!

IntersectionObserver란 타겟 요소와 상위 요소 또는 최상위 document의 viewport 사이의 intersection 내의 변화를 비동기적으로 관찰하는 방법이다. [mdn](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)

보통 무한스크롤을 구현하거나 이미지 동적 로딩을 위해 사용한다.

<br/>
<br/>

## 사용법

```javascript
const io = new IntersectionObserver((entries, observer) => {
  entries.forEach((entry) => {
    // isIntersecting : 뷰포트 교차 여부
    if (!entry.isIntersecting) return;

    const target = entry.target;
    target.src = target.dataset.src;
    target.classList.remove('lazy');

    // 감지되었으면 더이상 처리하지 않기 위해 해제한다.
    observer.unobserve(target);
  });
});

// 동적 로딩할 대상
const targets = document.querySelectorAll('.image.lazy');

// 대상들을 감지할 수 있도록 한다.
targets.forEach((target) => {
  io.observe(target);
});
```
