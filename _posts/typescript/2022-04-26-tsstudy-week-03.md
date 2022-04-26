---
layout: post
title: Effective Typescript - week 3 (item 22-23)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 3 (item 22-23)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item22. 타입 좁히기

타입 좁히기는 넓은 타입으로부터 좁은 타입으로 진행하는 과정이다. 주로 if문의 null 타입 체크

```typescript
const el = document.querySelector('.foo'); // Type: HTMLElement | null
if (el) { // Type: HTMLElement
  //...
} else { // Type: null
  //...
}
```

if 문의 첫 번째 블록에서 null을 제외하므로 타입이 좁혀졌다.

자바스크립트에서는 `typeof null === 'object'`이므로 주의할 것.

```typescript
const el = document.querySelector('.foo'); // Type: HTMLElement | null
if (typeof el === 'object') { // Type: HTMLElement | null??
  //...
}
```

마찬가지로 빈 문자열 '' 과 0 모두 false이므로 타입좁히기 시 주의해야한다.

<br>

타입을 좁히기 위해 명시적으로 태그를 사용할 수 있다. 태그된 유니온 | 구별된 유니온이라 부른다.

```typescript
interface Upload {
  type: 'upload';
  filename: string;
  contents: string;
}
interface Download {
  type: 'download';
  filename: string;
}
type AppEvent = Upload | Download;
function handleEvent(e: AppEvent) {
  switch (e.type) {
    case 'upload':
      //...
    case 'download':
      //...
  }
}
```

<br>

타입스크립트의 타입 식별을 돕기 위해 '타입 가드'를 사용할 수 있다.

```typescript
const wanted = ["frontEnd", "backEnd", "devOps"];
const departments = ["HR", "frontEnd"].map((a) => wanted.find((n) => n === a));
// departments Type : const departments: (string | undefined)[]
```

여기서 undefined를 없애려해도 잘 안될 때, 타입가드를 사용한다.

```typescript
function isDefined<T>(x: T | undefined): x is T {
  return x !== undefined;
}
const departments = ["HR", "frontEnd"]
	.map((a) => wanted.find((n) => n === a))
	.filter(isDefined);
// departments Type : const departments: string[]
```

<br>

### Item23. 한꺼번에 객체 생성하기

객체를 생성할 때, 여러 속성을 넣어서 한 번에 생성해야 타입 추론에 유리하다.

```typescript
const pt = { x: 1, y: 2 };
const db = { id: '123' };
const obj = {};
Object.assign(obj, pt, db);
obj.id; // Error: '{}' 형식에 'id' 속성이 없습니다.
```

spread 연산자를 사용하면 타입 오류 없이 객체를 생성할 수 있다.

```typescript
const pt = { x: 1, y: 2 };
const db = { id: "123" };
const obj = { ...pt, ...db };
// obj Type : const obj: { id: string; x: number; y: number; }
```

타입에 안전한 조건부 속성을 추가하려면 빈 객체 {} 또는 null을 이용한다.

```typescript
declare let isOk: boolean;
const term = { first: 'lim' };
const obj = { ...term, ...(isOk ? { second: 'two' } : {})};
// obj Type : const obj: { second?: string | undefined; first: string; }
```

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
