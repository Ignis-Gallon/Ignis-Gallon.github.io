---
title: "시계열 분해: Classical Decomposition과 STL"
date: 2025-04-06
last_modified_at: 2025-04-15
category: study
tags: [시계열, 시계열 분해, 추세, 계절성, STL]
---

# 시계열 분해: Classical Decomposition과 STL

작성일 2025-04-06 · 최종 수정일 2025-04-15
{ .post-date }

시계열 분해(time-series decomposition)는 시계열을 추세, 계절성, 나머지로 나누어 보는 방법이다. [정상성](02-stationarity-and-unit-root-test.md)을 확보하려고 추세와 계절성을 제거할 때도, 시계열이 어떤 성분으로 이루어져 있는지 이해하려 할 때도 쓴다. 이 글에서는 세 성분의 의미, classical seasonal decomposition의 계산 절차, STL이 그것과 어떻게 다른지를 정리한다. 2024년 12월에 처음 적어 둔 성분의 정의와 2025년 4월에 다시 정리한 노트를 합쳤다.

## 분해의 목적

Classical decomposition과 STL은 방법이 다르지만 목적은 같다.

1. 시계열 데이터를 더 쉽게 이해하고 해석한다.
2. 추세, 계절성, 나머지로 나누어 분석과 예측의 정확도를 높인다.
3. 이상치 탐지나 예측의 전처리에 활용한다.

## 세 가지 성분

시계열은 일반적으로 추세 성분, 계절 성분, 불규칙 성분으로 이루어져 있다고 보고, 관측값의 움직임을 이 세 성분으로 나누어 설명한다.

### 추세 (Trend)

관측값이 전반적으로 오르거나 내리는 경향이다. 데이터의 장기적인 움직임과 방향을 나타낸다.

- 장기적인 움직임이다.
- 매끄럽고 점진적으로 변한다.
- 단기적인 변동은 무시한다.

### 계절성 (Seasonality)

특정한 주기에 따라 일정하게 반복되는 패턴이다. 일, 주, 월, 연 단위의 주기로 나타나고, 연휴나 기상 조건 같은 요인의 영향을 받는다. 매출이 특정 일자에 튀어 오르거나 떨어지는 일이 공휴일의 영향으로 매년 반복된다면 그 변동은 계절 성분으로 설명할 수 있다.

- 주기가 규칙적이다.
- 서로 다른 시기에도 비슷한 모양이 반복되므로 예측하기 쉽다.

### 나머지 (Residual)

추세와 계절성을 빼고 남는 부분이다. 규칙으로 설명할 수 없는 무작위 변동이나 잡음이며, 불규칙 성분(irregular component)이라고도 부른다.

- 보통 무작위이고 서로 상관이 없으며 뚜렷한 패턴을 보이지 않는다.

나머지 성분을 살펴보면 분해나 모델이 데이터를 얼마나 잘 설명했는지 평가할 수 있다. 나머지에 여전히 어떤 패턴이나 구조가 보인다면 데이터에 들어 있는 패턴을 충분히 잡아내지 못한 것으로 볼 수 있다.

### Cycle과 계절성의 차이

반복되는 움직임이라고 해서 모두 계절성은 아니다.

| | 계절성 | Cycle |
|---|---|---|
| 주기 | 고정되어 있다. 1년 주기라면 매년 같은 시기에 같은 방향으로 움직인다 | 정확한 주기와 시작, 끝을 알기 어렵다 |
| 원인 | 일상적이고 계절적인 요인 | 경기 변동처럼 장기적이고 불규칙한 요인 |
| 예측 | 상대적으로 쉽다 | 어렵다. 불경기가 언제 시작해 얼마나 이어질지는 정확히 알 수 없다 |

Cycle은 계절성처럼 명확한 수학적 주기를 정하기 어렵다. 그래서 추세나 나머지 성분에 함께 포함시키거나, 장기적인 변화 양상으로 보아 비정상적인 요소로 다루기도 한다. 데이터가 많은 예측 모델에서는 과거를 충분히 길게 보게 해서 장기 패턴까지 스스로 학습하도록 한다.

## Classical Seasonal Decomposition

### 덧셈 모형과 곱셈 모형

세 성분을 합치는 방식은 두 가지다. $T_t$는 추세, $S_t$는 계절성, $R_t$는 나머지다.

$$
y_t = T_t + S_t + R_t \qquad \text{(additive)}
$$

$$
y_t = T_t \times S_t \times R_t \qquad \text{(multiplicative)}
$$

어느 쪽을 쓸지는 계절성의 성질을 보고 정한다.

- **Additive**: 계절 변동의 진폭이 시계열의 수준(level)과 무관하게 일정할 때 쓴다. 값이 커져도 계절성은 늘 같은 크기만큼 더해진다.
- **Multiplicative**: 계절 변동의 크기가 시계열의 수준에 비례할 때 쓴다. 값이 커질수록 계절 효과도 커진다.

### 계산 절차

1. 계절성의 성질에 따라 additive와 multiplicative 가운데 하나를 고른다.
2. 계절 주기 $m$에 맞춰 중심 이동 평균(centered moving average, CMA)을 적용해 추세를 구한다. 월별 데이터에서 연간 주기를 본다면 $m = 12$, 주별 데이터라면 $m = 52$다. $m$이 홀수이면 한 시점을 중심으로 좌우 같은 개수만큼 평균을 내고, 짝수이면 이동 평균을 두 번 적용해 중심을 맞춘다.
3. 원 시계열에서 추세를 제거한다. Additive이면 빼고, multiplicative이면 나눈다.
4. 추세를 제거한 시계열을 주기 길이대로 자르고, 주기 안에서 같은 위치에 있는 값들끼리 평균을 낸다. 이것이 계절 성분이다. 계절 성분은 한 주기에 걸쳐 상쇄되어야 하므로, additive 모형에서는 평균이 0이 되도록 전체를 옮겨 준다.
5. 추세를 제거한 시계열에서 계절 성분까지 제거하면 나머지가 남는다.

4번에서 알 수 있듯이 classical decomposition의 계절 성분은 모든 주기에서 같은 모양이다. 전체 기간의 평균으로 한 주기의 모양을 정하고 그것을 반복해 쓰기 때문이다.

Python의 statsmodels에서 `seasonal_decompose`가 이 방법이다. 이름만 보고 STL로 착각하기 쉬운데 둘은 다른 함수다.

### 질문: 추세는 선형이고 계절성은 비선형이라는 말은 무슨 뜻인가

분해를 설명하는 글에서 "추세는 선형적이고 계절성은 비선형적"이라는 표현을 자주 보았다. 한동안은 계절성을 선형적인 성분으로 잘못 기억하고 있어서, 왜 그렇게 구분하는지 따로 정리했다. 여기서 선형과 비선형은 시간 $t$에 대한 함수의 모양을 말한다.

추세는 시간에 따라 점진적이고 장기적으로 늘거나 줄어드는 움직임이다. 가장 기본적인 모델은 $t$의 1차식이다.

$$
T_t = \beta_0 + \beta_1 t
$$

고차 다항식이나 스플라인 같은 비선형 추세도 있지만, 기본적으로는 선형적인 경향을 먼저 가정한다.

계절성은 주기적으로 반복되는 패턴이다. 시간 $t$에 대해 사인파에 가까운 모양으로 나타나므로 $t$의 1차식으로는 표현할 수 없다.

주파수의 관점으로 보면 세 성분을 대략 다음과 같이 대응시킬 수 있다. 변화가 느리고 장기적으로 나타나는 저주파 성분이 추세, 그보다 빠르게 주기적으로 반복되는 성분이 계절성, 변화가 빠르고 잡음에 가까운 고주파 성분이 불규칙 성분이다. 주파수 성분을 어떻게 구하는지는 [9편](09-fourier-transform.md)에 정리했다.

## STL

STL(Seasonal-Trend decomposition using LOESS)은 classical decomposition보다 복잡한 패턴을 다룰 수 있는 분해 방법이다. 시계열을 추세, 계절성, 나머지로 나눈다는 점은 같지만, 이동 평균과 주기별 평균 대신 LOESS라는 국소 회귀 평활을 쓴다.

핵심 특성은 세 가지다.

- **Adaptive smoothing**: 평활의 정도를 데이터에 맞춰 조절한다.
- **변하는 계절성**: 불규칙하고 비선형적인 계절성을 잡아낼 수 있다. 계절 성분의 모양이 시간에 따라 달라지는 것을 허용한다.
- **이상치에 강건함**: 이상치에 덜 민감하다.

Classical decomposition과 비교하면 다음과 같다.

| | Classical decomposition | STL |
|---|---|---|
| 추세 추정 | 중심 이동 평균 | LOESS 평활 |
| 계절 성분 | 모든 주기에서 같은 모양 | 시간에 따라 변할 수 있다 |
| 이상치 | 상대적으로 민감하다 | 덜 민감하다 |

현실의 계절 패턴은 시간이 지나며 조금씩 달라진다고 보는 편이 더 자연스러우므로, STL의 가정이 현실에 더 가깝다. 다만 계절 성분이 고정되어 있지 않다는 점은 예측에서 부담이 된다. Classical decomposition에서는 정해진 한 주기의 모양을 미래에 그대로 붙이면 되지만, STL에서는 계절 성분이 앞으로 어떻게 변할지도 따로 예측해야 한다. 두 방법으로 같은 데이터를 분해해 AR 예측까지 비교한 결과는 [AirPassengers 정상화와 AR 차수 선택](../../research/sailab/06-ar-order-selection.md)에 있다.

## 더 읽을 것

- Q. Wen 외, "RobustSTL: A Robust Seasonal-Trend Decomposition Algorithm for Long Time Series" (2018). STL을 더 강건하게 만든 방법으로, 나중에 읽으려고 적어 둔 논문이다. <https://arxiv.org/abs/1812.01767>

## 참고 자료

- "Time Series Decomposition" (Medium, @roshmitadey). <https://medium.com/@roshmitadey/time-series-decomposition-62cbf31ab65e>
- I. Svetunkov, "Forecasting and Analytics with the Augmented Dynamic Adaptive Model (ADAM)", Classical Seasonal Decomposition 절. <https://openforecast.org/adam/ClassicalDecomposition.html>
- "다짜고짜 배워보는 시계열 분석 패턴 쪼개기: Seasonal Decomposition 1편" (Medium, @connect2yh). <https://medium.com/@connect2yh/%EB%8B%A4%EC%A7%9C%EA%B3%A0%EC%A7%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EB%8A%94-%EC%8B%9C%EA%B3%84%EC%97%B4-%EB%B6%84%EC%84%9D-%ED%8C%A8%ED%84%B4-%EC%AA%BC%EA%B0%9C%EA%B8%B0-seasonal-decomposition-1%ED%8E%B8-bd9483795960>
