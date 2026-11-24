---
title: "베이즈 이론"
---

# 베이즈 이론

연구 인턴으로 있던 2025년 2 ~ 3월에 베이즈 방식의 변화점 탐지(Bayesian Online Change Point Detection)를 공부하면서, 그 바탕이 되는 베이즈 추론의 기초를 함께 정리한 노트입니다. 블로그 글과 YouTube 강의, PRML을 보고 이해한 내용을 세 편으로 나누었습니다. 수식은 유도 과정을 직접 따라가며 다시 적었고, 공부하면서 스스로 던진 질문과 그에 대해 찾은 답도 함께 남겼습니다.

- [1. 켤레 사전분포: 동전 던지기로 보는 MLE, MAP, conjugacy](01-conjugate-prior.md): 동전 던지기의 MLE와 과적합, Beta prior와 MAP, conjugate prior의 정의와 두 가지 이점(닫힌 형태의 계산, 순차 갱신)
- [2. 지수족: 여러 분포를 하나의 꼴로 쓰기](02-exponential-family.md): 지수족의 일반형과 구성 요소, Bernoulli·Poisson·Gaussian·다항분포를 지수족으로 쓰기, log-normalizer의 미분과 충분통계량의 평균·분산, MLE 조건, conjugate prior의 일반형
- [3. 최소제곱에서 베이즈 회귀까지](03-from-least-squares-to-bayesian-regression.md): 최소제곱과 정규화, 확률적 회귀에서 MLE와 MAP가 각각 무엇과 같아지는지, posterior와 순차 갱신, marginalization으로 얻는 예측 분포, hyper-prior와 Type-II maximum likelihood 근사

1편과 2편은 분포 하나의 모수를 추정하는 문제를, 3편은 같은 생각을 회귀 모델에 적용하는 과정을 다룹니다.
