---
title: "드림코딩 CSS 1: selector"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [CSS, 드림코딩, selector, cascading]
---

# 드림코딩 CSS 1: selector

작성일 2023-07-23
{ .post-date }

- CSS(Cascading Style Sheet)

CSS는 cascading 방식을 사용한다. 브라우저를 구성하는 요소들에 Custom으로 정의된 Style이 있는지 먼저 체크하고, 있다면 그 Style을 적용한다. 만약 Style이 없다면 브라우저에서 기본으로 정의한 Style을 적용한다.

- Author Style : 우리가 만드는 css file
- User Style : Browser 상에서 사용자의 취향에 맞게 Browser에서 바꾸는 것
- Browser : Browser 상에서 기본적으로 지정된 Style

Cascading은 ↓ 방향으로 진행된다.

---

## Selector

- Universal

    - \*

- type Tag

    - div

- ID

    - \#id

- Class

    - .class

- State

    - :

- Attribute

    - []

---

```html
<!DOCTYPE html>

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <ol>
        <li id="special">First</li>
        <li>Second</li>
    </ol>

    <button>Button 1</button>
    <button>Button 2</button>

    <div class="red"></div>
    <div class="blue"></div>

    <a href="naver.com">Naver</a>
    <a href="google.com">Google</a>
    <a>Empty</a>
</body>

</html>
```

- ol은 container이므로, 기본적으로 사용자에게 보이지 않음.
