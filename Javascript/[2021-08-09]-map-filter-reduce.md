# map, filter, reduce

<br>

함수형 프로그래밍에서 map, filter, reduce 함수는 정말 많이 사용한다. 아래에 작성되어 있는 `products`배열을 사용해서 코드로 직접 확인해보자!

```js
const products = [
  { name: '반팔', price: 1000 },
  { name: '후드티', price: 2000 },
  { name: '선그라스', price: 15000 },
  { name: '모자', price: 3900 },
  { name: '슬리퍼', price: 5000 },
];
```

<br>

## map

<br>

```js
// 어떤 값을 수집할 것인지는 func 함수에 완전히 위임한다.
const customMap = (func, iter) => {
  const result = [];

  for (const i of iter) {
    result.push(func(i));
  }

  return result;
};

console.log(customMap((p) => p.name, products)); // ["반팔", "후드티", "선그라스", "모자", "슬리퍼"]
console.log(customMap((p) => p.price, products)); // [1000, 2000, 15000, 3900, 5000]
```

<br>

### 이터러블 프로토콜을 따른 map의 다형성

<br>

위에 구현한 map 함수는 이터러블 프로토콜을 따르고 있기 때문에 다형성이 굉장히 높다.

```js
// 에러 발생
// 이유 : querySelectorAll로 반환되는 NodeList의 프로토타입에 map이 구현되어 있지 않음
document.querySelectorAll('*').map((el) => el.nodeName);

// 동작
// 이유 : querySelectorAll로 반환되는 NodeList가 이터러블 프로토콜을 따르기 때문
customMap((el) => el.nodeName, document.querySelectorAll('*'));

function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

// 동작
// 이유 : 제너레이터는 이터레이터를 반환하기 때문
customMap((value) => value * 2, gen());
```

<br>

이번엔 자료구조중 하나인 Map을 사용해보자!

```js
const m = new Map();

m.set('a', 10);
m.set('b', 20);

const iter = m[Symbol.iterator]();

iter.next(); // {value: Array(2), done: false}
iter.next(); // {value: Array(2), done: false}
iter.next(); // {value: undefined, done: true}

console.log(customMap(([key, value]) => [key, value * 2], m)); // [["a", 20], ["b", 40]]
```

<br>

## filter

<br>

```js
const customFilter = (func, iter) => {
  const result = [];

  for (const i of iter) {
    if (func(i)) {
      result.push(i);
    }
  }

  return result;
};

customFilter((p) => p.price > 3000, products);
customFilter(
  (n) => n % 2,
  function* () {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    yield 5;
  }
); // [1, 3, 5]
```

<br>

## reduce

<br>

```js
const nums = [1, 2, 3, 4, 5];

const customReduce = (func, acc, iter) => {
  if (!iter) {
    iter = acc[Symbol.iterator]();
    acc = iter.next().value;
  }

  for (const i of iter) {
    acc = func(acc, i); // 연산을 보조함수에게 넘긴다.
  }

  return acc;
};

customReduce((acc, cur) => acc + cur, 0, [1, 2, 3, 4, 5]); // 15
customReduce((acc, cur) => acc + cur, [1, 2, 3, 4, 5]); // 15

// reduce의 동작 원리
// const add = (a, b) => a + b;
// reduce(add, 0, [1, 2, 3, 4, 5]);
// add(add(add(add(add(0, 1), 2), 3), 4), 5); // 15
```
