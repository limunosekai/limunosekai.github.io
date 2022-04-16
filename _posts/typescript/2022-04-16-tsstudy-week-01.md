---
layout: post
title: Effective Typescript - week 1 (item 06-08)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 1 (item 06-08)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item06. 편집기를 사용하여 타입 시스템 탐색하기

타입스크립트를 설치하면 컴파일러(tsc)와 타입스크립트 서버(tsserver)를 실행할 수 있다.

타입스크립트 서버도 '언어 서비스'를 제공하는데 여기에는 코드 자동완성, 스펙 검사, 검색, 리펙토링 등이 포함된다.

특히 타입이 동봉되지 않은 외부 라이브러리에 대해 @types 패키지를 설치하라는 인텔리센스를 지원받을 수 있다.

해당 기능을 끄고 싶으면 vs code setting에서 아래 항목을 true로 설정한다.

![인텔리센스](/assets/post/typescript/2022-04-16-01.png)

<br>

언어 서비스는 라이브러리의 타입을 쉽게 탐색할 수 있도록 도와준다. (F12: 정의)

![fetch](/assets/post/typescript/2022-04-16-02.png)

![fetch type](/assets/post/typescript/2022-04-16-03.png)

<br>

### Item07. 타입이 값들의 집합이라고 생각하기

> 타입은 값의 집합이다

타입의 세계에서 가장 작은 집합은 never 타입이다. never 타입은 공집합이므로 아무런 값도 할당받을 수 없다.

```typescript
const x: never = 12;
// 'number' 형식은 'never' 형식에 할당할 수 없습니다.
```

그 다음은 한 가지 값만 포함하는 unit 타입이라고도 불리는 literal 타입이다.

```typescript
type name = 'limu';
type B = 'B';
```

둘 이상으로 묶으려면 union 타입을 사용한다.

```typescript
type AKB48 = 'A' | 'K' | 'B' | 48;
```

<br>

> 타입은 엄격한 상속관계가 아닌 겹쳐지는 집합이다

```typescript
type AKB = 'A' | 'K' | 'B';

const akb: AKB = Math.random() < 0.5 ? 'A' : 'B';
// A와 B는 AKB 타입에 속하므로 정상
```

<br>

> 한 객체의 추가 속성이 타입 선언에 없더라도 그 타입에 속할 수 있다

```typescript
interface Person {
  name: string;
}

interface Life {
  birth: Date;
  death?: Date;
}

type PersonLife = Person & Life;

const human: PersonLife = {
  name: 'foo',
  birth: new Date('1992/01/03'),
}

type temp = keyof (Person | Life);

const human3: temp = {
  name: 'bar', // Error: 'string' 형식은 'never' 형식에 할당할 수 없습니다.
  birth: new Date('1992/01/03'), // Error: 'Date' 형식은 'never' 형식에 할당할 수 없습니다.
}

type temp2 = keyof (Person & Life);

const human4 = 'name' // 정상
```

Person과 Life의 교집합이 없으므로 never로 가는것을 생각하지만, 타입 연산자의 동작은 그렇지 않다.

& 인터섹션 타입의 값은 타입 내의 속성을 모두 포함하는 것이 일반적. 타입 정의를 하나로 합치는 동작을 한다.

반대로, 유니온 타입에 대한 keyof 는 never가 된다. keyof는 기본적으로 객체의 key값을 타입으로 사용 지정하게 한다.

`keyof (A&B) = (keyof A) | (keyof B)` : key 값들 ('name', 'birth', 'death')

`keyof (A|B) = (keyof A) & (keyof B)` : key 값들 중 겹치는 값 (never)

<br>

* 유니온 타입 작성 시 주의사항

```typescript
interface Person {
  name: string;
}

interface Developer {
  name: string;
  birth: Date;
  death?: Date;
}

type PType = Person | Developer;

function hello(someone: PType) {
  someone.name;
  someone.birh; // Error: 'PType' 형식에 'birth'속성이 없습니다.
}
```

함수 호출 시점에 두 타입 중 어느 것이 올 지 모르므로 오류가 나이 않는 선에서 타입추론을 하게 된다.

따라서, 두 인터페이스 모두 가지고 있는 name 이외에는 접근할 수 없다.

<br>

> 타입 연산은 집합의 범위에 적용된다

extends 키워드로 부분집합을 생각해볼 수 있다.

```typescript
interface Vector1D {
  x: number;
}

interface Vector2D extends Vector1D{
  y: number;
}
```

y이외에 반드시 x를 포함해야하기에,  Vector2D는 Vector1D의 부분집합이라 볼 수 있다.

<br>

#### Item08. 타입 공간과 값 공간의 심벌 구분하기

타입과 값의 혼동이 없도록 주의하자. 이름 지을 때 중복이 있다면 아무래도 혼란스러울듯?

class와 enum은 상황에 따라 타입, 값 두 가지에 쓰이는 예약어이다.

연산자 중에서는 typeof가 타입, 값에 쓰일 때 각각 다른 기능을 한다.

```typescript
interface Person {
  name: string;
}

const p: Person = {
  name: "foo",
};

type T1 = typeof p; // Person 타입

const b = typeof p; // 'object' 문자열
```

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
