---
title: "6.14 Detail Info"
date: 2023-03-03
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, API, FutureBuilder]
---

# 6.14 Detail Info

작성일 2023-03-03 · 최종 수정일 2023-03-04
{ .post-date }

- 코드가 제대로 동작하지 않았었다. API URL을 잘못 적어서, 이상한 API 요청이 들어와버렸다…

(”\$baseURL/\$id/episodes” 라고 적어야 되는데, “\$baseURL/\$id/episode”라고 적어서, \$id까지의 요청으로 API 요청을 보내버렸다. 그래서 이상한 값이 들어와서 Data class와 타입이 맞지 않아 오류가 발생했었다.)

- Column은 자식 요소들을 정렬시킬 수 있는 기능을 갖고 있으며, Column에서 가로축은 crossAxis이고, 세로축이 mainAxis이다. 이와 반대로, Row에서 가로축은 MainAxis일 것이며, 세로축이 CrossAxis일 것이다.
