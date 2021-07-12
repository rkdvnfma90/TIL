# 개발자가 알아야 하는 useEffect 4가지 팁!

<br/>

TOAST UI의 Weekly Picks에는 좋은글 들이 많은 것 같다. 매일매일 읽어보도록 해야지.

오늘은 그중에서 리액트의 `useEffect`관련 4가지 팁에 대해 정리해보려 한다.

<br/>
<br/>

## 단일 목적

<br/>

`useEffect` 안에서 여러개의 함수를 사용할 수 있다. 하지만 클린코드 관점에서 함수는 한 가지 목적을 가져야 하기 때문에 이렇게 사용하는 것은 좋지 않다.

`useEffect`를 단일 목적의 함수로 분리한다면 개발자가 의도하지 않은 이펙트 함수의 실행을 방지할 수 있다. 먼저 useEffect와 setTimeout을 이용한 나쁜 코드를 보자!

<br/>

```javascript
function App() {
  const [dataA, setDataA] = useState(0);
  const [dataB, setDataB] = useState(0);

  useEffect(() => {
    const timeoutA = setTimeout(() => setDataA(dataA + 1), 1000);
    const timeoutB = setTimeout(() => setDataB(dataB + 2), 2000);

    return () => {
      clearTimeout(timeoutA);
      clearTimeout(timeoutB);
    };
  }, [dataA, dataB]);

  return (
    <span>
      A : {dataA} , B : {dataB}
    </span>
  );
}
```

이렇게 할 경우 한번의 상태 변경으로 `dataA`, `dataB` 두 상태 변수에 업데이트가 발생하므로 정상적인 동작이 아니다. 그럼 정상정인 동작을 하는 코드를 보자.

<br/>

```javascript
function App() {
  const [dataA, setDataA] = useState(0);
  const [dataB, setDataB] = useState(0);

  useEffect(() => {
    const timeoutA = setTimeout(() => setDataA(dataA + 1), 1000);

    return () => clearTimeout(timeoutA);
  }, [dataA]);

  useEffect(() => {
    const timeoutB = setTimeout(() => setDataB(dataB + 2), 2000);

    return () => clearTimeout(timeoutB);
  }, [dataB]);

  return (
    <span>
      A : {dataA} , B : {dataB}
    </span>
  );
}
```

<br/>
<br/>

## 가능하다면 커스텀 훅 사용

<br/>

위 코드는 올바르게 동작한다, 하지만 만약에 `dataA`와 `dataB`가 완전 독립적이라면 어떨까?

커스텀훅으로 만든다면 두 상태를 완전히 독립적으로 만들 수 있고, 각 함수들이 어떤 변수를 사용하는지도 쉽게 파악할 수 있다.

<br/>

```javascript
function App() {
  const [dataA, setDataA] = useDataA();
  const [dataB, setDataB] = useDataB();

  return (
    <span>
      A : {dataA} , B : {dataB}
    </span>
  );
}

// 커스텀 훅
function useDataA() {
  const [dataA, setDataA] = useState(0);

  useEffect(() => {
    const timeoutA = setTimeout(() => setDataA(dataA + 1), 1000);

    return () => clearTimeout(timeoutA);
  }, [dataA]);

  return [dataA, setDataA];
}

function useDataB() {
  const [dataB, setDataB] = useState(0);

  useEffect(() => {
    const timeoutB = setTimeout(() => setDataB(dataB + 1), 1000);

    return () => clearTimeout(timeoutB);
  }, [dataB]);

  return [dataB, setDataB];
}
```

이러면 각 변수들은 고유의 훅을 갖게되었고 훨씬 더 유지보수가 쉽고 읽기도 쉽다!

<br/>
<br/>

## 조건부 useEffect의 옳은 방법

<br/>

아까 위에서 사용했던 예제코드를 봐보자

```javascript
function app() {
  const [dataA, setDataA] = useState(0);

  useEffect(() => {
    const timeoutA = setTimeout(() => setDataA(dataA + 1), 1000);

    return () => clearTimeout(timeoutA);
  }, [dataA]);

  return <span>A : {dataA}</span>;
}
```

만약 이 카운터를 최대 5개까지로 제한하고싶을 때 잘못된 방법과 옳은 방법을 살펴보자. 먼저 `잘못된 방법`이다.

<br/>

```javascript
function App() {
  const [dataA, setDataA] = useState(0);

  useEffect(() => {
    let timeout;

    if (dataA < 5>) {
      timeout = setTimeout(() => setDataA(dataA + 1), 1000);
    }

    return () => clearTimeout(timeout)
  }, [dataA]);

  return <span>A : {dataA}</span>;
}
```

위 코드는 동작하긴 하지만 setTimeout은 조건적으로 실행하는 반면 clearTimeout은 dataA가 변경될 때 마다 실행된다. 아주 큰 문제는 아니지만 정상적인 작업은 아니다.

useEffect를 조건적으로 사용하는 이상적인 방법은 함수의 초기에 바로 반환해버리는 것이다.

<br/>

```javascript
function App() {
  const [dataA, setDataA] = useState(0);

  useEffect(() => {
    if (dataA >= 5) return;

    const timeout = setTimeout(() => setDataA(dataA + 1), 1000);

    return () => clearTimeout(timeout);
  }, [varA]);

  return <span>A : {dataA}</span>;
}
```

이 방법은 `Material UI`에서도 많이 사용한다고 한다. 또한 의도치 않은 `useEffect`의 실행을 막을 수 있다.

<br/>
<br/>

## useEffect 안에서 사용하는 모든 변수들은 디펜던시 배열에 추가하자

<br/>

ESLint를 사용하면 `exhaustive-deps` 규칙의 경고를 많이 봤을 것이다.

이 규칙은 매우 중요하다. 앱이 점점 커질수록 `useEffect`에는 많은 디펜던시가 추가될 것이고 모든 디펜던시의 변화를 감지하려면 디펜던시 배열에 추가해야 한다.

<br/>

```javascript
function App() {
  const [dataA, setDataA] = useState(0);

  useEffect(() => {
    if (dataA >= 5) return;

    const timeout = setTimeout(() => setDataA(dataA + 1), 1000);

    return () => clearTimeout(timeout);
  }, []); // 디펜던시 배열에 dataA가 없다.

  return <span>A : {dataA}</span>;
}
```

위 코드를 보면 `useEffect`의 디펜던시에 dataA가 빠진것을 볼 수 있다. 저 코드는 비록 의도했던 대로 동작하지만 만약 코드가 더 커지거나 변경해야할 일이 생길수도 있기 때문에 이런 상황에 대비하여 사용되는 모든 변수를 디펜던시 배열에 추가하자! 그래야 테스트하기 수월해지고 문제를 쉽게 탐지할 수 있다.

<br/>
<br/>

> 출처 : https://ui.toast.com/weekly-pick/ko_20200916
