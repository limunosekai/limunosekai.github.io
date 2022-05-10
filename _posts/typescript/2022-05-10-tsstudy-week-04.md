---
layout: post
title: Effective Typescript - week 4 (item 36-37)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 4 (item 36-37)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item36. 해당 분야의 용어로 타입 이름 짓기

타입, 변수의 이름을 잘 짓는 것은 추상화의 수준과 가독성을 높일 수 있다.

```typescript
interface Animal {
  name: string;
  endangered: boolean;
  habit: string;
}

const leopard: Animal = {
  name: 'Snow Leopard',
  endangered: false,
  habit: 'tundra',
};
```

* name은 매우 일반적인 용어이므로 의미가 불분명하다
* endangered는 이미 멸종된 동물일 경우 어떻게 표현할지 불분명하다
* habit이 단순 string이라 범위가 너무 넓다
* 객체의 명과 name이 겹쳐서 의도가 다른지 불분명하다

조금 더 명확하게 데이터를 표현하고자 한다면 다음과 같을것이다

```typescript
interface Animal {
  commonName: string;
  genus: string;
  species: string;
  status: ConservationStatus;
  climates: KoppenClimate[];
}
type ConservationStatus = "EX" | "EW" | "CR";
type KoppenClimate = "Af" | "Am" | "As" | "Aw";

const snowLeopard: Animal = {
  commonName: "Snow LeoPard",
  genus: "Panthera",
  species: "Uncia",
  status: "EW",
  climates: ["As", "Aw"],
};
```

* 동일한 의미를 나타낼 때에는 같은 용어를 사용해야한다
* data, item, object 등 모호하고 의미없는 이름은 지양한다
* 포함된 내용, 계산 방식 등이 아닌 데이터 자체가 무엇인지 고려해야한다

<br>

### Item37. 공식 명칭에는 상표를 붙이기

구조적 타이핑으로 인해 오히려 잘못된 결과가 나올 수 있다.

값을 구분하기 위해 공식 명칭이 필요하다면 상표를 붙이는 방법을 고려해볼 수 있다.

```typescript
type Meters = number & {_brand: 'meters'};
type Seconds = number & {_brand: 'seconds'};

const meters = (m: number) => m as Meters;
const seconds = (s: number) => s as Seconds;

const oneKm = meters(1000);
const oneMin = seconds(60);
```

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
