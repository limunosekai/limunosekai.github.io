---
layout: post
title: Effective Typescript - week 2 (item 16-18)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 2 (item 16-18)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item16. number 인덱스 시그니처보다는 Array, 튜플, ArrayLike 사용

타입스크립트는 자바스크립트와 다르게 숫자로 된 키를 허용하고 문자열로 된 키와 다르게 인식된다.

자바스크립트로 트랜스파일되면 어차피 문자열로 바뀌게 되지만, 적어도 타입 체크 시점에서는 유용하다.

```typescript
const x = [1,2,3];
const x0 = x[0];
const x1 = x['1']; // Error: 인덱스 식이 'number' 형식이 아니므로 요소에 암시적으로 'any' 형식이 있습니다.
```

<br>

### Item17. 변경 관련된 오류 방지를 위해 readonly 사용하기

readonly 접근 제어자를 붙인 readonly number[] 는 타입으로 분류되어 일반 number[]와 구분되는 특징이 있다.

* 배열의 요소를 읽을 수 있지만, 쓸 수 없다
* length를 읽을 수는 있지만, 바꿀 수 없다
* 배열을 변경하는 Array Method를 호출할 수 없다

number[]는 readonly number[]의 서브타입이 된다. 따라서, 변경 가능한 배열을 readonly 배열에 할당할 수 있다.

하지만, 그 반대는 될 수 없다

```typescript
const a: number[] = [1,2,3];
const b: readonly number[] = a;
const c: number[] = b; // Error : 'readonly number[]' 형식은 'readonly'이며 변경 가능한 형식 'number[]'에 할당할 수 없습니다.
```

<br>

함수가 매개변수를 바꾸지 않는다면 readonly를 선언해야한다. 그런데 함수를 readonly로 만들면 그 함수를 호출하는 다른 함수도 readonly로 만들어야한다. 그래야 타입 안정성이 보장된다.

인덱스 시그니처에서도 readonly로 속성이 변경되는것을 방지할 수 있다.

```typescript
let obj: Readonly<{ [k: string]: number }> = {};
obj.a = 1; // Error : 'Readonly<{ [k: string]: number; }>' 형식의 인덱스 시그니처는 읽기만 허용됩니다.
obj = { ...obj, a: 1 }; // 정상
```

<br>

readonly는 shallow하게 동작하므로 deep하게 사용되길 원하면 ts-essentials의 DeepReadonly 제네릭을 사용하면 된다.

<br>

### Item18. 매핑된 타입을 사용하여 값을 동기화하기

특정 인터페이스의 속성들과 동기화된 새로운 값을 만들려면 이를 매핑시켜서 사용한다.

훗날 이 인터페이스에 새로운 속성이 추가될 때, 타입체크에 의해 오류를 방지할 수 있다.

다음은 렌더링 최적화를 위해 함수 타입은 shoudUpdate를 건너뛰기 위해 사용되는 코드이다.

```typescript
interface Product {
  id: string;
  price: number;
  title: string;
  label: string;
  onClick: () => void;
}

const PRODUCT_UPDATE: { [k in keyof Product]: boolean } = {
  id: true,
  price: true,
  title: true,
  label: true,
  onClick: false,
}

function shouldUpdate(oldProps: Product, newProps: Product) {
  let k: keyof Product;
  for (k in oldProps) {
    if (oldProps[k] !== newProps[k] && PRODUCT_UPDATE[k]) {
      return true;
    }
  }
  return false;
}
```

`[k in keyof Product]: boolean` 이렇게 매핑시켜놓으면 나중에 Product에 속성이 추가되었을때 PRODUCT_UPDATE도 추가하도록 강제할 수 있다.

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
