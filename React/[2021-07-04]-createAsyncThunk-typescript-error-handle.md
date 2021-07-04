# Redux toolkit (with typescript) createAsyncThunk 에러 핸들링

간단한 리액트 + 타입스크립트 토이프로젝트에 `redux-toolkit`의 `createAsyncThunk`를 사용하다가 에러 핸들링을 어떻게 해야할지 알아보기 위해 [공식사이트](https://redux-toolkit.js.org/usage/usage-with-typescript) 를 참조했다.

<br/>
<br/>

## 사용한 코드

<br/>

```typescript
// testSlice.tsx
...

export const fetchTest = createAsyncThunk<
  number[],
  void,
  {
    rejectValue: string;
  }
>('typePrefix', async (_, { rejectWithValue }) => {
  try {
    const { data } = await apiClient.get('API URL');

    return data as string[];
  } catch (e) {
    return rejectWithValue('Error Message');
  }
});

...

const testSlice = createSlice({
  name: 'test',
  initialState,
  reducers: {
    ...
  },

  extraReducers: (builder) => {
    builder
      .addCase(fetchTest.pending, (state) => {
        state.isLoading = true;
        state.isError = false;
        state.errorMessage = '';
        state.data = [];
      })
      .addCase(fetchTest.fulfilled, (state, { payload }) => {
        state.isLoading = false;
        state.isError = false;
        state.errorMessage = '';
        state.data = payload;
      })
      .addCase(fetchTest.rejected, (state, { payload }) => {
        state.isLoading = false;
        state.isError = true;
        state.errorMessage = payload;
        state.data = [];
      });
  },
});
```

<br/>

먼저 `createAsyncThunk`의 매개변수는 각 순서대로 다음과 같다.

<br/>

1. type prefix
2. 비동기 처리 함수 (payload creator)

<br/>

그리고 `createAsyncThunk`에 정의할 수 있는 타입은 각 순서대로 다음과 같다.

<br/>

1. 비동기 처리 함수 (payload creator)의 리턴 타입
2. 비동기 처리 함수의 매개변수 타입
3. 커스텀 옵션

커스텀 옵션으로 정의할 수 있는 항목은 아래와 같다.

```typescript
type AsyncThunkConfig = {
  /** return type for `thunkApi.getState` */
  state?: unknown
  /** type for `thunkApi.dispatch` */
  dispatch?: Dispatch
  /** type of the `extra` argument for the thunk middleware, which will be passed in as `thunkApi.extra` */
  extra?: unknown
  /** type to be passed into `rejectWithValue`'s first argument that will end up on `rejectedAction.payload` */
  rejectValue?: unknown
  /** return type of the `serializeError` option callback */
  serializedErrorType?: unknown
  /** type to be returned from the `getPendingMeta` option callback & merged into `pendingAction.meta` */
  pendingMeta?: unknown
  /** type to be passed into the second argument of `fulfillWithValue` to finally be merged into `fulfilledAction.meta` */
  fulfilledMeta?: unknown
  /** type to be passed into the second argument of `rejectWithValue` to finally be merged into `rejectedAction.meta` */
  rejectedMeta?: unknown
}
```

<br/>

궁극적으로 비동기 처리함수 (payload creator)의 두번째 매개변수는 `thunkApi`이고, 여기에 `rejectWithValue`를 이용하여 리듀서에 에러 메세지를 넘겨줄 수 있는 것이다.

