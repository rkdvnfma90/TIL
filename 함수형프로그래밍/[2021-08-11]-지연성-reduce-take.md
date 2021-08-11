# reduce와 take의 활용

<br>

객체로 부터 쿼리스트링을 만들어내는 `queryStr`함수

```js
const queryStr = (obj) =>
  go(
    obj,
    Object.entries,
    customMap(([k, v]) => `${k}=${v}`),
    customReduce((a, b) => `${a}&${b}`)
  );

const queryStr2 = pipe(
  Object.entries,
  customMap(([k, v]) => `${k}=${v}`),
  customReduce((a, b) => `${a}&${b}`)
);

console.log(queryStr({ limit: 10, offset: 10, type: 'notice' })); // limit=10&offset=10&type=notice
console.log(queryStr2({ limit: 10, offset: 10, type: 'notice' })); // limit=10&offset=10&type=notice
```

<br>

`Array.prototype.join` 보다 다형성이 높은 `join` 함수

```js
const join = curry((seperator = ',', iterable) =>
  customReduce((a, b) => `${a}${seperator}${b}`, iterable)
);

const queryStr3 = pipe(
  Object.entries,
  customMap(([k, v]) => `${k}=${v}`), // L.map(([k, v]) => `${k}=${v}`) 도 동일하게 동작함
  join('&')
);

console.log(queryStr3({ limit: 10, offset: 10, type: 'notice' })); // limit=10&offset=10&type=notice
```

<br>

iterable에서 조건에 맞는 요소 1개를 반환하는 `find` 함수

```js
const users = [
  { age: 32 },
  { age: 31 },
  { age: 22 },
  { age: 27 },
  { age: 37 },
  { age: 51 },
  { age: 12 },
];

const find = (func, iterable) =>
  go(iterable, customFilter(func), take(1), ([a]) => a); // L.filter 사용하여 최적화 가능!

console.log(find((u) => u.age < 20, users));
```
