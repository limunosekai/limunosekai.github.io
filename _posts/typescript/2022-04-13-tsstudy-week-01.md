---
layout: post
title: Effective Typescript - week 1 (item 04-05)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 1 (item 04-05)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item04. 구조적 타이핑에 익숙해지기

> 타입은 봉인되어 있지 않다 or 타입은 열려있다

자바스크립트는 덕 타이핑 기반으로 타입스크립트는 이를 모델링하여 구조적 타이핑을 사용한다.

```typescript
interface Vector2D {
  x: number;
  y: number;
}

function calcLength(v: Vector2D) {
  return Math.sqrt(v.x * v.x + v.y * v.y);
}

interface NamedVector {
  name: string;
  x: number;
  y: number;
}

const v: NamedVector = { x: 3, y: 4, name: "foo" };
console.log(calcLength(v));  // 5
```

NamedVector의 구조가 Vector2D에 호환되기 때문에 NamedVector만을 위한 별도의 함수를 만들지 않아도 calcLength를 호출할 수 있다. 이를 **구조적 타이핑 (structural typing)**이라고 한다.

이 구조적 타이핑으로 문제가 발생하기도 한다. 

Vector2D만을 받기를 원했지만 x, y와 다른 값들을 가진 객체를 인자로 전달했을때 타입 체커는 오류를 뱉지 않는다. 

일반적으로 함수 작성 시, 인자로 들어올 값이 파라미터의 타입에 선언된 속성만을 가질거라 생각하지만, <u>타입은 열려있다.</u>

*봉인된 타입 혹은 정확한 타입을 위한 타입스크립트 설정도 가능하긴 하다.*

타입이 열려있기 때문에 예기치 못한 에러와 만날 수도 있다.

```typescript
interface Vector3D {
  x: number;
  y: number;
  z: number;
}

function calcLength2(v: Vector3D) {
  let length = 0;
  for (const axis of Object.keys(v)) {
    const coord = v[axis]; // <-- Error: 'string' 형식의 식을 'Vector3D' 인덱스 형식에 사용할 수 없으므로 요소에 암시적으로 'any' 형식이 있습니다.
    length += Math.abs(coord);
  }
  return length;
}

const v2 = { x: 3, y: 4, z: 1, name: "string" };
console.log(calcLength2(v2));
```

v는 Vector3D의 인터페이스로 들어오기 때문에 x, y, z만 가질거라 생각하고 함수를 작성했지만, 타입은 열려있기에 v2 처럼 x,y,z를 모두 가지고 다른 타입도 가진 객체를 던졌을 때, 문제가 발생할 수 있다. 이를 타입스크립트가 미연에 잡아준 것 !

위 코드를 정확히 하기 위해선 모든 키를 루프를 돌기보단 정확히 필요한 값만 꺼내어 쓰는 것이 좋다.

타입스크립트 사용시 주의할 점은 늘어나겠지만, 결국 런타임 에러를 방지할 수 있다는 점이 강려크하긴 한듯 😇

<br>

### Item05. any 타입 지양하기

프로토타입 코드 작성시, 일단 any로 작성하고 나중에 바꿔야지?? 라고 생각하여 any를 써두거나 타입을 지정하기 귀찮아서 as any로 타입을 단언해버리거나...

이처럼 any는 아주 편리한것처럼 보이지만, 편리한만큼 지게 될 리크스도 높아진다는 점을 생각해야한다.

> any 타입에는 타입 안정성이 없다

```typescript
let mustNumber: number;
mustNumber = "가나다" as any;
mustNumber += 1;
console.log(mustNumber); // 가나다1 ..?!?!
```

이렇듯 코드는 점점 산으로 가게 되고...

<br>

> any는 함수 시그니처를 무시해버린다

다시 말해, 함수의 파라미터로 특정 타입의 인자를 받고 특정 타입의 값을 반환한다는 약속을 어기는 것이다.

```typescript
function mustReturnNumber(d: Date): number {
  //...
}
const birthDate: any = '1992-01-03';
mustReturnNumber(birthDate); // <-- 타입 체커에 걸리지는 않지만 의도하지 않은 동작이 나올수도
```

이렇듯 코드는 또 다시 산으로 가게 되고...

<br>

> any 타입에는 언어 서비스가 적용되지 않는다

즉, any를 남발하다보면 타입스크립트가 '그래 니 맘대로 해라' 하고서 자동완성 기능 등 인텔리센스를 제공하지 않는다 !

<br>

이 밖에도 any 타입을 객체에 사용하면 타입 설계를 감추게 되므로, 이후 작업자가 코드를 이해하는데 상당한 시간을 쏟게 될 것이다. 또, 타입 체커를 의도적으로 패스하게 되므로 당장은 편하겠지만 그만큼 코드 신뢰도가 떨어지게 된다.

무조건적으로 나쁘다는것 보다 써야될 상황 또, 그렇지 않은 상황을 적절히 판단할 수 있는 눈을 가지라는 댄 형님의 조언 👊

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
