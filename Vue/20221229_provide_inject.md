date: 2022.12.29.Thu.

# :memo: 2. props 말고 Provide - Inject 사용하기
[참고] <Vue 프로젝트 투입 일주일 전>

<br/>

부모 컴포넌트 > 자식 컴포넌트로 데이터를 전달할 때 `props`를 이용할 수 있다. <br/>하지만 컴포넌트 계층 구조가 복잡할 경우에는 `props` 로 전달하는 과정이 굉장히 복잡해진다.<br/>
이러한 경우에 사용하는 것이 `Provide/Inject` 다. <br/>
부모 컴포넌트 - `Provide`, 자식 컴포넌트 - `Inject` 를 각각 사용하여 데이터를 주고받을 수 있다. (Store 사용 안할 경우)

<br/>

```js
// 부모 컴포넌트
export default {
  name: 'Parent',
  data: () => ({
    items: ['A', 'B'],
  }),
  provide: () => ({
    items: this.items,
  }),
}
```

<br/>

부모 컴포넌트는 `Provide` 함수를 통해 보내고자 하는 데이터를 보낼 수 있다.

```js
// 자식 컴포넌트
export default {
  name: 'Children',
  inject: ['items'],
}
```

<br/>

자식 컴포넌트는 `Inject` 함수 안에, 부모 컴포넌트에서 보낸 데이터를 문자열 배열로 정의한다. <br/>
이렇게 `Provide/Inject`를 이용하면 아무리 컴포넌트 계층 구조가 복잡하더라도 원하는 자식 컴포넌트로 데이터를 한 번에 전달할 수 있게 된다. <br/><br/>
하지만 `inject`를 통해 데이터를 전달받는 자식 컴포넌트의 경우, 전달받는 데이터가 어떤 부모 컴포넌트에서 전달되는지 확인을 할 수 없다는 단점이 있다.