---
layout: post
title: Effective Typescript - week 4 (item 33-35)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 4 (item 33-35)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item33. string 타입보다 더 구체적인 타입 사용하기

string 타입은 범위가 매우 넓기 때문에 남발하는것은 좋지 않다.

```typescript
interface Album {
  artist: string;
  title: string;
  releaseDate: string; // YYYY-MM-DD
  recordingType: string; // live | studio
}
```

위와 같이 다 string으로 타입을 잡으면 releaseDate에 잘못된 날짜형식이나 recordingType에 엉뚱한 값이 들어올 수 있다.

또, function의 매개변수에서 매개변수1, 2 모두 string일 경우 2, 1과 같이 반대로 인자가 들어와도 타입체크를 통과하게 된다.

```typescript
/** 타입이 잘 작성되었는가? */
type RecordingType = 'studio' | 'live';

interface Album {
  recordingType: RecordingType;
}

function getAlbum(recordingType: RecordingType) {}
```

위와 같이 타입을 따로 표기하면 재사용도 되고 타입을 사용한 곳에서 마우스를 올려놓으면 주석에 써놓은 설명을 볼 수 있다.

![스크린샷](/assets/post/typescript/2022-05-09-01.png)

<br>

또, 객체의 속성 이름을 매개변수로 받을 때에는 `keyof T`를 사용하는 것이 좋다.

<br>

### Item34. 부정확한 타입보다는 미완성 타입을 사용하기

일반적으로 any의 사용을 줄이고 명시적으로 타입을 달아주는 것이 좋지만, 타입의 모델링이 까다로운 경우는 괜히 부정확한 타입으로 인해 생산성이 떨어지는 경우가 생길 수 있다.

오류메시지가 난해해서 읽기 어려워진다거나, 자동완성을 방해할 수도 있다.

<br>

### Item35. 데이터가 아닌, API와 명세를 보고 타입 만들기

API 명세가 있다면 명세로부터 타입을 생성하는 것이 좋다. GraphQL과 상성이 좋음.

특히, 특정 쿼리에 대해 타입스크립트의 타입을 생성할 수 있다는 점이 꿀~

쿼리와 스키마가 바뀌면 타입도 자동으로 바뀐다. 타입은 GraphQL 스키마로부터 생성되기 때문에 타입과 실제 값이 항상 일치한다.



<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
