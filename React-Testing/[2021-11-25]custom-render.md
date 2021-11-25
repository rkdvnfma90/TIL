# 커스텀 render

테스트를 하던 도중 context API를 사용해야 했고, context API를 사용하기 위해선 컴포넌트를 Provider로 감싸야 한다. 그러기 위해서는 아래와 같이 하면 된다.

```js
... 생략
render(<App />, { wrapper: UserContextProvider });
```

하지만 이렇게 할 경우 context API를 사용하는 모든 테스트에 감싸줘야 하는데 이는 매우 비효율적이다!
그럼 어떻게 효율적으로 사용할 수 있을까?
바로 아래와 같이 별도의 customRender 함수를 export 하는 파일을 생성한다.

```js
// testUtils.js
import { render } from '@testing-library/react';
import { OrderContextProvider } from './contexts/OrderContext';

const customRender = (ui, options) => {
  render(ui, { wrapper: OrderContextProvider, ...options });
};

export * from '@testing-library/react';

export { customRender };
```

`export * from '@testing-library/react'` 는 기존에 있던 라이브러리의 모든 기능등을 사용할 수 있게 하기위해 했고
`export { customRender }`는 render 함수를 래핑한 함수이다.
`export { customRender as render }` 이런식으로 사용해서 기존 render와 동일한 이름으로도 사용할 수 있다.

- 실제 사용

  - 실제 사용은 아래와 같이 사용하면 된다.

  ```js
  import { customRender, screen } from '../testUtils';

  ...생략
  customRender(<App />);
  ...생략
  ```
