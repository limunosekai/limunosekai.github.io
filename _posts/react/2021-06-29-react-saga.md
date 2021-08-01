---
layout: post
title: Redux-saga (업데이트 예정)
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## Redux-saga (업데이트 예정)

---

### 1. redux-saga란?

`redux-saga`는 리액트/리덕스 애플리케이션의 사이드 이펙트(fetching 등 비동기 동작들)를 더 쉽고 좋게 만드는 것이 목적이다. 즉, 사이드 이펙트만을 담당하는 별도의 쓰레드와 같은 것으로 ES6의 Generator를 사용하여 비동기 흐름을 동기식 자바스크립트 코드처럼 보이게 한다. redux-thunk와 다르게 액션을 모니터링하고 있다가, 액션 발생 시 특정 작업을 하는 방식으로 사용한다. redux-thunk로 하지 못하는 API 실패시 재요청, 특정 액션 발생 시 다른 액션을 디스패치하거나 자바스크립트 코드 실행, 비동기 작업시 기존 요청 취소 처리 등이 가능하다.

<br>

### 2. Generator

자바스크립트 ES6 문법으로 함수 작성 시 함수를 특정 구간에 멈춰놓거나, 원할 때 돌아가게 할 수 있다. return 값을 여러번 반환하는 것도 가능하다.

제너레이터 함수는 `function*` 키워드로 함수를 선언한다. 제너레이터 함수 호출 시 생성되는 객체가 제너레이터이다.

```javascript
function* generatorFunc() {
    console.log('Start!');
    yield 1;
    console.log('제너레이터 함수');
    yield 2;
    return 3;
}

const generator = generatorFunc();

generator.next(); // Start!, {value: 1, done: false}
generator.next(); // 제너레이터 함수, {value: 2, done: false}
generator.next(); // {value: 3, done: true}
```

<br>

제너레이터는 next 호출 시 인자를 전달하여 제너레이터 함수 내부에서 사용할 수도 있다.

```javascript
function* generatorSum() {
    console.log('Start!');
    let a = yield;
    console.log('a값 전달 성공!');
    let b = yield;
    console.log('b값 전달 성공!');
    yield a + b;
}

generator.next(); // Start!, {value: undefined, done: false}
generator.next(1); // a값 전달 성공!, {value: undefined, done: false}
generator.next(2); // b값 전달 성공!, {value: 3, done: false}
```

<br>

### 

<br>

---

## 참고 자료

---

[벨로퍼트와 함께하는 모던 리액트 - redux-saga](https://react.vlpt.us/redux-middleware/10-redux-saga.html)

[redux-saga 튜토리얼](https://mskims.github.io/redux-saga-in-korean/)



