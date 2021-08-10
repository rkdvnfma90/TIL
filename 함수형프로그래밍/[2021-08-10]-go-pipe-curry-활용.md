# go, pipe, curry 활용

<br>

## HTML

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Go, Pipe, Curry 활용</title>
  </head>
  <body>
    <div id="cart"></div>
    <script src="./fx.js"></script>
    <script src="./index.js"></script>
  </body>
</html>
```

## fx.js

```js
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
    acc = func(acc, i);
  }

  return acc;
});

const go = (...args) => customReduce((a, func) => func(a), args);

const pipe =
  (f, ...funcs) =>
  (...args) =>
    go(f(...args), ...funcs);
```

## index.js

```js
const products = [
  { name: '반팔', price: 15000, quantity: 1 },
  { name: '긴팔', price: 20000, quantity: 2 },
  { name: '후드', price: 15000, quantity: 3 },
  { name: '선글라스', price: 30000, quantity: 4 },
  { name: '수영복', price: 25000, quantity: 5 },
];

const add = (a, b) => a + b;

// total 로직을 조금 더 추상화
const sum = (func, iter) => go(iter, customMap(func), customReduce(add));
const sumByCurry = curry((func, iter) =>
  go(iter, customMap(func), customReduce(add))
);

// 총 수량 : go 사용
const getTotalQuantity = (products) =>
  go(
    products,
    customMap((p) => p.quantity),
    customReduce(add)
  );

// 총 수량 : pipe 사용
const getTotalQuantity2 = pipe(
  customMap((p) => p.quantity),
  customReduce(add)
);

// 총 수량 : sum을 사용한 로직
const getTotalQuantity3 = (products) => sum((p) => p.quantity, products);

// 총 수량 : sumByCurry를 사용한 로직
const getTotalQuantity4 = sumByCurry((p) => p.quantity);

console.log(getTotalQuantity(products));
console.log(getTotalQuantity2(products));
console.log(getTotalQuantity3(products));
console.log(getTotalQuantity4(products));

// 수량 * 가격의 총 합
const getTotalPrice = pipe(
  customMap((p) => p.price * p.quantity),
  customReduce(add)
);

// 수량 * 가격의 총 합 sum을 사용한 로직
const getTotalPrice2 = (products) => sum((p) => p.price * p.quantity, products);

// 수량 * 가격의 총 합 sumByCurry를 사용한 로직
const getTotalPrice3 = sumByCurry((p) => p.price * p.quantity);

console.log(getTotalPrice(products));
console.log(getTotalPrice2(products));
console.log(getTotalPrice3(products));
```
