---
layout: post
title: [redux] redux-observable 
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## [redux] redux-observable

---

#### 1. redux-observable이란

redux-observable는 RxJS를 기반으로 만들어진 redux의 미들웨어로,

redux의 비동기 처리 로직을 커스텀하는 것이 가능해진다.

여기서 커스텀이란 action을 가로채서 무언가 처리를 하고 다음 action을 dispatch 할 수 있다는것을 말한다.

redux-thunk, redux-saga 등에 비해 러닝 커브가 높고 굳이 이걸 써야할까? 라고 생각할 수도 있지만

~~회사에서 사용하고 있으면 알아야한다..~~

redux-observable을 사용하면 기존 api 콜을 취소하고 새로 요청을 하는 등의 작업도 가능하여 비동기 처리에서는

막강한 파워를 자랑한다.

<br>

#### 2. Epic

redux-observable에서의 핵심 기능이 아닐까 싶다.

보통 action이 발생하면 reducer에서 받아서 해당 로직을 처리한다.

이 reducer는 동기적이다. action이 dispatch되면 reducer에서 <u>동기적으로 먼저 처리를 한 후에</u>

<u>action을 구독중인 epic이 비동기 처리를 수행</u>하게 된다. (같은 action에 대해 epic이 더 나중에 시작)

epic은 action stream을 받아서 action stream을 내보내는 함수이다.

```javascript
const somethingEpic = (action$) => action$.pipe(
    ofType('SOMETHING_TYPE'),
		 switchMap((action) => {
             const { id } = action.payload || {};
             return axios.get(`api/${id}`)
             			.then((res) => somethingSuccess({data: res.data}))
             			.catch((err) => somethingFailure({error: err.response.data}))
         });
    );
```

<br>

보통 이런 식으로 쓰인다. 개념 자체가 RxJS이고 해당 Operator들도 RxJS에서 나온 것이라 자주 쓰이는 것들은 알고 있는 것이 좋다. _(ofType()은 redux-observable의 함수이다)_

react-redux 패키지의 combineReducers처럼 redux-observable 패키지의 combineEpics를 사용해서 epic도 묶어서 사용할 수 있다.

```javascript
import { combineEpics } from "redux-observable";

const epic1 = () => {
  //...
};
const epic2 = () => {
  //...
};

export const epics = combineEpics(
  epic1,
  epic2
  //...
);
```

<br>

#### 3. Inject Dependency

처음 store 생성 시 middleware를 만들어 적용해야하며 이 때, epic들이 공통적으로 사용할 dependency도 주입할 수 있다.

```javascript
import { createEpicMiddleware } from "redux-observable";

const epicMiddleware = createEpicMiddleware({
  dependencies: {
    service: something(),
  },
});

// 추후 epic에서 사용 시
const exampleEpic = (action$, state$, { service }) =>
  action$.pipe(
    ofType("EXAMPLE"),
    mergeMap((action) => service(action.payload))
    //...
  );
```

<br>

---

## 참고 자료

---

[Redux Observable 간단 정리 하기](https://pks2974.medium.com/redux-observable-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC-%ED%95%98%EA%B8%B0-68f331b10ef8)

[redux-observable 공식문서](https://redux-observable.js.org/)
