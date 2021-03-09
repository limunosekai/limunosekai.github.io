---
layout: post
title: React.js 프로젝트 깃허브 배포시 blank 현상
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## React.js 프로젝트 깃허브 배포시 blank 현상

---

리액트로 만든 프로젝트를 깃허브 페이지에 배포시 404 에러와는 다르게 빈 화면만 렌더링이 되는 경우가 있다.  

깃허브에 deploy 하기 위해서는 gh-pages 패키지를 설치하고 package.json에 hompage를 추가, scripts 부분에 predeploy와 deploy를 추가하고 npm run deploy 명령어를 실행한다.  (자세한 사용법에 대해서는 공식문서 및 여러 블로그 참고)

그런데 하얀 화면만 떠있고 \<div id="root"> 밑으로 아무런 컴포넌트도 렌더링이 되지 않는다.

<br>

<br>

우선 몇 가지 점검해볼 사항이 있다.

1. **open API를 사용하는 경우 end-point의 url이 http로 시작하는지**

   * 이 문제는 보안 이슈로 https을 사용하여 해결한다

2. **깃허브 저장소의 Settings의 GitHub Pages - Source 확인**

   ![페이지](/assets/post/react/2021-03-09-01.JPG)

   * Branch가 mater로 되어있다면 gh-pages로 설정한다

3. **Router를 사용하는 경우**

   * 로컬에서 돌릴때는 / (슬래시)만 그어도 home 이라는 것을 인식하지만 깃허브 페이지 URL의 basename은 https://githubname.github.io 이 된다
   * 따라서, /repository 가 우리가 생각하는 home이 되는 것이고 그 하위 페이지들은 /repository/ 뒤에다 작성을 해야한다
   * Route, Link 태그 등 다 확인 필요
   * BrowserRouter에 basename도 확인 필요

<br>

위 방법들로도 해결이 안된다면

![코드](/assets/post/react/2021-03-09-02.JPG)

BrowserRouter의 basename에 다음을 추가한다.

<br>

<br>

결국 마지막 방식으로 고치긴 했지만 거기까지 도달하는 과정이 엄청 길었다...

한국 사이트에선 찾기 힘들었지만 해외에선 이런 에러를 겪은 사람이 꽤 되는듯하다

다음엔 이런 상황을 마주하지 않도록 기록해둔다

---

## 참고 자료

---

[GitHub Pages](https://maximorlov.com/deploying-to-github-pages-dont-forget-to-fix-your-links/)

[Code is deployed, but page is blank](https://github.com/gitname/react-gh-pages/issues/3)