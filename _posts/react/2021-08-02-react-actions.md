---
layout: post
title: redux-actions 사용하기
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## redux-actions 사용하기

---

#### 1. redux-actions 왜 사용할까?

- **createAction을 통한 액션 생성 자동화**

- **handleActions로 swtich문 대체**

  기존 방식의 reducer로 scope를 설정하는 결점을 보완

#### 2. 사용법

- **createAction**

```javascript
import {createAction} from 'redux-actions';

// 기존 액션 생성 방식
// export const increment = (index) => {
// 	type: 'INCREMENT',
//  payload: index
// };

// 1. createAction 사용
export const increment = createAction('INCREMENT');

increment(3); // { type: 'INCREMENT', payload: 3 }

// 2. parameter가 여럿일 때
export const updateColor = createAction('UPDATE_COLOR');
// action에 객체를 넣어서 parameter로 전달
updateColor({ index: 3, color: '#ffffff'});
// { type: 'UPDATE_COLOR', payload: { index: 3, color: '#ffffff' }};

// 3. parameter 명시
export const createUser = createAction('CREATE_USER', ({index, name}) => ({index, name}));

createUser({index: 1, name: 'foo'});
```

<br>

- **handleActions**

```javascript
import {handleActions} from 'redux-actions';

const reducer = handleActions({
    INCREMENT: (state, action) => ({
        counter: state.counter + action.payload
    })
}, { counter: 0 });
```

action을 매핑한 후 defaultState 설정

<br>

---

## 참고 자료

---

[Ducks 구조와 redux-actions 사용하기](https://happyer16.tistory.com/entry/4%EC%9E%A5-Ducks-%EA%B5%AC%EC%A1%B0%EC%99%80-redux-actions-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

[ReactJS - redux-actions를 이용한 더 쉬운 액션 관리](https://backback.tistory.com/316)
