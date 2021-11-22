# CRA로 구성된 프로젝트에 emotion 얹기

<br>

먼저 emotion을 사용하기 위해선 babel에 별도 설정을 해줘야 한다.
하지만 CRA를 사용하여 리액트 프로젝트를 만들면 `.babelrc` 같은 설정파일이 먹히지 않기 때문에 `eject`를 해야 한다.

하지만 `eject`를 하는것 자체는 쉽지만 그 이후 해야할 일이 더 많아질 수 있기 때문에 `craco`라는 라이브러리를 사용하여 좀 더 간단하게 세팅할 수 있다.

`create-react-app-config-override 의 약자이다!`

<br>

```cmd
npm i -D @craco/craco
npm i -D @emotion/babel-preset-css-prop
```

<br>

craco를 설치했다면 프로젝트의 루트 경로에 아래와 같이 `craco.config.js` 파일을 구성해야 한다.

<br>

```js
module.exports = {
  babel: {
    presets: ['@emotion/babel-preset-css-prop'],
  },
};
```

<br>

설정을 마쳤으면 `package.json`의 `scripts`부분을 기존 `react-scripts` 에서 `craco`로 변경해주자!
