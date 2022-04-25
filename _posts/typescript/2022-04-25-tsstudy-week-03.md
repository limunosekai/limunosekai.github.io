---
layout: post
title: Effective Typescript - week 3 (item 19-21)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 3 (item 19-21)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item19. 추론 가능한 타입을 사용해 장황한 코드 방지하기

타입 구문을 일일이 적는것은 때로는 비생산적이라고 느낄때가 있다.

마침 이번 장에서 그에 대한 내용을 설명하고 있다.

> 타입스크립트가 추론 가능하다면 타입 구문을 작성하지 않는것이 좋다

```typescript
interface Product {
  id: string;
  price: number;
}

function log(product: Product) {
  const id: string = product.id;
  const price: number = product.price;
}
```

여기서 id가 number로 바뀌었다고 가정했을 때, log 함수 내의 id 지역변수도 타입을 일일이 수정해주어야한다.

```typescript
function log(product: Product) {
  const { id, price } = product;
}
```

id는 string, price는 number로 추론이 가능하기 때문에 굳이 적어주지 않아도 된다. 매개변수에 이미 타입을 명시했기 때문에 이 정도로도 충분하다.

<br>

> 함수의 시그니처에는 타입 구문이 있지만, 함수 내 지역변수에는 타입 구문이 없는것이 이상적이다

매개변수에 기본값이 주어지는 경우엔 타입이 추론이 되기 때문에 타입 구문을 적지 않을 때도 있다.

또, 타입 정보가 있는 라이브러리의 콜백 함수에서 매개변수 타입은 자동으로 추론된다.

```typescript
// express
app.get('/api', (req: express.Request, res: express.Response) => {
  // X 잘못된 사례
});

app.get('/api', (req, res) => {
  // O 자동 추론이 되므로 타입 구문 필요 없다
});
```

<br>

> 객체 리터럴, 함수 반환 시에는 타입 명시를 고려해볼 필요가 있다

객체 선언 시에 타입을 명시해서 잉여속성 체크의 이점을 누릴 수 있다. 

또, 타입 구문이 없으면 객체를 사용한 곳에서 오류가 발생하지만,

타입이 있으면 객체를 선언한 부분에서 오류가 발생해서 디버깅에 용이하다.

```typescript
interface Product {
  id: string,
  title: string,
}

const a = {
  id: 123,
  title: 'abc',
};

function log(product: Product) {
  const { id, title } = product;
}

log(a); // Error: 'number' 형식은 'string' 형식에 할당할 수 없습니다.
```

<br>

함수 반환 시에도 타입을 명시하여 오류를 방지할 수 있다. 더 직관적으로 타입 확인이 가능하다.

```typescript
interface Vector2D {
  x: number;
  y: number;
}

// function add(a: Vector2D, b: Vector2D): Vector2D
function add(a: Vector2D, b: Vector2D): Vector2D {
  return {
    x: a.x + b.x,
    y: a.y + b.y,
  };
}

// function add(a: Vector2D, b: Vector2D): { x: number; y: number; }
function add(a: Vector2D, b: Vector2D) {
  return {
    x: a.x + b.x,
    y: a.y + b.y,
  };
}
```

<br>

### Item20. 다른 타입에는 다른 변수 사용하기

일반적으로 자바스크립트 let 키워드의 변수는 다른 타입의 값도 담아서 재사용이 가능하지만,

타입스크립트에서는 오류가 발생한다.

```typescript
let a = '123';
a = 123; // Error: 'number' 형식은 'string' 형식에 할당할 수 없습니다.
```

이를 방지하기 위해 유니온 타입을 사용할 수 있다.

```typescript
let a: string|number = '123';
a = 123;
```

하지만, a가 복잡한 타입이라면 이를 일일이 확인하는 것이 더 비효율적일 수 있다.

차라리 타입이 다르다면 변수를 분리하는 것이 좋다. 또, let 대신 const의 사용으로 타입 체커가 타입을 추론하기에 더 좋다.

<br>

### Item21. 타입 넓히기

let, var 등 변경 가능한 변수로 선언하고 타입을 명시하지 않으면, 그 변수의 타입이 리터럴 값이 속한 기본 타입으로 넒혀진다.

이를 타입 넒히기(type widening)라 한다. 타입을 명시하여 넒히기를 방지할 수 있다.

```typescript
let x = 'x';
function (axis: 'x'|'y') {
  return [axis];
}
add(x); // Error: 'string' 형식의 인수는 '"x" | "y"' 형식의 매개 변수에 할당될 수 없습니다.
```

런타임에서는 문제가 없지만, x의 타입이 string으로 넓혀져서 타입스크립트에서는 오류가 발생한다.

const로 정의하여 리터럴 값을 타입으로 정하던지, 타입을 명시하여 해결할 수 있다.

하지만, const도 모호한 순간이 있다.

```typescript
const x = {
  v: 1,
};

x.v = 3;
x.v = '3'; // Error: 'string' 형식은 'number' 형식에 할당할 수 없습니다.
x.a = '123'; // Error: { v: number; }' 형식에 'a' 속성이 없습니다.
```

x의 타입은 `{ v: number }`로 추론되었다. 따라서, v에 string 형식을 할당할 수도, 다른 속성을 추가할 수도 없다.

<br>

* **as const** (const assertion)

as const로 타입을 좁히고 넓히기 동작을 막을 수 있다.

```typescript
// const x2: { v: 1; }
const x2 = {
  v: 1 as const,
};

// const x3: { readonly v: 1; }
const x3 = {
  v: 1,
} as const;
```



<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
