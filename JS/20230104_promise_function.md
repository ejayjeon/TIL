date: 2023.01.04.Wed.

 # :memo: 3. 자주쓰는 Async Function, Promise로 만들기

양방향 Stream 통신을 사용하기 위해 Node에서 `Socket`을 자주 이용한다. <br/>
그러다보니 통신을 할 때마다 동일한 코드를 쓰기 싫어서 반복코드를 함수로 만들고 싶어졌다. <br/>
이때, 서버와 클리이언트 간의 `Socket` 통신은 비동기로 이행되어야 한다. 비동기로 데이터를 주고받는 경우, 
 <br/><br/>

```js
// Vue 전역으로 socket 등록
this.$socket.emit(command, options, () => {})
```

```js
async emitter(command, args) {
  return new Promise((resolve, reject) => {
    try {

    } catch (error) {
      
    }
  });
},
```