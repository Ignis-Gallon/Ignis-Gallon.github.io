---
title: "추세 필터링과 변화점 탐지"
---

# 추세 필터링과 변화점 탐지

연구 인턴으로 있던 2024년 12월 ~ 2025년 3월에, 연구실의 선행 연구인 DTF-net을 이해하려고 공부한 배경 지식을 정리한 노트입니다. DTF-net은 시계열에서 추세를 뽑아내는 trend filtering 논문이면서, 추세가 급격히 변하는 지점을 찾는다는 점에서 change point detection과도 맞닿아 있습니다. 그래서 trend filtering이 무엇이고 시계열 분해와 어떻게 다른지, extreme value와 abrupt change는 어떻게 구분되는지, change point와 structural break 같은 비슷한 용어들이 각각 무엇을 가리키는지를 차례로 공부했습니다. 글은 노트를 쓴 순서대로 놓았고, 공부하면서 스스로 던진 질문과 찾은 답도 함께 남겼습니다.

- [1. 추세 필터링이란: 시계열 분해와의 차이](01-decomposition-vs-trend-filtering.md): trend filtering의 목적 함수와 두 항의 역할, 시계열 분해의 성분과 모델, 두 작업이 말하는 "추세"의 차이
- [2. Extreme value와 abrupt change](02-extreme-value-and-abrupt-change.md): 두 개념의 정의, 한 시계열과 다변량 시계열에서의 차이, abrupt change를 찾는 일이 더 어려운 이유
- [3. Change point detection 개요](03-change-point-detection-overview.md): change point의 종류, offline과 online, 방법의 계열과 한계, KS test, CPD가 trend filtering을 대신하지 못하는 이유
- [4. Structural break, regime change, change point, abrupt change](04-change-point-terms.md): 네 용어의 정의, Chow test와 CUSUM, sup-Wald, Bai-Perron 등 structural break 검정, trend filtering의 abrupt change와 CPD의 abrupt change의 차이

이 시리즈에서 정리한 내용으로 직접 한 실험은 [HP filter와 L1 trend filtering 비교 실험](../../research/sailab/04-hp-vs-l1-trend-filtering.md)에, 함께 읽은 논문은 [DTF-net](../../papers/2024-12-29-dtf-net.md), [Modeling Extreme Events in Time Series Prediction](../../papers/2025-02-04-modeling-extreme-events.md), [Bayesian Online Changepoint Detection](../../papers/2025-02-25-bocpd.md) 리뷰에 있습니다.
