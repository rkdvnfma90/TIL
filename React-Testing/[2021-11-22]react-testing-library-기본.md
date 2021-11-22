# React-Testing-Library

- React Testing Library는 한마디로 표현하자면 리액트의 컴포넌트 테스트를 할 수 있는 솔루션 이다.

- 보통 CRA를 통해 프로젝트를 생성하면 그 안에 포함되어 있고 별도로 설치하기 위해선 `yarn add -D @testing-library/react` 로 설치하면 된다.

- Airbnb에서 만든 `Enzyme`을 대체할 수 있다.

<br>
<br>

## 테스팅을 함으로써 얻는 이점

- 디버깅 시간을 단축할 수 있다.
- 더 안정적인 어플리케이션을 유지할 수 있다. 테스트 코드가 바탕이 되어 있기 때문에 사이드 이펙트를 쉽게 감지할 수 있음!

<br>
<br>

## Jest

그렇다면 Jest는 무엇일까? Jest는 페이스북에서 만든 테스팅 프레임워크이다. 보통 단위 테스트를 할 때 사용한다.

간단한 사용법

- `yarn add -D jest`로 설치.
- `"test": "jest" or "jest --watchAll"`로 테스트의 스크립트를 package.json에 추가해준다.
- 테스트를 작성할 폴더나 파일을 생성하면 되는데 이 때 규칙은 다음과 같다.
  - {filename}.`test`.js
  - {filename}.`spec`.js
  - `test` 폴더

<br>

### 기본 사용법

보통 `describe`로 그룹화하여 `test`로 개별 테스트를 진행한다.

```js
descibe('메인화면에 대한 테스트를 진행한다.', () => {
  test('카운터는 0부터 시작한다', () => {
    render(<App />);
    const counterElement = screen.getByTestId('counter');
    expect(counterElement).toHaveTextContent(0);
  });

  test('- 버튼이 있는지 확인한다.', () => {
    render(<App />);
    const minusButtonElement = screen.getByTestId('minus-button');
    expect(minusButtonElement).toHaveTextContent('-');
  });
});
```

- `expect` 함수는 단독으로는 거의 사용하지 않으며 보통 `matcher`와 같이 사용한다.
- `matcher`는 값을 테스트 할 수 있도록 한다. (위 코드에서는 `toHaveTextContent`가 matcher이다.)

<br>
<br>

## React Testing Library의 주요 API

### render 함수

- DOM에 컴포넌트를 렌더링하는 함수이다. 렌더링 할 리액트 컴포넌트를 명시해주면 된다.

```js
test('~~~', () => {
  render(<App />);
  ... 생략
});
```

### 쿼리 함수

- 쿼리는 어떤 페이지에서 요소를 찾기위해 테스트 라이브러리가 제공하는 함수이다. 크게 3가지로 나눌 수 있는데 바로 `get, find, query`이다.

  - `getBy...` : 쿼리에 대해 일치하는 노드를 1개 반환한다. 일치하는 요소가 없거나 둘 이상의 일치가 반환되면 오류를 발생시킨다. (둘 이상의 요소가 예상되면 getAllBy... 를 사용한다.)
  - `queryBy...` : 쿼리에 대해 일치하는 노드를 반환하고 일치하는 요소가 없으면 null을 반환한다. getBy...와 마찬가지로 둘 이상의 일치가 반환되면 오류를 발생시킨다. (queryAllBy... 사용하여 둘 이상 요소에 사용할 수 있다.)
  - `findBy...` : 쿼리에 대해 일치하는 요소가 반환되면 `Promise`를 반환한다. 요소가 발견되지 않거나 기본 제한 시간인 1000ms 뒤에 둘 이상의 요소가 발견되면 Promise가 거부된다. 마찬가지로 둘 이상의 요소를 찾아야 하는 경우 findAllBy를 사용한다.

  <br>
  <br>

## Query 사용 우선순위

testing library에서 추천하는 쿼리 사용 우선순위가 있다. 자세한 내용은 [여기](https://testing-library.com/docs/queries/about/#priority)에서 확인할 수 있다.

<br>
<br>

## userEvent

`userEvent`는 fireEvent를 사용해서 만들어졌다. userEvent는 엘리먼트의 타입에 따라 Label을 클릭했을 때, checkbox, radio를 클릭했을 때의 그 타입에 맞는 적절한 반응을 보여준다고 한다.
fireEvent로 버튼을 클릭하면 `fireEvent.click(buttonElement)` 버튼이 focus가 되지 않는다. 하지만 userEvent로 클릭하면 `userEvent.click(buttonElement)` 버튼이 focus가 된다.
이렇게 실제 사용하는 유저가 보기에 실제 버튼을 클릭하는 행위가 더 잘표현되기 때문에 userEvent를 사용하는게 추천된다!

[여기](https://github.com/testing-library/user-event/blob/5feaa942f46bb37d96c2f2fbeb4b33e8beff75ad/src/click.js#L87-L103) 에서 내부 코드를 확인할 수 있다.
