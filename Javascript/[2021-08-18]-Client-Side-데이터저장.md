# Client Side에서 데이터 저장

<br>

서버에서만 데이터를 저장할 수 있는 것이 아니라 클라이언트 측에서도 데이터를 저장할 수 있는 방법이 여러가지가 있다. 그 중 몇가지에 대해 알아보도록 하자!

<br>

## Cookie

<br>

- 쿠키는 브라우저에 저장되는 작은 문자열이다.
- RFC 6265 명세서에 정의한 HTTP 프로토콜의 일부이다.
- 다른 방법들에 비해 오래된 방법이다.

<br>

**쿠키를 추가하는 방법**

```js
document.cookie = 'name=강푸름';
```

key=value의 형태로 쿠키를 추가할 수 있다. 유의할 점이 전체 쿠키에 덮어씌우는게 아니라 위에 지정한 key에 대한 쿠키가 기존쿠키에 추가되는 것이다.

<br>

**쿠키를 가져오는 방법**

```js
const cookie = document.cookie; // key1=value1;key2=value2;....
```

쿠키는 `;` 세미콜론으로 구분되는 긴 문자열 이기 때문에 사용하려면 `split` 메소드를 적절히 사용하여 원하는 쿠키값을 가져올 수 있다.

<br>

**쿠키에 유효기간 설정하기**
쿠키에 유효기간을 설정하지 않으면 브라우저를 닫는 순간 날라간다. 쿠키에 유효기간을 설정하는 방법은 2가지가 있는데 먼저 `expires`옵션부터 설명하겠다.

```js
document.cookie = 'name=kpr; expires=Wed, 20 Aug 2021 00:00:00 GMT';
```

expires 옵션에는 GMT String으로 지정해줘야 한다. (`new Date().toGMTString()`) 홈페이지에서 팝업창에 `오늘하루 보지않기`등에 사용한다.

<br>

두 번째 방법은 `max-age`옵션이다. 이 옵션은 쿠키가 생성되는 시점 기준으로 유효기간을 설정 가능하다. (단위는 1초이며, 3600일 경우 1시간이다.) max-age가 0이되면 쿠키가 삭제 된다!

```js
document.cookie = 'name=kpl; max-age=3600';
```

<br>

**쿠키사용시 주의사항**

- HTTP 요청시 헤더에 자동으로 쿠키가 보내지기 때문에 쿠키의 사이즈가 커지면 HTTP 요청 사이즈도 커진다.
- 사이즈에 제한이 있다.
- 여러가지 보안 취약점을 조심해야 한다.

<br>

## LocalStorage

<br>

- 로컬스토리지는 key, value 기반으로 Local에 데이터를 저장할 수 있는 곳이다.
- 도메인 기반으로 로컬스토리지를 공유한다.
  - 도메인이 같다면 여러탭 내에서 같은 스토리지를 공유한다.
- 직접 삭제하지 않는 이상 브라우저를 껏다 켜도 사라지지 않는다.

<br>

**로컬스토리지에 값을 저장하는 방법**

로컬스토리지에 값을 저장하는 방법은 크게 3가지가 있다.

```js
window.localStorage.name = 'kpl'; // 1번
window.localStorage['name'] = 'kpl'; // 2번
window.localStorage.setItem('name', 'kpl'); // 3번
```

이 3가지 방법중 `setItem`을 사용하는 방법이 권장된다. 그 이유는 1,2번 방법처럼 직접 property를 수정하는 방법으로 지정하면 `length, toString`과 같은 내장함수를 덮어씌울 수 있을 위험이 있기 때문이다.

<br>

**몇 가지 사용 팁**

- 로컬스토리지에는 `string` 값만 넣을 수 있기 때문에 `JSON.stringify`를 사용해 넣고 `JSON.parse`로 꺼낸 값을 파싱해서 사용하자!

```js
const user = {
  name: 'kpl',
  favoriteFood: 'Pork cutlet',
};

localStorage.setItem('user', JSON.stringify(user));

const { name } = JSON.parse(localStorage.getItem('user'));

console.log(name);
```

- 많이 변하지 않는 HTTP 요청 같은 경우 응답을 로컬스토리지에 캐싱하여 사용하기위한 목적으로 쓸수도 있다.

<br>

## Session Storage

<br>

- 전체적으로 Local Storage와 동일하다.
- 브라우저를 닫으면 저장한 내용이 초기화 된다.
- 임시값이 필요할 때 사용한다.

<br>
