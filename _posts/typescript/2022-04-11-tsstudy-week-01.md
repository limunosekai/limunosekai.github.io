---
layout: post
title: Effective Typescript - week 1 (item 01)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 1 (item 01)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item01. 타입스크립트와 자바스크립트의 관계 이해하기

_duck typing : 동적 타이핑의 한 종류로, 객체의 변수 및 메소드의 집합이 객체의 타입을 결정하는 것_

> 자바스크립트는 타입스크립트의 부분집합이다

타입스크립트는 컴파일도 자바스크립트, 실행도 자바스크립트로 이루어진다.

타입스크립트는 자바스크립트의 상위집합이다. 문제가 없는 자바스크립트 코드는 유효한 타입스크립트 코드라고 볼 수 있다.

.js 확장자를 가진 파일은 이미 타입스크립트라 할 수 있다. 확장자를 .ts라 바꾸더라도 달라지는 것이 없다.

하지만, 그 반대는 성립하지 않는다. 타입스크립트라고 해서 모두 자바스크립트라는 명제는 거짓이다. 타입스크립트가 타입을 명시하는 추가적은 문법을 가지기 때문.

타입스크립트 컴파일러는 자바스크립트에서도 돌아간다.

<br>

> 타입스크립트는 정적 타입 시스템이다

타입스크립트는 런타임에 발생할 오류를 미리 찾아낸다. 타입 체커가 모든 오류를 찾아내지는 않지만, 오탈자와 같은 문제를 찾아내기도 한다.

```typescript
const options = [
  {
    name: "foo",
    age: "11",
  },
  {
    name: "foo2",
    age: "13",
  },
  {
    name: "bar",
    age: "25",
  },
];

for (const option of options) {
  console.log(option.agee); // <--- Error
}
```

![스크린샷](/assets/post/typescript/2022-04-11-01.png)

하지만, 어떤게 오타인지까지 구분할 수 없기 때문에 명시적으로 타입구문을 적는 것이 옳다

<br>

> 타입스크립트 타입 시스템은 자바스크립트의 런타임 동작을 모델링한다

자바스크립트에서 문제가 없는 코드도 타입스크립트에서는 타입 체커에 의해 오류가 표시될 수 있다.

```typescript
const a = null + 7; // <-- 자바스크립트 O, 타입스트립트 X
```

또, 타입 체커를 통과해도 런타임에서 오류가 나는 코드도 있다.

이들의 근본적인 원인은 타입스크립트가 이해하는 값의 타입과 실제 값이 다르기 때문이다.

타입스크립트를 사용하면 확실히 오류가 적은 코드를 작성할 수 있지만, 완벽히 사용자의 생각대로 동작하는 것은 아니기 때문에 도입할지 말지는 사용자 나름~

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음