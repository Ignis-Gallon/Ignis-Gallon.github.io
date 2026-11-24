---
title: "시계열 통계 기초"
---

# 시계열 통계 기초

KAIST SAILAB에서 연구 인턴으로 있던 2025년 2월부터 6월 사이에, 시계열 예측과 인과 분석 논문을 읽는 데 필요한 통계 기초를 공부하며 정리한 노트입니다. 블로그와 강의, 논문을 읽고 제 말로 다시 적었고, 읽다가 막힌 지점과 스스로 찾은 답을 함께 남겼습니다.

대부분의 노트를 4월 초 며칠 사이에 함께 썼기 때문에, 글의 순서는 작성일이 아니라 앞의 개념이 뒤의 글에 필요한 순서로 두었습니다.

- [1. 확률 과정과 Random Walk](01-stochastic-process-and-random-walk.md): 확률 과정과 실현의 의미, white noise, random walk와 AR(1)의 관계
- [2. 정상성과 단위근 검정](02-stationarity-and-unit-root-test.md): 강정상성과 약정상성, 약정상성 조건에 대한 세 가지 의문, ADF와 KPSS 검정, 로그 변환과 차분
- [3. 자기공분산, ACF, 교차상관, PACF](03-autocovariance-acf-pacf.md): 공분산에서 편자기상관까지의 정의와 계산, ACF와 PACF 그래프의 신뢰구간이 나오는 과정
- [4. AR과 VAR](04-ar-and-var.md): 두 모델의 식과 계수 추정, AR 모델링 절차, ARIMA와의 관계, 공부하며 남긴 질문
- [5. AIC와 BIC](05-aic-and-bic.md): KL divergence로 본 AIC, AR(p)의 조건부 정규 가능도, Laplace 근사로 본 BIC
- [6. 시계열 분해: Classical Decomposition과 STL](06-time-series-decomposition.md): 추세, 계절성, 나머지의 의미와 분해 절차, 두 방법의 차이
- [7. Granger 인과](07-granger-causality.md): 예측 오차 분산으로 내린 정의, 제한 모델과 비제한 모델의 F-검정, 예제와 한계
- [8. 시차 연산자와 AR, MA의 상호 변환](08-lag-operator-and-arma.md): MIT 14.384 1강. 시차 다항식의 역, 단위원 조건, AR(1)과 MA 과정의 공분산 구조
- [9. 푸리에 변환 메모](09-fourier-transform.md): 푸리에 급수와 변환의 차이, 실수 시계열의 FFT 결과를 읽는 방법

여기서 정리한 내용을 실제 데이터에 적용해 본 실험은 [AirPassengers 정상화와 AR 차수 선택](../../research/sailab/06-ar-order-selection.md)에 있습니다.
