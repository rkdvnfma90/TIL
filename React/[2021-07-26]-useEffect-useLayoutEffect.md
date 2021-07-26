# useEffect, useLayoutEffect

<br>

## useEffect

<br>

- 컴포넌트 렌더링 -> 화면 업데이트 -> `useEffect`
- 비동기

<br>
<br>

## useLayoutEffect

<br>

- 컴포넌트 렌더링 -> `useLayoutEffect` -> 화면 업데이트
- 동기
- 렌더링 직후 DOM 요소 값을 제어할 때
- useEffect를 사용할 경우 화면 깜빡임 현상이 발생할 수 있다.
