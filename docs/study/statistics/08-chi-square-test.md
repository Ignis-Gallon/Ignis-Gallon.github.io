---
title: "카이제곱 검정"
date: 2024-08-06
last_modified_at: 2024-08-06
category: study
tags: [통계, 카이제곱 검정, 독립성]
---

# 카이제곱 검정

[통계데이터분석 - 독립성검정과 적합성검정 - 카이제곱검정 🔑 chi-square test | 카이스퀘어검정 | 카이제곱값 | 범주형 변수 | 교차표 | 기대빈도 | 관측빈도](https://www.youtube.com/watch?v=XIxcK0H7J4Y)

카이제곱 검정은 기본적으로 독립성이 만족되어야 한다. 독립성이란, 추출된 하나의 표본이 다른 표본과 관련이 있어서는 안된다는 것이다.  다음 예시를 생각해보자.

| status\\seatbelt | with seatbelt | without seatbelt |
|---|---|---|
| minor injury | 1443 | 1781 |
| major injury | 151 | 312 |
| dead | 47 | 135 |

이와 같이 안전벨트 착용 유/무가 사고시 부상에 미치는 영향을 분석하고자 할 때, 카이제곱 검정을 이용할 수 있다. 그런데 여기서 seatbelt를 착용하고 minor injury를 당한 사람은 1의 빈도 만큼 기여한다. 즉, seatbelt를 착용하고 minor injury를 당한 사람이 1443명이라는 것이다.

반면 seatbelt를 착용하고 minor injury를 당한 한 사람의 빈도가 몇 십, 몇 백 만큼 기여되는 상황은 독립성에 위배되므로 카이제곱 검정으로 분석할 수 없다.
