---
layout: post
title: Effective Typescript - week 3 (item 26-27)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 3 (item 26-27)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item26. 타입 추론에 문맥이 어떻게 사용되는지 이해하기

```typescript
type Language = 'Javascript' | 'Typescript';

function setLanguage(language: Language) {
  console.log(language);
}

let language = 'Javascript';
setLanguage(language); //Error: 'string' 형식의 인수는 'Language' 형식의 매개 변수에 할당될 수 없습니다.
```

런타임에서는 전혀 문제가 없는 동작이지만, 함수의 매개변수로 들어갈 값을 변수로 할당하니 오류가 발생한다.

이를 해결하기 위해 language에 타입을 선언하거나 const로 선언하여 변경할 수 없음을 알려준다.

```typescript
let language: Language = 'Javascript';
setLanguage(language);
```

```typescript
const language = 'Javascript';
setLanguage(language);
```

<br>

```typescript
function calc(where: [number, number]) {}

const loc = [10, 20];
calc(loc); // Error: 'number[]' 형식의 인수는 '[number, number]' 형식의 매개 변수에 할당될 수 없습니다.
           // 대상에 2개 요소가 필요하지만, 소스에 더 적게 있을 수 있습니다.
```

튜플 사용 시 비슷한 문제가 발생하여 as const로 타입을 단언하여 해결한다.

```typescript
function calc(where: [number, number]) {}

const loc = [10, 20] as const;
calc(loc); // Error: 'readonly [10, 20]' 형식의 인수는 '[number, number]' 형식의 매개 변수에 할당될 수 없습니다.
           // 'readonly [10, 20]' 형식은 'readonly'이며 변경 가능한 형식 '[number, number]'에 할당할 수 없습니다.
```

하지만 이번엔 다른 문제가 발생한다. as const로 loc을 불변이라고 정의하였지만, calc의 매개변수인 where는 불변을 보장하지 않는다. 

```typescript
function calc(where: readonly [number, number]) {}

const loc = [10, 20] as const;
calc(loc); // 정상
```

where에 readonly 구문을 추가하여 불변을 유지하였다. 타입 오류는 발생하지 않지만, loc의 배열 길이가 3이 되면?

실제 문제는 loc에서 발생하였지만, calc 함수를 사용한 곳에서 오류가 발생하므로 오류 파악이 힘들어진다.

<br>

```typescript
function callWithCB(fn: (n1: number, n2: number) => void) {
  fn(1, 2);
}
callWithCB((a, b) => a + b);
```

a와 b는 number로 추론이 된다. 콜백을 상수로 뽑아내면 문맥이 소실되고 a, b에 any 타입이 허용되어 오류가 난다.

```typescript
function callWithCB(fn: (n1: number, n2: number) => void) {
  fn(1, 2);
}
const fn = (a, b) => a + b; // Error: 'a', 'b' 매개 변수에는 암시적으로 'any' 형식이 포함됩니다.
callWithCB(fn);
```

a와 b에 타입을 선언하여 문제를 해결한다.

```typescript
function callWithCB(fn: (n1: number, n2: number) => void) {
  fn(1, 2);
}
const fn = (a: number, b: number) => a + b; // 정상
callWithCB(fn);
```

<br>

### Item27. 함수형 기법과 라이브러리로 타입 흐름 유지하기

내장된 함수형 기법과 lodash 등을 이용하여 타입 흐름을 유지하고 가독성을 높일 수 있다.

```typescript
import _ from "lodash";

const users = [
  { id: 1, name: "v1" },
  { id: 2, name: "v2" },
  { id: 3, name: "v3" },
  { id: 4, name: "v4" },
];

const result = _.chain(users)
  .sortBy("id")
  .map((i) => {
    return i.name + " hi";
  })
  .head()
  .value();

console.log(result); // v1 hi
```

lodash도 체이닝해서 사용할 수 있다는 것을 처음 알았다..

확실히 타입 선언을 따로 안해도 알아서 추론을 해주니 편하긴 하지만, lodash를 굳이 사용해야하는지는 회사에서도 논란이 많았고, lodash 자체가 번들 사이즈도 큰 편이어서 이 부분은 좀 고민이 필요해보인다 👀

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
