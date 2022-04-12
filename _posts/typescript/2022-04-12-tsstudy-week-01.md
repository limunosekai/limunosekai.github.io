---
layout: post
title: Effective Typescript - week 1 (item 02-03)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 1 (item 02-03)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item02. 타입스크립트 설정 이해하기

타입스크립트 컴파일러 설정은 `tsconfig.json`에서 가능하다. 커맨드 라인보다 설정파일을 통하는 것이 좋다.

`tsc --init` 후 json 설정파일을 생성할 수 있다. 매우 디테일한 고급설정까지 가능하므로 설정을 이해하는 것은 중요하다.

* `noImplicitAny` : 변수들이 미리 정의된 타입을 가져야하는지 여부 

```typescript
function add(a, b) { // <-- Error: 'a' 매개 변수에는 암시적으로 'any' 형식이 포함됩니다
  return a + b;
}
```

기존 자바스크립트를 타입스크립트로 마이그레이션하는 상황이 아니라면 위 옵션 설정을 해주는 것이 좋다.

* `strictNullChecks` : null | undefined가 모든 타입에서 허용되는지 여부

```typescript
const x: number = null; // <-- Error: 'null' 형식은 'number' 형식에 할당할 수 없습니다.
const a: number | null = null; // <-- 명시적으로 null 타입을 선언해주면 OK
```

위 옵션을 설정하려면 먼저 noImplicitAny를 설정해야한다. 가급적 프로젝트 초반에 설정하는 것이 좋다.

모든 체크를 설정하려면 strict를 설정하면 된다.

<br>

_타입스크립트에서 변수 뒤에 **!** 를 붙여 Null이 아닌 어선셜 연산자 혹은 확정 할당 어선셜 용도로 사용할 수 있다_

> Null이 아닌 어선셜 연산자 (Non-null assertion operator)
>
> 해당 변수가 null이 아니라고 컴파일러에 전달하여 일시적으로 null 제약조건을 완화한다

```typescript
interface human {
  name: string | null;
}

let me: human = {
  name: null
};

console.log(me.name.toString()); // <-- Error: 개체가 'null'인 것 같습니다.
console.log(me.name!.toString()); // OK

```

> 확정 할당 어선셜 (Definite Assignment Assertions)
>
> 값이 무조건 할당되어 있다고 컴파일러에 전달하여 변수, 객체 등을 사용할 수 있게 된다

```typescript
let x: number;
console.log(x + x); // <-- Error: 'x' 변수가 할당되기 전에 사용되었습니다.
console.log(x! + x!); // OK

let a!: number;
console.log(a + a); // OK
```

<br>

모든 컴파일러 옵션은 [여기](https://typescript-kr.github.io/pages/compiler-options.html)에서 확인하도록 하자.

<br>

### Item03. 코드 생성과 타입이 관계없음을 이해하기

타입스크립트 컴파일러는 JS로 트랜스파일, 타입 오류 체크 두 가지 역할을 수행한다.

두 가지 작업은 독립적이라 서로 영향을 미치지 않는다. 즉, 다음과 같은 특징을 가진다.

#### 1. 타입 오류가 있는 코드도 컴파일이 가능하다

유효한 자바스크립트 코드라면 타입스크립트에서 타입 체크에 걸리더라도 컴파일된다.

이를 방지하고 싶다면 설정 파일에서 `noEmitOnError`를 설정한다.

#### 2. 런타임에는 타입 체크가 불가능하다

```typescript
interface Human {
  hp: number;
}
interface Wizard extends Human {
  mp: number;
}
type Point = Human | Wizard;

function buff(point: Point) {
  if (point instanceof Wizard) { // <-- Error: 'Wizard'은(는) 형식만 참조하지만, 여기서는 값으로 사용되고 있습니다
    return point.hp * point.mp; // <-- Error: 'Point' 형식에 'mp' 속성이 없습니다, 'Human' 형식에 'mp' 속성이 없습니다.
  } else {
    return point.hp * point.hp;
  }
}
```

instanceof 는 런타임에서 일어나는데 트랜스파일 후 타입이 제거되기 때문에 타입을 유지해야한다.

방법1: 해당 속성이 인자에 존재하는지 ?

```typescript
if ('mp' in point) {
  // ...
}
```

방법2: 런타임에 접근 가능한 타입 정보를 명시적으로 저장하는 <u>태그 기법</u> 사용

```typescript
interface Human {
  kind: 'human';
  hp: number;
}
interface Wizard {
  kind: 'wizard';
  hp: number;
  mp: number;
}
type Point = Human | Wizard;

function buff(point: Point) {
  if (point.kind === 'wizard') { 
    return point.hp * point.mp; 
  } else {
    return point.hp * point.hp;
  }
}
```

Human과 Wizard는 각각 유니크한 kind를 가지므로 Point는 교집합이 없다. Human이거나 Wizard이거나, 둘 다 일 수 없다

이를 <u>태그된 유니온(tagged union)</u> 혹은 <u>서로소 유니온</u>이라고 불린다.

방법3: 아예 Human, Wizard를 class로 만들면 타입과 값 두 개 다 사용이 가능해진다.

#### 3. 타입 연산은 런타임에 영향을 주지 않는다

```typescript
function asNum(val: number | string): number {
  return val as number; // <-- 에러는 없다
}

// 컴파일 후
function asNum(val) {
  return val; // <-- 무슨 기능인지 알 수 없다
}

// 값을 정제하려면 런타임의 타입을 체크하고 연산을 통해 변환한다
function asNum(val: number | string): number {
  return typeof(val) === 'string' ? Number(val) : val;
}
```

#### 4. 런타임 타입은 선언된 타입과 다를 수 있다

비동기 후 응답의 값이라던지 선언된 타입이 바뀔 수 있음을 명심해야한다. 여기에 타입스크립트가 자바스크립트로 트랜스파일 되면 타입이 사라지기에 의도하지 않은 동작이 나올 수 있다.

#### 5. 타입스크립트 타입으로는 함수를 오버로드 할 수 없다

함수의 타입을 여러 개 선언해도 구현체는 하나이기에 트렌스 파일 후 타입이 제거되면 구현체만 남게 된다.

#### 6. 타입스크립트 타입은 런타임 성능에 영향을 주지 않는다

타입과 타입 연산자는 트랜스파일 단계에서 제거되므로 런타임 성능에 영향을 주지 않는다.

<br>



<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
