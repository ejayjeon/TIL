date: 2022.12.27.Tue.

# :memo: 1. 화면 스크롤 맨 위로 / 맨 아래로 이동하기

## 1. 스크롤 맨 위로
```js
function backToTop() {
  window.scrollTo({
    top: 0,
    behavior: 'smooth',
  })
}
```
* '스크롤 맨 위로' 버튼을 만드는 것은 scrollTo라는 메소드를 이용하면 쉽게 만들 수 있다. 
* `top`을 **0** 으로 적용만 하면, 해당 함수 실행 시 화면이 가장 상단으로 이동한다.
* `behavior` 은 옵션이다. `'smooth'`, `'slow'` 등이 있는데, 매끄러운 UI를 위한 애니메이션 효과다. 별도로 설정해주지 않으면 애니메이션 효과 없이 바로 상단으로 이동하게 된다.

<br/><br/>

## 2. 스크롤 맨 아래로
```js
function backToBottom() {
  // window.scrollTo({ bottom: 0 }) : 안됨
  // document.body.scrollTop = document.body.scrollHeight : 안됨
  document.documentElement.scrollTop 
  = document.documentElement.scrollHeight;
}
```

```css
/* scroll-behavior 속성을 전역으로 지정함 */
html, * {
  scroll-behavior: smooth; 
}
```
* `scroollTop` 속성을 스크롤 할 때 페이지 상단의 위치값을 나타내고, `scrollHeight`는 스크롤을 포함한 해당 컨텐츠의 전체 높이값이다. 두 값을 같게 만듦으로써 페이지에서 스크롤을 가장 아래로 내려버리는 효과를 준다.
* 대다수의 블로그나 문서들을 찾아보면 `document.body` 속성을 이용하면 가능하다고 하는데, 어쩐지 나는 적용이 되지 않았다.
* 하단 블로그를 참고해서 `document.body` 대신 `document.documentElement` 속성을 이용했더니 적용됐다.
* 왜 안되는지는 하단 블로그에 기술된 내용과는 조금 상이한 듯하다. 
* 나의 경우, 가장 부모 컴포넌트에 맨 위로, 맨 아래로 버튼을 만들고, 그것이 자식 컴포넌트 어디에서든 사용할 수 있도록 이벤트가 전달이 되어야 한다. 
* 그래서 부모 컴포넌트 (함수를 사용하는 최상위 요소)를 가리키는 `document.documentElement`를 사용해야 제대로 이벤트를 발생시킬 수 있는 것이다. (`document.body`는 함수를 선언한 해당 컴포넌트만 가리킬 뿐)
* 참고 : https://jang8584.tistory.com/62 / https://w-giraffe.tistory.com/67?category=1001991