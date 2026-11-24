---
title: "추세 필터링이란: 시계열 분해와의 차이"
date: 2024-12-24
last_modified_at: 2025-02-03
category: study
tags: [시계열, 추세 필터링, 시계열 분해, HP filter]
---

# 추세 필터링이란: 시계열 분해와의 차이

인턴으로 있던 연구실의 선행 연구인 DTF-net([논문 리뷰](../../papers/2024-12-29-dtf-net.md))을 처음 읽을 때 trend filtering이라는 말부터 낯설었다. 시계열 분해(decomposition)에서 말하는 trend와 같은 것인지, 다르다면 무엇이 다른지가 분명하지 않았다. 이 글은 trend filtering의 기본 형태를 정리하고, 시계열 분해와 비교해 둘이 말하는 "추세"가 어떻게 다른지를 정리한 노트다.

## Trend filtering의 기본 형태

시계열 $(y_t)_{t=1}^{T}$가 있다고 하자. Trend filtering의 목표는 이 데이터에서 매끄러운 추세 성분 $(x_t)_{t=1}^{T}$를 노이즈와 분리하는 것이다. 구하려는 $x = (x_1, x_2, \dots, x_T)$는 두 조건을 만족해야 한다.

1. $x$는 원본 $y = (y_1, y_2, \dots, y_T)$에 합리적으로 가까워야 한다.
2. 고주파 노이즈가 아니라 시간에 따른 추세를 담도록 매끄러워야 한다.

가장 고전적인 방법은 원본과의 제곱 오차를 줄이면서 $x$의 매끄러움에 penalty를 주는 것이다.

$$
\min_{x} \; \underbrace{\sum_{t=1}^{T}(y_t - x_t)^2}_{\text{data fidelity}} \; + \; \lambda\underbrace{\sum_{t=1}^{T-2}(x_{t+2} - 2x_{t+1} + x_t)^2}_{\text{smoothness term}}
$$

- **data fidelity 항**은 추세 $x$가 원본 $y$에 가까워지게 한다.
- **smoothness 항**은 $x$가 매끄럽도록 만드는 항이다. 이 값이 작을수록 $x$가 매끄럽다. 괄호 안은 이차 차분으로, 추세가 급격히 변하는 정도(곡률)를 나타낸다.
- $\lambda$는 원본에 맞추는 정도와 매끄러움 사이의 균형을 정하는 hyperparameter다.

이 문제는 이차식을 최소화하는 문제(quadratic optimization)다. 볼록(convex)하고 해가 존재하므로 역행렬 계산, 행렬 분해, 반복법 등으로 최적의 추세 $x$를 구할 수 있다. 위 식이 Hodrick-Prescott(HP) filter다.

L1 trend filtering은 smoothness 항에 제곱 대신 L1 norm을 쓴다. 추세의 변화를 억제해 매끄럽게 만들면서도 급격한 변화는 허용하기 때문에, 추세가 급하게 꺾이는 부분을 더 잘 잡아낸다. 두 방법의 차이는 [HP filter와 L1 trend filtering 비교 실험](../../research/sailab/04-hp-vs-l1-trend-filtering.md)에서 직접 돌려 보며 확인했다.

## 시계열 분해

시계열 분해는 시계열을 특징적인 성분으로 나누는 작업이다. 보통 한 시계열이 다음 세 성분으로 이루어진다고 가정한다.

- **Trend**: 장기적인 변동의 방향
- **Seasonal**: 계절적 요인에 의한 반복 패턴
- **Residual**: 무작위로 발생하는 노이즈, 설명되지 않는 변동

성분을 합치는 방식에 따라 두 가지 모델이 있다.

| 모델 | 식 |
|---|---|
| 가산 모델 (additive) | $Y_t = T_t + S_t + R_t$ |
| 곱셈 모델 (multiplicative) | $Y_t = T_t \times S_t \times R_t$ |

대표적인 방법으로는 이동 평균을 쓰는 고전적 분해, STL(Seasonal-Trend decomposition using Loess), 그리고 X-12-ARIMA나 X-13ARIMA-SEATS 같은 통계적 분해가 있다.

## Trend filtering

Trend filtering은 노이즈가 섞인 시계열에서 그 안에 내재된 매끄러운 추세를 추출하는 작업이다. 최적화를 통해 원본에 대한 충실도(fidelity)와 매끄러움의 제약(smoothness constraint) 사이에서 균형을 맞춘 추세를 찾는다.

대표적인 방법은 다음과 같다.

- Moving average
- Hodrick-Prescott(HP) filter
- Kalman filtering
- Total variation filtering(TVF)

이 밖에 LOESS, LASSO 계열의 trend filtering처럼 penalty를 준 회귀(penalized regression)로도 추세를 얻을 수 있다. 어느 방법이든 결과로 나오는 시계열은 원본에 가깝고, 원하지 않는 단기 변동만 제거된 형태다.

## 둘은 무엇이 다른가

Trend filtering은 분해의 하위 작업으로 포함될 수 있다. 그러나 분해는 추세를 걸러내는 것에서 그치지 않고 seasonal과 residual까지 분리한다.

이 차이를 정리하다가 생긴 질문이 있다.

> HP filter나 DTF-net으로 추세를 얻었다면, 그것을 원본에서 빼면 seasonal 성분을 얻을 수 있는가?

그렇지 않다고 결론 내렸다. Trend filtering은 변동을 smoothing하도록 설계된 것이지, seasonality를 반영하도록 설계된 것이 아니다. 주기성이 강한 데이터에서는 trend filtering이 오히려 seasonality를 망가뜨릴 수도 있다.

두 작업이 말하는 "추세"의 뜻도 다르다.

| | Trend filtering | Decomposition |
|---|---|---|
| 목표 | 단기 변동(고주파 노이즈)을 제거하고 장기적인 패턴을 남긴다 | 시계열을 trend, seasonal, residual로 나눈다 |
| 추세를 얻는 방식 | 원본을 smoothing해 얻는다 | seasonal과 residual의 존재를 함께 고려해 최적화한 성분으로 얻는다 |
| 가정 | 원본에 내재된 추세가 있고, 노이즈를 걷어내면 그것이 드러난다 | 시계열이 세 성분의 합 또는 곱으로 이루어져 있다 |
| seasonal, residual | 고려하지 않는다 | 처음부터 모델에 포함한다 |

정리하면 trend filtering은 말 그대로 노이즈를 제거해 추세만 남기는 것이고, 분해는 seasonality와 residual을 고려한 상태에서 추세를 얻는다. 그래서 분해에서 얻은 trend 성분은 항상 원본을 smoothing한 결과와 같지는 않으며, 두 작업이 말하는 trend의 뜻이 서로 같지 않을 수 있다.

### 이 차이가 만드는 결과

- 데이터에 강한 seasonality가 있으면 trend filtering은 정확한 추세를 추출하지 못할 수 있다. 계절적 변동이 추세에 그대로 남을 수 있기 때문이다.
- DTF-net 논문은 L1 trend filtering이 정상(stationary) 데이터에서는 더 강한 성능을 보인다고 말한다. 추세를 조각별 직선(piecewise linear)으로 표현한다는 가정이, 노이즈가 적고 추세가 실제로 매끄러운 데이터와 잘 맞기 때문이라고 이해했다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. seasonality가 문제라면 먼저 차분(differencing)이나 계절 조정(seasonal adjustment)으로 seasonality를 제거한 뒤 trend filtering을 적용하는 것이 바람직하다고 한다. 구체적으로 어떤 절차를 말하는가?**

세 가지 방법이 있다. 주기를 $m$이라 하자(월별 자료의 연간 주기라면 $m = 12$).

- **계절 조정 뒤 filtering.** 계절 성분 $S_t$를 먼저 추정해 빼고, 남은 $y_t - S_t$에 trend filtering을 적용한다. $S_t$는 고전적 분해로 구할 수 있다. 길이 $m$의 이동 평균으로 대략의 추세를 구해 원본에서 빼고, 남은 값을 같은 계절끼리(1월은 1월끼리) 평균한 뒤, 한 주기의 합이 0이 되도록 맞춘다. STL로 구해도 된다. 곱셈 모델이면 빼는 대신 나눈다.
- **계절 차분.** $z_t = y_t - y_{t-m}$을 만든다. 주기가 같은 값끼리 빼므로 매 주기 같은 모양으로 반복되는 계절 성분이 사라진다. 다만 추세도 함께 바뀐다. 기울기가 $\beta$인 직선 추세는 상수 $m\beta$가 되므로, 차분한 계열에서 얻는 것은 원래의 추세가 아니라 "1주기 동안의 변화량"의 추세다. 원래 단위의 추세가 필요하면 계절 조정 쪽이 더 직접적이다.
- **한 번에 풀기.** $\ell_1$ trend filtering 논문(Kim et al., 2009)은 계절 성분을 최적화 문제 안에 넣는 확장을 제시한다. 추세 $x$와 주기 성분 $s$를 함께 변수로 두고, $s$가 주기 $p$로 반복되며 한 주기의 합이 0이라는 제약을 건다.

$$
\min_{x,\, s} \; \frac{1}{2}\lVert y - x - s \rVert_2^2 + \lambda \lVert Dx \rVert_1
\quad \text{subject to} \quad s_{t+p} = s_t, \;\; \sum_{k=1}^{p} s_k = 0
$$

합이 0이라는 제약이 없으면 상수를 추세와 계절 성분 어느 쪽에 넣어도 되어 분해가 하나로 정해지지 않는다. 어느 방법이든 핵심은 같다. smoothness penalty는 주기적인 오르내림과 노이즈를 구분하지 못하므로, 주기적인 부분을 따로 설명해 준 다음에 나머지에서 추세를 찾는다.

**Q. Trend filtering이 찾는 "추세가 변하는 지점"은 change point와 같은 것인가?**

찾는 지점이 겹칠 수는 있지만 같은 개념은 아니다. Trend filtering, 특히 $\ell_1$ trend filtering이 찾는 것은 추세의 기울기가 꺾이는 지점(kink)이고, 관측값의 모양에서 직접 정해진다. Change point detection이 찾는 것은 데이터를 만든 분포의 parameter(평균, 분산 등)가 바뀌는 지점이다. 기울기가 꺾이는 지점은 "조각별 직선인 평균 함수의 기울기"가 바뀌는 change point로 볼 수 있어 그 경우에는 둘이 일치한다. 반면 평균은 그대로이고 분산만 바뀌는 change point는 추세에 꺾임을 만들지 않으므로 trend filtering에서는 드러나지 않는다. 결과물도 다르다. 한쪽은 원본에 가까운 추세를, 다른 쪽은 변화 지점의 위치나 확률을 내놓는다. 자세한 비교는 [4편](04-change-point-terms.md)에 적었다.

## 참고 자료

- "Towards Dynamic Trend Filtering through Trend Point Detection with Reinforcement Learning", IJCAI 2024. <https://arxiv.org/abs/2406.03665>
- S.-J. Kim, K. Koh, S. Boyd, D. Gorinevsky, "$\ell_1$ Trend Filtering", SIAM Review, 51(2), 2009. <https://doi.org/10.1137/070690274>
- R. J. Hodrick, E. C. Prescott, "Postwar U.S. Business Cycles: An Empirical Investigation", Journal of Money, Credit and Banking, 29(1), 1997.
