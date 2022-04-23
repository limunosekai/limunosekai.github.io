---
layout: post
title: Effective Typescript - week 2 (item 14-15)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 2 (item 14-15)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item14. 타입 연산과 제네릭 사용으로 반복 줄이기

extends 키워드나 인터섹션 연산자로 반복 줄이기

```typescript
interface Person {
  name: string;
  age: number;
}

interface PersonWithBirth extends Person {
  birth: Date;
}
type PersonWithDate = Person & { birth: Date };
```

<br>

제네릭 타입 사용하기

* Pick

```typescript
interface State {
  id: string;
  title: string;
  files: string[];
  content: string;
}

type NavState = Pick<State, 'id' | 'title' | 'content'>; // { id, title, content }
```

```typescript
interface SaveAction {
  type: 'save'
}
interface LoadAction {
  type: 'load'
}
type Action = SaveAction | LoadAction;
type ActionType = Pick<Action, 'type'>; // { type: 'save' | 'load' }
```

* Partial

```typescript
interface Address {
  email: string;
  address: string;
}
type MyEmail = Partial<Address>; // email?, address? 선택적으로 가질 수 있다, 다 없어도 됨
```

```typescript
interface Product {
  id: number;
  price: number;
  title: string
}

interface updateProduct { // 잘못된 예시
  id?: number;
  price?: number;
  title?: string;
}

function update(item: Partial<Product>) { // id, price, title 어떤 것이든 인자로 받을 수 있다
  // ...
}
```

* Omit

```typescript
interface Product {
  id: number;
  title: string;
  price: number;
  desc: string
}

type item = Omit<Product, 'desc'>; // { id, title, price }만 가진다
```

* ReturnType

```typescript
function getUserInfo(userId: string) {
  // ...
  return {
    userId,
    name,
    age,
    color,
  };
}

typeof UserInfo = ReturnType<typeof getUserInfo>;
```

<br>

타입 연산자 사용하기

* typeof

```typescript
const OPTION = {
  width: 640,
  height: 480,
  color: '#FFF',
  label: 'label'
};

type Options = typeof OPTION; // { width, height, color, label }
```

<br>

### Item15. 동적 데이터에 인덱스 시그니처 사용하기

타입에  `인덱스 시그니처`를 사용하여 유연하게 매핑할 수 있다

```typescript
type Product = { [property: string]: string };
const item: Product = {
  category: "스낵",
  label: "과자",
};
```

`[키의 이름: 키의 타입]: 값의 타입`으로 사용된다

어떤 이름이건 키의 이름으로 들어갈 수 있고, 키 마다 다른 타입의 값을 가질 수 없다.

또, 자동완성 기능을 제공받지 못하는 단점이 있다.

런타임 때까지 객체의 속성을 알 수 없을 경우에만 인덱스 시그니처를 사용하자.

가능하다면 정확한 타입을 사용하라는 댄 형님의 조언 👊

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
