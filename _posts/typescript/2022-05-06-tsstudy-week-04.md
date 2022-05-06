---
layout: post
title: Effective Typescript - week 4 (item 30-32)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 4 (item 30-32)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item30. 문서에 타입 정보를 쓰지 않기

주석과 변수명에 타입 정보를 적는 것을 피한다. 타입 선언 자체만으로 이해할 수 있기 때문.

```typescript
/** 매개변수 item을 변경하지 않습니다. */
function add(item: string) {} // X

// -----------------------------------
function add(item: readonly string) {} // O  
```

타입이 명확하지 않은 경우 변수명에 단위 정보를 기입하는 정도는 용인된다. (ex: timeMs, temperatureC ...)

<br>

### Item31. 타입 주변에 null값 배치하기

```typescript
function extent(nums: number[]) {
  let min, max;
  for (const num of nums) {
    if (!min) {
      min = num;
      max = num;
    } else {
      min = Math.min(min, num);
      max = Math.max(max, num); // Error: 'undefined' 형식은 'number' 형식에 할당할 수 없습니다.
    }
  }
  return [min, max];
}
```

위 분기문에 null / undefined 체크에 max는 포함되어있지 않아서 <u>strictNullChecks</u>에 의해 오류가 발생했다.

이 함수를 사용하려한다면 또 오류가 발생할 것이다.

min과 max를 한 객체에 넣어 null이거나 null이 아니거나로 나누어 해결할 수 있다.

```typescript
function extent(nums: number[]) {
  let result: [number, number] | null = null;
  for (const num of nums) {
    if (!result) {
      result = [num, num];
    } else {
      result = [Math.min(num, result[0]), Math.max(num, result[1])];
    }
  }
  return result;
}
```

이 함수는 다음과 같이 사용할 수 있다.

```typescript
const [min, max] = extent([0,1,2,3])!;
const span = max - min;

//----------------------------------------

const range = extent([0,1,2]);
if (range) {
  const [min, max] = range;
  const span = max - min;
}
```

한 값의 null 여부가 다른 값의 null 여부에 관련되도록 설계하면 디버깅이 힘들어진다.

반환 타입을 큰 객체로 만들고 반환 타입 전체가 null이거나 null이 아닌 경우로 나누면 명료한 코드가 된다.

<br>

### Item32. 유니온의 인터페이스보다 인터페이스의 유니온 사용하기

```typescript
interface Layer {
  type: 'fill' | 'line' | 'point';
  layout: FillLayout | LineLayout | PointLayout;
  paint: FillPaint | LinePaint | PointPaint;
}
```

위와 같이 인터페이스 안에 유니온을 쓸 경우, type fill과 LineLayout을 같이 사용하는 등 실수를 유발할 수 있고 타입 추론도 어려워진다.

```typescript
interface FillLayer {
  type: 'fill',
  layout: FillLayout;
  paint: FillPaint
}
interface LineLayer {
  type: 'Line',
  layout: LineLayout;
  paint: LinePaint
}
interface PointLayer {
  type: 'Point',
  layout: PointLayout;
  paint: PointPaint
}
type Layer = FillLayer | LineLayer | PointLayer;

function draw(layer: Layer) {
  if (layer.type === 'fill') {
    const { layout, paint } = layer;
  	//...
  }
}
```

타입스크립트가 제어의 흐름을 분석할 수 있고 코드의 정확성을 체크하는데 도움이 된다.

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
