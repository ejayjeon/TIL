Date: 2023.01.10.Tue.

 # :memo: 4. JWT 사용하기

## 1. crypto 모듈 
JS에 내장된 `crpyto` 모듈을 통해 JSON 형태로 작성된 객체를 암호화할 수 있다.

```js
const crypto = require('crypto')
```

<br>

## 2. Base64 인코딩
```js
function objToBase64(json) {
  // 1. 인자로 받은 json 객체를 Strng화
  const stringified = JSON.stringify(json);

  // 2. String화된 json 객체를 Base64로 인코딩
  const base64Encoded = Buffer.from(stringified).toString('base64')

  // 3. = or == 제거
  const paddingRemoved = base64Encoded.replaceAll('=', '');

  return paddingRemoved;
}
```
json 객체를 인코딩하다보면 간혹 `=` 혹은 `==` 문자열이 포함되어서 나온다. 이것을 제거하지 않으면 URL을 신뢰할 수 없게 되므로 제거해야 한다.<br/>
`replaceAll` 메소드는 node 버전이 15이상부터 실행가능하며 ES2021에서 공식스펙으로 포함되었다.

<br>

## 3. JWT 몸통 만들기
JWT의 형태를 `header`.`payload`.`signature` 형태로, 두 개의 온점 사이로 클라이언트에서 보낸 정보를 담고 있다.
```js

// 1. Header
const header = {
  alg: 'HS256', // 암호화 알고리즘 (HMAC SHA256)
  typ: 'JWT' // 형태 -> JWT
}

// 2. Payload
const payload = {
  email: 'test@email.com',
  password: 'test1234',
  isAdmin: true,
}

 // 각각 base64로 인코딩
const encodedHeader = objToBase64(header)
const encodedPayload = objToBase64(payload)

// 3. Signature
const signature = crypto
.createHmac('sha256', 'secret_key') // secret_key는 설정가능
.update(`${encodedHeader}.${encodedPayload}`)
.digest('base64')
.replaceAll('=', '')
```
인코딩된 header와 payload를 온점으로 이어 붙인 것은 `sha256` 알고리즘을 사용하여 `HMAC` 으로 암호화한다. <br/>
> `HMAC(Keyed-hash Message Authentication Code)` 이란, 메세지 인증 코드의 한 유형으로 특정 Key와 함께 특정 메세지를 Hash 값으로 만드는 암호화 방식이다. 공격자로 하여금 레인보우 테이블 기법의 해킹을 어렵게 하기 위해 원문과 함께 비밀키를 더해 해싱하는 방법이다.

<br/>

## 4. JWT 조합하기
```js
const jwt = `${encodedHeader}.${encodedPayload}.${signature}`
```

<br/>

## 5. 검증하기
[jwt.io](jwt.io) 에서 JWT가 유효한지 검증할 수 있다.

<br/>

최근 서비스들은 동시에 Access Token과 Refresh Token을 함께 사용하는 방식으로 사용자를 인가한다. Access Token은 보통 5분 내지 24시간 이내로 기간이 짧아서, 클라이언트는 Access Token과 함께 발급된 Refresh Token을 대조하여 새로운 Access Token으로 재발급한다. 