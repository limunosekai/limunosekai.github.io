---
layout: post
title: [Next.js] dynamic import
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## [Next.js] dynamic import

---

#### 1. dynamic import

Next.js는 ES2020의 **dynamic import** 문법을 지원한다.

`next/dynamic`은 Next.js 3.0부터 지원하고 있다.

이를 통해 모듈을 런타임시 동적으로 로딩하는 것이 가능하다.

Code Splitting을 통한 초기 로딩속도 향상을 기대할 수 있다.

사용자 언어 등 런타임시에만 알 수 있는 정보에 기반하는 모듈에 적용이 가능하다.

Next.js에서 제공하는 dynamic은 promise를 resolve하는 과정 없이 변수에 할당이 가능하다.

<br>

#### 2. 사용법

```react
import dynamic from 'next/dynamic';

const Home = dynamic(() => import('./pages/HomePage'));

function App() {
    return (
    	<>
          <Home />
        </>
    )
}
```

React.lazy와 마찬가지로 최상위 레벨에서 써야하며, React component 내부에서 사용할 수 없다.

- 옵션을 통한 no SSR도 구현이 가능하다.

`dynamic(() => import(path), { ssr: false });`

굳이 서버사이드로 가져가지 않아도 될 무거운 컴포넌트에 적용이 가능하다.

- React.lazy의 Suspense의 fallback처럼 로딩시의 대체 컴포넌트를 보여주는 것도 가능하다.

`dynamic(() => import(path), { loading: () => (<div>loading...</div>)});`

하지만 대체 컴포넌트와 본래 컴포넌트가 너무 다를 경우, 화면이 깜빡거릴 수 있으니 주의해야한다.

<br>

---

## 참고 자료

---

[dynamic import로 줄여본 next.js 앱의 최초 로딩 시간](https://blog.rhostem.com/posts/2020-05-10-nextjs-and-dynamic-import)

[Next.js 공식문서 - Dynamic Import](https://nextjs.org/docs/advanced-features/dynamic-import)

[Code splitting with dynamic imports in Next.js](https://web.dev/code-splitting-with-dynamic-imports-in-nextjs/)
