---
layout: post
title: Effective Typescript - week 4 (item 28-29)
category: Typescript
permalink: /typescript/:year/:month/:day/:title/
tags: [Typescript]
comments: true
---

---

## Effective Typescript - week 4 (item 28-29)

---

해당 포스트는 [이펙티브 타입스크립트]\(댄 밴더캄 지음, 장원호 옮김, 인사이트, 2021) 책을 읽으며 정리한 내용입니다.

### Item28. 유효한 상태만 표현하는 타입을 지향하기

```typescript
interface State {
  pageText: string;
  isLoadingL boolean;
  error?: string;
}

function renderPage(state: State) {
  if (state.error) {
    return 'error...';
  } else if (state.isLoading) {
    return 'Loading...';
  }
  return state.pageText;
}
```

위와 같은 코드가 있을때, 분기 조건이 명확하지 않아서 버그를 유발할 수 있다.

isLoading이 true면서, error 값이 설정되는 **무효한 상태**를 허용하기 때문이다.

```typescript
interface RequestPending {
  state: 'pending';
}
interface RequestError {
  state: 'error';
  error: string;
}
interface RequestSuccess {
  state: 'success';
  pageText: string;
}
type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
  currentPage: string;
  requests: { [page: string]: RequestState };
}

function renderPage(state: State) {
  const { currentPage } = state;
  const requestState = state.requests[currentPage];
  switch (requestState.state) {
    case 'pending':
      return 'Loading...';
    case 'error':
      return 'error...';
    case 'success':
      return requestState.pageText;
  }
}
```

위처럼 무효한 상태를 허용하지 않도록 각각의 상태를 명시적으로 모델링하는 <u>태그된 유니온</u>을 사용할 수 있다.

<br>

### Item29. 사용할 때는 너그럽게, 생성할 때는 엄격하게

함수의 매개변수는 타입의 범위가 넓어도 되지만, 결과를 반환할 때는 타입의 범위가 구체적이어야 한다.

선택적 속성과 유니온 타입은 반환 타입보다는 매개변수의 타입으로 더 많이 사용된다.

매개변수와 반환 타입의 재사용을 위해 기본 형태(*반환 타입*)와 느슨한 형태(*매개변수 타입*)을 도입하는 것이 좋다.

<br>

---

## 참고 자료

---

이펙티브 타입스크립트 - 댄 밴더캄 지음
