---
title: "Flex 2"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [CSS, Flexbox, 정렬, flex-wrap]
---

# Flex 2

[https://www.youtube.com/watch?v=phWxA89Dy94](https://www.youtube.com/watch?v=phWxA89Dy94)

- display: flex;

⇒ container를 생성하고, 그 container 안에 main axis, cross axis를 생성함.

기본적으로 main axis는 horizontal (row)이다.

- 여기서 flex-direction을 세팅하면 쌓이는 방향이 달라진다.

⇒ flex-direction: row가 default이고, flex-direction:column 으로 하면, 아래로 쌓이게 된다.

즉, main axis가 vertical이 된다.

- justify-content : main-axis의 정렬
- align-items : cross-axis의 정렬
- 아이템이 한 줄에 너무 많게되면 뭉개지는 현상이 발생하는데, 아이템의 크기를 유지하면서 한 줄에 표시하지 않으려면 flex-wrap을 사용하면 된다.

⇒ flex-wrap: wrap; 이 옵션은 아이템의 크기에 맞춰 줄이 나눠진다.

만약 flex-wrap: wrap;으로 설정되었다면, align-content: ; 설정을 사용할 수 있게 된다.

- align-content는 오직 flex-wrap: wrap;인 상황에서만 사용 가능하며, 이 상태에서 cross-axis의 정렬을 담당한다.
- gap : 아이템 간에 간격을 추가한다.

---

- flex-grow: ;

⇒ 아이템을 main-axis 방향으로 공간이 남지 않을때까지 grow 시킨다. 설정되는 값은 비율이며, 만약 모든 아이템들의 flex-grow 값이 1이라면 균등하게 크기가 분배된다.

- flex: ;

⇒ flex는 flex-grow, flex-shrink, flex-basis가 혼합된 shortcut이다.

---

- flex를 사용하면, 부모 요소가 Container가 되고, 자식 요소가 Item이 되는 것이다. 그러므로, flex를 적용한 요소 간의 배열의 차이가 flex, inline-flex의 차이라고 볼 수 있겠다.
