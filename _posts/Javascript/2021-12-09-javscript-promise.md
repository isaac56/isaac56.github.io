---
layout: post
title:  "javascript promise 란"
excerpt: ""

categories:
  - Javascript
tags:
  - [Javascript]

toc: true
toc_sticky: true
comments: true
 
date: 2021-12-09
last_modified_at: 2021-12-09
---
## 비동기 처리

비동기 처리가 뭘까?

비동기의 영단어인 Asynchronous는 "동시에 존재하는" 이란 뜻의 synchronous 에 부정의미인 A가 붙은 단어이다.

단순 직역하면 "동시에 존재하지 않는" 이라고 할 수 있다.

이게 사전 의미 그대로 이해하면 조금 헷갈릴 수 있는데 나는 다음과 같이 이해했다.

```
A라는 함수가 B라는 함수를 호출할 때를 가정해보자, 

A가 B의 존재를 알아서 B가 수행되는동안 멈췄다가(안 멈춰도 상관없다) 끝나는 시점에 다시 본인 일을 시작한다면, *A는 B의 존재를 인지*하고 있기때문에 그 두개는 동시에 존재한다(synchronous)고 할 수 있다.

반대로 A가 B를 실행시키고 나서 B의 존재를 몰라서 B의 수행여부와 관계없이 본인의 일을 한다면, *A는 B의 존재를 모르기*때문에 동시에 존재하지 않는다고(Asynchronous)라고 할 수 있다.
```
- 참고 - [Synchronous vs Asynchronous vs Blocking vs Non-Blocking](https://isaac56.github.io/java/2021/01/25/Sync_Async_Blocking_NonBlocking/)

그렇다면 비동기 처리가 왜 필요할까?

예를 들어, 웹페이지에 필요한 각종 이미지나 리소스들을 로딩하는동안 모든 페이지가 먹통이 된다면 엄청 답답할 것이다.

이런 경우에 각종 리소스를 불러오는 작업을 비동기로 처리한다면 우리는 안답답할 수 있다.

이렇게 동시에 어떤 일을 처리해야할때 비동기로 처리할 필요가 있다.

>  java같이 우리(?)와 친숙한 언어는 쓰레드를 생성하여 비동기처리를 하곤 한다.
>
> 앞으로 얘기하려는 javascript는 단일 스레드에서 이벤트루프를 사용하여 비동기를 지원하는데 이는 다음에 공부해서 다뤄보겠다.

## 콜백 지옥

자 이제 비동기 처리를 하면서 생겨난 콜백 지옥을 알아보자.

비동기처리를 한 후에 그 결과를 받아서 뭔가를 처리하고 싶을 때, 비동기함수를 호출한 A는 그 결과를 받지 못한다.

왜냐면 A는 B를 호출하고 본인 갈 길을 가기 때문에 B가 뭔가를 전달할수가 없다.

그래서 A는 B를 호출할 때 C라는 콜백함수를 같이 전달하는데, B는 본인 할일이 끝나면 필요한 정보를 C(콜백)에 인자로 전달해서 호출한다.

하지만 꽤 많은 순차적인 함수가 필요하다면 어떻게 될까?

A는 B에게 C라는 콜백함수를 넘기는데, B는 C를 비동기적으로 호출하면서 C에게 D라는 콜백함수를 넘기고, C는 D를 비동기적으로 호출하면서 E라는 콜백함수를 넘기고....

예를 들어 코드로 표현하면 아래와 같다.

```javascript
$.get('url', function(response) {
	parseValue(response, function(id) {
		auth(id, function(result) {
			display(result, function(text) {
				console.log(text);
			});
		});
	});
});
```

이렇게 콜백이 계속 필요하면서 depth도 깊어지고, 가독성도 낮아지는 현상을 콜백 지옥이라고 한다.

## Promise 로 콜백 지옥 탈출하기

위에서 비동기 처리가 필요하고, 비동기 처리하다보면 콜백지옥이라는 상황과 마주하게 되었다.

그럼 비동기 처리는 하되, 콜백지옥 없이 하면 되지 않을까?ㅋㅋ 그러기 위해 나온것이 Promise 객체이다

콜백 지옥을 피하고 가독성을 높이되 똑같은 동작을 실행할 수 있다.

위의 콜백지옥 예시를 promise를 사용하면 다음과 같이 바꿀 수 있다.

```javascript
const A = new Promise(() => {
  $.get('url', function(response) {
    reslove(id);
  });
});

// 위의 parseValue, autho, display 는 2번째 인자인 콜백함수에 값을 전달하겠지만, 
// 여기선 다음 콜백에 전달하던 값을 return 한다고 가정
A.then(parseValue)
  .then(auth)
  .then(display)
  .then(function(text) {
  	console.log(text);
	})
```

A()를 호출한 후에 then을 순서대로 호출하는 것을 보면 콜백지옥에 비해 정말 좋고 편하다.

promise 객체를 통해 매번 콜백함수를 넘기던 것을, 마치 익숙한 동기적 호출처럼 보이게 만들 수 있게 되었다.

이제 Promise의 기본적인 상태, 상태, 호출 방법을 알아보자.

## Promise의 3가지 상태

프로미스란 객체는 기본적으로 3가지의 상태를 갖는다.

- Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
- Fullfiled(이행) : 비동기 처리가 완료되어 프로미스가

- Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

### Pending(대기)

```javascript
new Promise((resolve, reject) => {
  something...
});
```
로 객체를 생성하면 안의 something을 즉시 실행하게되는데,

something안에서 resolve() 호출하기 전까지를 Pending 상태라고 한다.

즉 어떤 수행을 완료하는 시점을 resolve를 호출하는 시점으로 생각하고, 그 전은 모두 Pending 상태가 된다.

(위의 something...안에서 resolve를 호출하지 않으면 영원히 pending 상태가 된다.)

### Fullfilled(이행)

위의 something에서 resolve를 호출하게 되면 이행상태가 된다.

즉 resolve를 호출하는 시점에 해당 프로미스가 할 일을 끝냈다고 생각하는 것이다.

이 때, resolve에 넘긴값은 then에서 받을 수 있다.

> 하지만 something안에서 resolve를 호출해도 그 아래의 수행들은 실행된다.
>
> 또 하지만, resolve를 호출한 뒤에 다시 resolve를 호출해도 then에 넘기는 값은 처음 resolve에 넘긴 값으로 고정된다.



``` javascript
const test = new Promise((resolve, reject) => {
  console.log('1');
  resolve('result1');
  console.log('2');
  resolve('result2');
})

// 출력결과:
// 1
// 2

// test 객체의 상태:
// "Promise {<fulfilled>: 'result1'}"
test.then((result) => {console.log(result);})
// 출력결과:
// result1
```

### Rejected(실패)

resolve를 호출하는 것처럼, reject를 호출하면 Rejected 상태가 된다.

이 때, reject로 넘긴 값은 .then().catch() 에서 받을 수 있다.

```javascript
const test = new Promise((resolve, reject) => {
  console.log('1');
  reject(new Error('result1'));
  console.log('2');
  reject(new Error('result2'));
})

// 출력결과:
// 1
// 2

// test 객체의 상태:
// "Promise {<rejected>: Error: result1)"
test
  .then((result) => {console.log(result);})
  .catch((e) => {console.log(e);});
// 출력결과:
// Error: result1
```

## Promise 사용법

기본 사용법은 위의 예제에서 느낌이 왔을 것이다.

1. Promise 객체를 생성하면서 ```resolve, reject 두개 함수를 인자로 받는 함수``` 를 함수로 넣어준다.
2. .then과 .catch 인자로 비동기 완료 또는 에러 시 수행할 함수 넣어준다.

근데 위에서 then을 연속으로 사용하는 것을 봤을 것이다.

결과적으로 then을 chaining하면, 1) 결과 받고 처리하고 2) 다음 then으로 결과 넘기고 를 순서대로 처리할 수 있다.



### (궁금증) then으로 어떻게 chaining이 가능할까?

근데 이게 어떻게 가능할까?

promise.then()은 다시 promise를 반환하기 때문이다.

또한 new Promise에선 resolve에 값을 넣어 then으로 그 값을 넘겼지만, then() 내부의 함수에선 return 하게되면 해당 값이 다음 then으로 넘어가고 그 promise는 fullfilled 상태가 된다.

즉, then()으로 promise를 생성할 땐 내부 함수의 return이, new Promise() 내부 함수의 resolve와 같은 것이다.

```javascript
const promise1 = new Promise((resolve, reject) => {
    resolve('삭삭');
});

const promise2 = promise1.then(() => {
  return '삭삭';
});

// promise2는 promise1 이후에 생성되는 것이긴 하지만 이는 then을 호출하기 위해 promise 객체가 필요했을 뿐,
// promise1이나 promise2는 다음 then에 '삭삭'이라는 값을 넘긴다는 점에서 똑같은 수행을 한다.
```

직접 then으로 promise를 생성해보면서 확인해보았는데 비슷한 궁금증 가졌던 사람들에게 조금이라도 도움이 되었으면 좋겠다.

 

