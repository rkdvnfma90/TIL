# Mock Service Worker (msw)

보통 백엔드에서 데이터를 가져오는 부분을 테스트하기 위해 실제 서버로 호출하는 테스트를 작성할 수 있지만. 아직 백엔드 환경이 구성되지 않았을 경우엔 msw를 사용해서 테스트를 할 수 있다.
그럼 `msw`는 무엇일까?

서버에 요청을 보낼 때 그 요청을 가로채서 `Mock Service Worker`라는 것으로 요청을 처리할 수 있고, 모의 응답을 보내준다.

## 작동방식

브라우저에 Service Worker를 등록하여 외부로 나가는 리퀘스트를 감지하여 실제 서버로 가기전 중간에 가로채서 msw의 클라이언트 사이드 라이브러리로 보내준다. 그 후 `등록된 핸들러`에서 처리한 후 모의 응답을 브라우저로 보낸다.

이 글을 보면 별도의 핸들러를 등록해서 사용해야 한다는 점을 알 수 있다.
자세한 사용법은 [여기](https://mswjs.io/docs/getting-started/mocks/rest-api) 에서 확인할 수 있다.

## 설치

1. 먼저 `npm i msw` 로 설치를 해야 함
2. 핸들러 생성
   - req : 매칭 요청에 대한 정보
   - res : 모의 응답을 생성하는 유틸리티
   - ctx : 모의 응답의 상태코드, 헤더, 본문을 설정하는 데 도움을 주는 함수

```js
import { rest } from 'msw';

export const handler = [
  rest.get('http://localhost:5000/products', (res, req, ctx) => {
    return res(
      ctx.json([
        {
          name: 'America',
          imagePath: '/images/america.jpeg',
        },
        {
          name: 'England',
          imagePath: '/images/england.jpeg',
        },
      ])
    );
  }),
  rest.get('http://localhost:5000/options', (res, req, ctx) => {
    return res(
      ctx.json([
        {
          name: 'Insurance',
        },
        {
          name: 'Dinner',
        },
      ])
    );
  }),
];
```

3. 서버생성 : 위에서 생성한 handlers들을 setupServer에 넘겨준다.

```js
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

// mock 서버 생성
// 요청을 보내면 이 핸들러로 요청이 간다!
export const server = setupServer(...handlers);
```

4. API Mocking 설정은 `setupTests.js`에서 진행한다.

```js
import '@testing-library/jest-dom';

import { server } from './mocks/server';

// 모든 테스트가 실행하기 전에 listen 한다.
beforeAll(() => server.listen());

// 다른 테스트에 영향을 끼치지 않기 위해 각각의 테스트가 끝난 후 핸들러들을 리셋 한번 해줘야 한다.
afterEach(() => server.resetHandlers());

// 모든 테스트가 끝나고 서버를 close 해준다.
afterAll(() => server.close());
```
