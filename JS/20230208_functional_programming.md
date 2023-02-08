Date: 2023.02.08.Wed.

 # :memo: 5. 함수형 프로그래밍

## 1. 함수형 프로그래밍
<br/>
최근 애플리케이션이나 사용자의 눈높이가 높아짐에 따라 요구되는 기술도 늘어나지만, 그에 못지 않게 생산성이 중요하다. 제작한 기능을 평가시점이 언제이든지 상관없이 활용할 수 있을 때 해당 프로그래밍의 생산성도 높아지기 마련이다.

함수형 프로그래밍은 `순수함수`를 기준으로 프로그래밍을 하는 방식을 말한다. 순수함수란 언제 사용하든지 항상 동일한 결과를 리턴하는 함수를 말한다. 외부의 상태가 함수 결과에 영향을 끼치지 못하며, 순수하게 함수 내부에 정의한 로직에 의해서만 값이 리턴된다는 특징이 있다.

순수함수 자체를 정의하여 사용할 수도 있고, 이 함수를 다른 함수의 값으로 받거나 함수의 인자로 넘겨, 함수 안에서 클로저로 구성할 수도 있다.

<br/><br/>

## 2. 함수형 프로그래밍 vs. 객체지향 프로그래밍

프로그래밍을 하는 방식으로 어떤 게 좋고 어떤 게 나쁘다는 절대적인 기준은 없다. 함수형 프로그램은 `함수`를 먼저 만들고 그 함수에 맞춰서 데이터셋을 구성하는 프로그래밍이고, 객체지향 프로그래밍은 `데이터(객체)`를 기준으로 데이터를 먼저 디자인하고, 그 데이터에 맞는 메소드를 구성하는 프로그래밍이다. 다음 코드를 보면 두 프로그래밍의 차이점을 알 수 있다.

<br/>

```js
/* 함수형 프로그래밍 : 함수 기준 */
moveLeft(dog);
moveRight(dog);

/* 객체지향 프로그래밍 : 데이터(객체) 기준 */
dog.moveLeft();
dog.moveRight();
```

<br/><br/>

## 3. _filter, _map 함수로 다형성은 높이고 중복을 제거하기

```js
// list
var users = [
  { id: 1, name: 'John', age: 21 },
  { id: 2, name: 'Annah', age: 30 },
  { id: 3, name: 'Mike', age: 32 },
  { id: 4, name: 'Jemma', age: 34 },
  { id: 5, name: 'Della', age: 20 },
  { id: 6, name: 'Rouis', age: 17 },
  { id: 7, name: 'Stella', age: 50 },
  { id: 8, name: 'Grace', age: 37 },
  { id: 9, name: 'Jeniffer', age: 28 },
];

// 1. 일반적인 명령형 코드
// 1-1. users 목록에서 30세 이상인 사람 고르기
var temp_users = [];
for (var i = 0; i < users.length; i++) {
  if (users[i].age >= 30) {
    temp_users.push(users[i]);
  }
}

// 1-2. 30세 이상인 사람의 이름만 수집하기
var names = [];
for (var i = 0; i < temp_users.length; i++) {
  if (users[i].age >= 30) {
    names.push(temp_users[i].name);
  }
}
```

함수형 프로그래밍 없이 명령형 코드로 데이터를 출력하면 위와 같은 형식이 된다. `for`문을 돌리는 조건과, 수집하는 리스트가 중복이며 해당 코드는 다른 구성으로는 사용할 수가 없다.

<br/>

이 코드에서 중복을 제거하고 다형성을 높이기 위해 `고차함수`를 만들어 다용도로 활용할 수 있다. 코드는 아래와 같다.

```js
// 2. 함수형 프로그래밍
// filter 함수
// 1. 인자로 필터링하고자 하는 리스트와, for 문을 수행할 함수를 받는다
// 인자로 받은 pred 함수에 평가하고자 하는 조건을 위임한다 : 필터링
function _filter(list, pred) {
  var new_list = [];
  for (var i = 0; i < list.length; i++) {
    if (pred(list[i])) {
      new_list.push(list[i]);
    }
  }
  return new_list;
}

// map 함수
// 1. 인자로 리스토와 mapper 함수를 받는다
// mapper 함수에 평가 조건을 위임한다, _filter 와 다른 점은, _filter는 조건을 검사하는 항목이 있다는 것
function _map(list, mapper) {
  var new_list = [];
  for (var i = 0; i < list.length; i++) {
    new.list.push(mapper(list[i]));
  }
  return new_list;
}


// 2-1. users 목록에서 30세 이상인 사람 고르기
var over30 = _filter(users, function(user) { return user.age >= 30;});
// 2-2. 30세 이상인 사람의 이름만 수집하기
var name = _map(over30, function(user) { return user.name; });
```

<br/><br/>

## 4. each : 인자로 받은 리스트를 순회
```js
function _each(list, iter) {
  for (var i = 0; i < list.length; i++) {
    iter(list[i]);
  }
  return list;
}
```

위 `_filter`, `_map` 함수에서도 for loop 중복을 제거할 수 있다. 이것을 each 함수를 통해 수행한다.

```js
// 1. _filter 리팩토링
function _filter(list, pred) {
  var new_list = [];
  _each(list, function (val) {
    if (pred(val)) {
      new_list.push(val);
    }
  });
  return new_list;
}

// 2. _map 리팩토링
function _map(list, mapper) {
  var new_list = [];
  _each(list, function (val) {
    new_list.push(mapper(val));
  });
  return new_list;
}
```
