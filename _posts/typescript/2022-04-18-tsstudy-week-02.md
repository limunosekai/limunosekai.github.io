---
layout: post
title: Effective Typescript - week 2 (item 09-10)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 2 (item 09-10)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item09. 타입 단언보다는 타입 선언을 사용하기

변수에 값을 할당하고 타입을 부여하는 방법은 두 가지가 있다

```typescript
interface Person {
  name: string;
};

const foo: Person = { name: 'foo' }; // 타입 선언
const poo = { name: 'poo' } as Person; // 타입 단언
```

타입 선언은 해당 값이 인터페이스를 만족하는지 체크하지만, 타입 단언은 강제로 '이 타입이다'라고 간주하기 때문에 타입 체크를 무시한다.

```typescript
const sin: Person = {}; // Error: 'name' 속성이 '{}' 형식에 없지만 'Person' 형식에서 필수입니다.
const cos = {} as Person; // 정상
```

속성 추가시에도 적용된다. 타입 선언으로 지정한 객체에 속성 추가시, 해당 속성이 interface에 없으면 오류가 나지만, 타입 단언의 경우에는 그냥 지나간다.

<br>

화살표 함수를 사용할 때, 타입 추론으 모호할 때가 있다.

```typescript
interface MyNumber {
  number: string;
};

const num = ['1', '2', '3'].map((number) => ({ number }));
// const num: { number: string }[]
```

num의 타입이 `{ number: string }[]`이 된다. `MyNumber[]` 타입을 원한다고 다음과 같이 작성할 수 있다.

```typescript
const num = ['1', '2', '3'].map((number) => ({ number } as MyNumber));
// const num: MyNumber[]
```

위에서처럼 반환되는 값이 `{} as MyNumber`여도 타입 체크는 오류를 뱉지 않는다.

```typescript
const num: MyNumber[] = ['1', '2', '3'].map((number): MyNumber => ({ number }));
```

이렇게 `(number): MyNumber` number의 타입은 없지만 반환되는 타입을 지정한다.

`(number: MyNumber)`라고 작성하면 number의 타입은 있지만 반환 타입이 없어서 오류가 난다.

<br>

타입스크립트는 DOM에 접근할 수 없기 때문에 이런 경우에는 타입 단언이 활용된다.

```typescript
document.querySelector('.Button').addEventListener('click', (e) => {
  const button = e.currentTarget as HTMLButtonElement;
});
```

<br>

접두사에 붙이는 `!`  (확정 할당 어선셜)의 경우에도 타입 단언이라고 볼 수 있다. null이 아님이 확실할 때 사용한다. 아니면 일일이 null 타입 체크를 다 넣어야할지도?

<br>

### Item10. 객체 래퍼 타입 피하기

원시타입 string은 그 안에 메서드를 가지고 있는 것처럼 보이지만, 실제로는 String 객체로 래핑하고 메서드를 호출하고 마지막에 래핑한 객체를 버리는 식이다.

이 객체 래퍼는 자기 자신 외에는 모두 같지 않다.

```typescript
'hi' === new String('hi'); // false
new String('hi') === new String('hi'); // false
```

string은 String에 할당 가능하지만 그 반대의 경우는 성립되지 않는다.

따라서, 타입 선언 시에 잘못 타이핑하지 않도록 주의하자는 댄 형님의 조언 👊

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
