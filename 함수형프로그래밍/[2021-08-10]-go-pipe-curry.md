# go, pipe, curry

<br>

함수형 프로그래밍에서는 코드를 값으로 다루는 아이디어를 많이 사용한다. 코드를 값으로 다룰 수 있기 때문에 어떤 함수가 코드인 함수를 받아서 평가하는 시점을 원하는대로 다룰 수 있다.

<br>

```js
const products = [
  { name: '반팔', price: 1000 },
  { name: '후드티', price: 2000 },
  { name: '선그라스', price: 15000 },
  { name: '모자', price: 3900 },
  { name: '슬리퍼', price: 5000 },
];

const add = (a, b) => a + b;

customReduce(
  add,
  map(
    (p) => p.price,
    filter((p) => p.price < 3000, products)
  )
);
```

위 코드는 이전에 구현한 `customReduce`함수이다. 아직 함수 중첩이 그리 많이 되진 않았지만 이것을 조금 더 읽기편하게 바꿔보자!

<br>

## go

<br>

```js
const go = (...args) => customReduce((a, func) => func(a), args);

// 여러 인자를 받아 하나로 값을 축약해 나간다 -> 즉 reduce라고 생각할 수 있음!
go(
  0,
  (a) => a + 1,
  (a) => a + 10,
  (a) => a + 100,
  console.log
); // 111
```

<br>

## pipe

<br>

pipe는 위에 작성한 go 함수와는 다르게 함수를 리턴하는 함수이다.

<br>

```js
const go = (...args) => customReduce((a, func) => func(a), args);
const pipe =
  (f, ...funcs) =>
  (...args) =>
    go(f(...args), ...funcs);

go(
  0,
  (a) => a + 1,
  (a) => a + 10,
  (a) => a + 100,
  console.log
);

// 인자로 받은 함수들을 하나의 함수로 축약한다.
const f = pipe(
  (a, b) => a + b, // 인자를 2개받는 함수도 사용할 수 있도록
  (a) => a + 10,
  (a) => a + 100
);

console.log(f(0, 1)); // 111
```

<br>

이렇게 구현하므로써 함수를 위에서부터 아래로 읽을 수 있다.

```js
go(
  products,
  (products) => customFilter((p) => p.price > 3000, products),
  (products) => customMap((p) => p.price, products),
  (prices) => customReduce(add, prices),
  console.log
);
```

<br>

## curry

<br>

함수를 값으로 다루면서 받아둔 함수를 내가 원하는 시점에 평가시키는 함수이다.
정리해보면 함수를 받아서 함수를 리턴하고, 인자를 받아서 인자가 원하는 개수만큼 들어왔을 때 받아두었던 함수를 나중에 평가시키는 그런 함수이다.

코드를 보며 이해해보자

<br>

```js
// a, ...rest 는 func 함수에서 나중에 사용할 인자들이다.
// rest.length 가 있다면 (인자가 2개 이상이라면) 받아둔 함수 'func'를 즉시 실행하고
// 아니라면 함수를 리턴하는데 이 함수는 그 이후에 들어올 값들을 받아서 실행한한다.
const curry =
  (func) =>
  (a, ...rest) =>
    rest.length ? func(a, ...rest) : (...rest2) => func(a, ...rest2);

const mult = curry((a, b) => a * b);
console.log(mult(1)); // 인자를 1개만 전달하면 함수를 반환한다.
console.log(mult(1)(2));

const mult3 = mult(3);
console.log(mult3(9));
```

<br>

위 코드와 같이 `curry`를 구성해 볼 수 있다. (`인자를 1개만 받으면 나머지 인자를 받을 수 있도록 기다리는 것!`)
그렇다면 `customMap, customFilter, customReduce`함수를 `curry`를 사용하도록 바꿔보자

<br>

```js
const products = [
  { name: '반팔', price: 1000 },
  { name: '후드티', price: 2000 },
  { name: '선그라스', price: 15000 },
  { name: '모자', price: 3900 },
  { name: '슬리퍼', price: 5000 },
];

const curry =
  (func) =>
  (a, ...rest) =>
    rest.length ? func(a, ...rest) : (...rest2) => func(a, ...rest2);

const customMap = curry((func, iter) => {
  const result = [];

  for (const i of iter) {
    result.push(func(i));
  }

  return result;
});

const customFilter = curry((func, iter) => {
  const result = [];

  for (const i of iter) {
    if (func(i)) {
      result.push(i);
    }
  }

  return result;
});

const customReduce = curry((func, acc, iter) => {
  if (!iter) {
    iter = acc[Symbol.iterator]();
    acc = iter.next().value;
  }

  for (const i of iter) {
    acc = func(acc, i); // 연산을 보조함수에게 넘긴다.
  }

  return acc;
});

const go = (...args) => customReduce((a, func) => func(a), args);

const add = (a, b) => a + b;

go(
  products,
  (products) => customFilter((p) => p.price > 3000, products),
  (products) => customMap((p) => p.price, products),
  (prices) => customReduce(add, prices),
  console.log
);

go(
  products,
  (products) => customFilter((p) => p.price > 3000)(products),
  (products) => customMap((p) => p.price)(products),
  (prices) => customReduce(add)(prices),
  console.log
);

// 이렇게도 사용할 수 있다.
go(
  products,
  customFilter((p) => p.price > 3000),
  customMap((p) => p.price),
  customReduce(add),
  console.log
);
```

<br>

## 함수 조합으로 함수 만들기

<br>

```js
const totalPrice = pipe(
  customMap((p) => p.price),
  customReduce(add)
);

// Before : 아래 두 go 함수를 보면 customMap, customReduce가 중복으로 들어가있다.
go(
  products,
  customFilter((p) => p.price > 3000),
  customMap((p) => p.price),
  customReduce(add),
  console.log
);

go(
  products,
  customFilter((p) => p.price <= 3000),
  customMap((p) => p.price),
  customReduce(add),
  console.log
);

// After : pipe를 이용하여 totalPrice 함수에 중복으로 들어간 로직을 분리한다.
go(
  products,
  customFilter((p) => p.price > 3000),
  totalPrice,
  console.log
);

go(
  products,
  customFilter((p) => p.price <= 3000),
  totalPrice,
  console.log
);

// 또 살펴보면 customFilter, totalPrice를 분리할 수도 있을것 같다.
const baseTotalPrice = (predi) => pipe(customFilter(predi), totalPrice);

go(
  products,
  baseTotalPrice((p) => p.price > 3000),
  console.log
);
```
