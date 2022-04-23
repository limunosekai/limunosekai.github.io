---
layout: post
title: Effective Typescript - week 2 (item 11-13)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 2 (item 11-13)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item11. 잉여 속성 체크의 한계 인지하기

```typescript
interface Wanted {
  id: number;
  name: string;
}

const me: Wanted = {
  id: 1,
  name: 'limu',
  position: 'front-end', // Error: 객체 리터럴은 알려진 속성만 지정할 수 있으며 'Wanted' 형식에 'position'이(가) 없습니다.
};

// --------------------------------

const obj = {
  id: 1,
  name: 'limu',
  position: 'front-end',
};

const i: Wanted = obj; // 정상
```

첫번째 예시에서 `잉여 속성 체크`가 수행되어 타입 Wanted에 포함되지 않은 속성이 에러로 잡혔다.

두번째 예시에서 obj 타입은 id, name, position을 모두 포함한 객체가 되고 이 객체는 Wanted 타입의 부분집합이므로 Wanted에 할당이 가능하기 때문에 타입체커를 통과했다.

`잉여 속성 체크`와` 할당 가능 검사`는 별도의 과정인 것을 알 수 있다.

<u>객체 리터럴을 변수에 직접 할당할 때</u>, 잉여 속성 체크가 수행된걸 알 수 있다.

또, 함수에 매개변수 전달 시에도 잉여 속성 체크가 수행된다.

<br>

```typescript
const o = { id: 1, name: 'limu', position: 'front' } as Wanted; // 정상
```

타입 단언문의 경우에도 역시 타입체커를 통과했다. 단언문의 사용은 지양해야된다는 예시.

<br>

### Item12. 함수 표현식에 타입 적용하기

타입스크립트에서는 함수 표현식을 사용하는 것이 좋다. 함수의 매개변수부터 반환값까지 전체를 함수 타입으로 선언해서 재사용할 수 있기 때문.

```typescript
type Fn = (num: number) => number;
const aa: Fn = num => { //... };
```

<br>

```typescript
type BinaryFn = (a: number, b: number) => number;
const add: BinaryFn = (a, b) => a + b;
const sub: BinaryFn = (a, b) => a - b;
const mul: BinaryFn = (a, b) => a * b;
const div: BinaryFn = (a, b) => a / b;
```

깔~끔 👍

<br>

### Item13. 타입과 인터페이스의 차이점 알기

유니온 타입은 가능하지만 유니온 인터페이스는 불가능하다

```typescript
type AB = 'A' | 'B';
interface aa extends AB { // Error: 인터페이스는 개체 형식 또는 정적으로 알려진 멤버가 포함된 개체 형식의 교집합만 확장할 수 있습니다.
  name: string;
}

type able = 'A' | 'B' & { name: string }; // 타입은 이렇게도 가능
```

<br>

튜플, 배열도 type으로 깔끔하게 표현이 가능하다

```typescript
type TTuple = [number, number];

interface ITuple {
  0: number,
  1: number,
  length: 2,
};
```

<br>

반면 인터페이스는 타입에 없는 기능이 있다.

```typescript
interface IState {
  name: string;
}

interface IState {
  age: number;
}

const who: IState  = {
  name: 'lim',
  age: 19,
}
```

인터페이스의 속성을 확장하는 `선언 병합`이 그 중 하나이다.

<br>

API에 대한 타입을 작성하는 경우 향후 명세가 바뀔 것을 고려한다면 선언 병합이 가능한 인터페이스로 구현하는 것이 좋다.

그러나, 프로젝트 내부에서 사용되는 타입에 선언 병합이 발생하는 것은 잘못된 설계이므로 타입, 인터페이스 잘 선택하라는 댄 형님의 조언 👊

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
