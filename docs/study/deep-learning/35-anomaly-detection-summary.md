---
title: "이상 탐지(Anomaly Detection) 정리"
date: 2024-03-22
last_modified_at: 2024-03-22
category: study
tags: [이상 탐지, 시계열, STL, ARIMA]
---

# 이상 탐지(Anomaly Detection) 정리

작성일 2024-03-22
{ .post-date }

## &lt;통계적 기법&gt;

- 근본 원칙: 이상값은 가정된 확률분포에서 생성되지 않아 부분적으로, 또는 완전히 동떨어졌다고 여겨지는 관측값이다. 이상값은 확률분포에서 낮은 영역에서 나타난다고 가정하며, 검정통계량을 바탕으로 학습된 모형으로부터 생성되었을 확률이 낮은 관측값을 이상값으로 본다. 모수, 비모수적 기법을 모두 적용할 수 있다.

### 1. 모수적 기법

- 정규분포 기반
- 회귀모형 기반

### 2. 비모수적 기법

- 히스토그램 기반

    - 훈련 데이터로 히스토그램을 생성한 뒤, 테스트 관측값이 유의미한 구간에 포함되면 정상, 그렇지 않으면 이상으로 판단한다.
    - 관측값이 포함되는 구간의 빈도를 바탕으로 이상점수를 구하기도 한다.

---

## &lt;시계열 데이터의 이상 탐지&gt;

시계열 데이터에서 이상의 유형은 Contextual Anomalies가 자주 발견된다.

- **이상의 유형**

    - Point Anomalies: 개별적인 데이터 instance가 다른 데이터의 범주와 많이 벗어나는 것
    - Contextual Anomalies: instance 그 자체로는 다른 데이터의 값의 범주에서 벗어나지 않지만, 특정한 context에서 변칙적인 것으로 여겨질 때
    - Collective Anomalies: 데이터 집합이 전체 데이터 집합과 비교하여 다른 유형인 경우

### STL Decomposition

- STL: Loess(Local Regression)에 기반한 Seasonal-trend Decomposition
- 시계열 데이터를 Seasonal, Trend, Residual의 3개의 Part로 분해할 수 있다.
- Additive Outliers를 탐지하는 데 특히 좋고, 데이터의 특성이 급격히 변할 때 잘 작동하지 않는다.

### SH-ESD

- Seasonal Hybrid ESD(Extreme Studentized Deviate)
- STL을 사용해 Seasonality와 Trend의 영향을 제거한다.
- MAD, Grubb’s T-Test 등 robust한 metric을 사용한다.

### ARIMA(Auto-regressive Integrated Moving Average)

- AU + MA 모형

- 시계열 데이터의 정상성(Stationary)을 가정한다.

    - 정상성: 평균, 분산이 시간에 따라 일정한 성질이며, 시계열 데이터의 특성이 시간의 흐름에 따라 변화하지 않음을 의미한다.

### Exponential Smoothing

- ARIMA와 유사하게 Trend, Level, Seasonality를 활용하지만, 최근의 결과값에 더 가중치를 준다거나 줄일 수 있다.

---

## &lt;기타&gt;

- Shapelet
