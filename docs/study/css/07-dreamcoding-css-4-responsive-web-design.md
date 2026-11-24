---
title: "드림코딩 CSS 4: responsive web design"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [CSS, 드림코딩, 반응형 웹, media query]
---

# 드림코딩 CSS 4: responsive web design

작성일 2023-07-23
{ .post-date }

- Content is like water

이전에는 유동적인 Layout을 구현하기 위하여 float과 percentage value들을 이용했었다. 최근에는 Layout을 만들 때 Flex grid, Flex box, %, vw, vh를 많이 이용한다.

- 어디까지 Desktop이고, 어디까지 Tablet이고, 어디까지 Smartphone인가?

⇒ 이 부분을 고려해 보아야 한다. 이전에는 명확하게 구분이 되어있었지만, 요즘에는 이런 구분이 잘 안되어 있다.

굳이 나누자면, (무난한 크기)

- Mobile

320px~480px

- Tablet

768px~1024px

- Desktop

1024px~&gt;

---

이렇게 할 수 있는 것은 CSS의 media query를 이용하면 된다.

- media query

```css
@media screen and (min-width: 800px) {
	.container {
		width: 50%;
	}
}
```

- media query에 사용되는 mode는 screen, speech, print, all 등이 있다.
- 또한, and 뒤에 오는 문법은 (and), only, ‘,’(comma) 등이 올 수 있다.

⇒ MDN에 다양한 예제가 있음. (MDN의 Using Media Queries)

**대부분의 경우, min-width와 max-width로 반응형 웹을 충분히 만들 수 있음.**
