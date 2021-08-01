---
layout: post
title: React에서 classnames 활용하기
category: React
permalink: /react/:year/:month/:day/:title/
tags: [React]
comments: true
---

---

## React에서 classnames 활용하기

---

classnames 패키지를 이용하여 className을 dynamic하게 관리할 수 있다.

key-value로 설정하여 value의 true/false 평가에 따른 조건부 className 적용이 가능하다.

```react
import classNames from 'classnames';

function Button (props) {
    const { isLoading } = props;
    return {
        <button className={classNames('btn', {'btn-loading': isLoading})}></button>
    }
}
```

이런 식으로 isLoading이 undefined가 아니고 true일 때, dynamic하게 클래스명을 조건부로 설정할 수 있다.

<br>

---

## 참고 자료

---

[classnames - npm](https://www.npmjs.com/package/classnames)
