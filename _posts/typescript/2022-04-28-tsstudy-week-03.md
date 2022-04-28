---
layout: post
title: Effective Typescript - week 3 (item 24-25)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 3 (item 24-25)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item24. 일관성 있는 별칭 사용하기

> 별칭은 타입 좁히기를 방해하므로 별칭 사용 시 일관되게 사용해야한다

```typescript
interface Axis {
  x: number;
  y: number;
}

interface Flag {
  x: [number, number];
  y: [number, number];
}

interface Coordinate {
  log: Axis[];
  lan?: Flag;
}

function calc(coor: Coordinate) {
  const flag = coor.lan;
  if (coor.lan) {
    const temp = flag?.x[0] + flag?.y[0]; // Error: 개체가 'undefined'인 것 같습니다.
  }
}
```

`coor.lan`으로 잘 동작하던 코드가 flag라는 별칭을 만들어 사용하는 순간 편집기에 오류가 표시된다.

flag와 coor.lan의 타입은 `Flag | undefined`가 되기 때문이다.

if문의 체크를 다음과 같이 바꿔주면 '별칭은 일관성 있게 사용한다'는 원칙을 준수하기에 오류가 없어진다.

```typescript
function calc(coor: Coordinate) {
  const flag = coor.lan;
  if (flag) {
    const temp = flag?.x[0] + flag?.y[0]; // 정상
  }
}
```

굳이 별칭을 쓰지 않고 비구조화 할당을 사용하여 더 간결하게 표기한다.

```typescript
function calc(coor: Coordinate) {
  const { lan } = coor;
  if (lan) {
    const { x, y } = lan;
  }
}
```

주의할 점은 x와 y가 선택적 속성일 경우 null 속성 체크가 필요하다.

<br>

### Item25. 비동기 코드에는 콜백 대신 async 함수 사용하기

> 콜백보다 프로미스를 사용할 경우, 타입스크립트가 추론을 더 잘한다

```typescript
async function getItem() {
  const [res1, res2, res3] = await Promise.all([
    fetch("1"),
    fetch("2"),
    fetch("3"),
  ]);
}
// res1, res2, res3 Type: Response
```

<br>

> 함수가 프로미스를 반환한다면, async로 선언하는 것이 좋다

```typescript
async function getData(url: string): Promise<any> {
  const res = await fetch(url);
  const json = await res.json();
  return json;
}
```

async 함수에서 프로미스 반환 시, 또 다른 프로미스로 래핑되지 않는다.

`Promise<Promise<T>>`가 아닌 `Promise<T>`가 된다.

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
