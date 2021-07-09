# Throttling, Debouncing

- Throttling : 어떠한 이벤트가 일정시간 내에 계속 호출되면 하나의 이벤트만 실행하도록 한다. (짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화) 주로 `스크롤` 이벤트에 사용한다.

```javascript
const throttle = (callback, delay) => {
  let timerId = null;

  return (...args) => {
    if (!timerId) {
      timerId = setTimeout(() => {
        timerId = null;
        callback(...args);
      }, delay);
    }
  };
};
```

<br/>
<br/>

- Debouncing : 어떠한 이벤트가 일정시간 내에 계속 호출되면 가장 마지막에 호출된 이벤트만 실행한다. 주로 `검색, 버튼 중복클릭 방지`에 사용한다.

```javascript
const debounce = (callback, delay) => {
  let timerId = null;

  return (...args) => {
    if (timerId) clearTimeout(timerId);

    timerId = setTimeout(() => {
      callback(...args);
    }, delay);
  };
};
```
