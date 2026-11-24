---
title: "확률 과정과 Random Walk"
date: 2025-04-04
last_modified_at: 2025-04-04
category: study
tags: [시계열, 확률 과정, Random Walk, White Noise]
---

# 확률 과정과 Random Walk

작성일 2025-04-04
{ .post-date }

통계적 시계열 분석은 "관측한 시계열 뒤에는 그것을 만들어 낸 확률적 구조가 있다"는 가정에서 출발한다. 이 글에서는 그 가정을 표현하는 개념인 확률 과정(stochastic process)을 정리하고, 가장 단순한 확률 과정의 예인 random walk를 살펴본다. 이 시리즈의 나머지 글에서 쓰는 용어(실현, white noise, AR(1))를 여기서 먼저 정해 둔다.

## 확률 과정

확률 과정은 한마디로 "한 줄로 늘어선 확률 변수들(random variables in a row)"이다. 시간이 흐르면서 진행되는 확률 실험이라고 볼 수도 있다. 기호로는 다음과 같이 쓴다.

$$
\{X_t : \Omega \to \mathbb{R}\}_{t \in T}
$$

같은 확률 공간 위에 정의된 확률 변수들을 시간 순서로 모아 놓은 것이다. 기호 하나하나의 뜻은 다음과 같다.

| 기호 | 의미 |
|---|---|
| $T$ | 시간 지표 집합. 자연수면 이산 시간, 실수면 연속 시간이다 |
| $X_t$ | 시점 $t$에 정의된 확률 변수 |
| $\Omega$ | 표본 공간. 전체 시간 구간에 걸쳐 일어날 수 있는 모든 시나리오의 집합 |
| $\omega \in \Omega$ | 그중 실제로 일어난 하나의 결과 |
| $X_t(\omega)$ | 결과가 $\omega$일 때 시점 $t$에서 관측되는 값 |
| $\{X_t(\omega)\}_{t \in T}$ | 하나의 $\omega$에 대해 전 시간에 걸쳐 만들어진 값들. 실현(realization)이라고 부른다 |

$\omega$ 하나를 고른다는 것은 모든 시점의 값이 하나씩 확정된다는 뜻이다. 그렇게 확정된 값들을 시간 순서로 늘어놓은 것이 우리가 보는 시계열 한 개다. 즉 관측된 시계열은 확률 과정이 만들어 낼 수 있는 수많은 가능성 가운데 하나이고, 같은 확률 과정이라도 $\omega$가 달랐다면 다른 시계열이 나왔을 것이다.

### 정리하며 든 생각

"지금 관측된 시계열을 만들어 낸 어떤 확률 구조가 있다" 정도로 이해하고 나니 한 가지 의문이 생겼다. 시계열 예측의 목표가 시점 사이의 종속 관계를 파악하는 것이라면, autoregressive 모델은 확률 과정 자체를 찾아내려는 것과는 다른 일이 아닐까?

생각을 이어 가며 다음과 같이 정리했다.

- 과거 값과 현재 값의 관계를 모델로 세우는 것이 곧 확률 변수들 사이의 관계를 파악하는 일이므로, 근원이 되는 확률 과정이 무엇인지 알아내려는 노력으로 볼 수 있다.
- 실현은 하나뿐이지만 그 안에 관측값이 많다. 이 값들로 변수 사이의 관계를 추정하면, 결국 확률 과정이 값을 만들어 내는 구조를 모델링하는 셈이다.
- 다만 이것이 가능하려면 조건이 필요하다. 시계열이 비정상(non-stationary)이면 하나의 실현이 일관된 통계적 성질을 갖지 않으므로, 그 실현만으로 과정 전체를 일반화하기가 어렵거나 불가능할 수 있다.

마지막 항목이 [다음 글](02-stationarity-and-unit-root-test.md)에서 다루는 정상성이 필요한 이유다.

## White Noise

Random walk를 정의하려면 white noise가 먼저 필요하다. 시계열에서 white noise $\epsilon_t$는 보통 다음 세 가지를 만족하는 확률 변수의 열을 말한다.

- 평균이 일정하다. 보통 0으로 둔다.
- 분산이 일정하다($\sigma^2$).
- 서로 다른 시점의 값 사이에 자기상관이 없다.

자기상관이 없다는 것은 선형적인 상관이 없다는 뜻이다. 독립이라는 뜻은 아니다. 독립이라면 비선형적인 관계도 없어야 하므로, 독립은 무상관보다 강한 조건이다.

## Random Walk

### 기본형

일반적인 의미의 random walk는 정해진 규칙 없이 한 걸음씩 무작위로 나아가는 과정이다. 시계열에서는 다음 값이 현재 값에 white noise를 더해 만들어지는 확률 과정을 가리킨다.

$$
Y_t = Y_{t-1} + \epsilon_t
$$

$Y_{t-1}$이 현재 값, $Y_t$가 다음 값, $\epsilon_t$가 white noise다. 확률 과정이므로 같은 식에서도 $\omega$에 따라 매번 다른 경로가 나온다.

식을 처음 시점까지 거슬러 올라가며 풀면 $Y_t = Y_0 + \sum_{i=1}^{t} \epsilon_i$가 된다. 과거에 들어온 충격이 사라지지 않고 모두 쌓여 있는 꼴이다. 그래서 분산이 $t\sigma^2$로 시간에 따라 커지고, random walk는 비정상 시계열의 대표적인 예가 된다. 이 계산은 [8편](08-lag-operator-and-arma.md)에서 직접 해 본다.

### Drift가 있는 random walk

Random walk는 AR(1) 모델의 특수한 경우로 볼 수 있다. AR(1)은 현재 값을 바로 앞 시점의 값 하나로 설명하는 모델이다.

$$
Y_t = \beta_0 + \beta_1 Y_{t-1} + \epsilon_t
$$

여기서 $\beta_1 = 1$이면 random walk가 된다. $\beta_0$은 drift 항으로, 매 시점 같은 크기만큼 값을 밀어 주기 때문에 일정한 방향의 추세를 만든다.

### 계수가 1이 아닌 경우

현실의 시계열은 변화가 완전히 무작위인 경우도, 직전 값을 그대로 이어받는 경우도 드물다. 오늘 올랐으면 내일도 오를 가능성이 큰 지속성(persistence)을 보이는 시계열이 있고, 값이 너무 커지면 다음에는 줄어드는 쪽으로 움직이는 평균 회귀(mean-reverting) 성질을 보이는 시계열도 있다. 참고한 글에서는 이런 성질을 담기 위해 직전 값에 계수 $\rho$를 붙인 형태를 correlated random walk라고 소개한다.

$$
Y_t = \rho Y_{t-1} + \epsilon_t
$$

$\rho$는 스칼라 하나이고, 직전 값이 다음 값에 얼마나, 어느 방향으로 이어지는지를 정한다.

- $\rho$가 양수이면 직전 값과 같은 부호 쪽으로 이어지려는 지속성이 생긴다.
- $\rho$가 음수이면 값의 부호가 번갈아 뒤집히는 움직임이 생긴다.
- $\lvert \rho \rvert < 1$이면 과거 충격의 영향이 $\rho, \rho^2, \rho^3, \dots$으로 줄어들어 값이 평균 주변으로 돌아온다. 이것이 정상 AR(1)이다.
- $\rho = 1$이면 위의 random walk로 돌아간다.

식의 모양은 AR(1)과 같다. 결국 random walk와 정상 AR(1)을 가르는 것은 계수의 크기이며, 이 경계가 [2편](02-stationarity-and-unit-root-test.md)의 단위근 검정과 [8편](08-lag-operator-and-arma.md)의 "근이 단위원 밖에 있어야 한다"는 조건으로 이어진다.

## 정리

- 확률 과정은 시간 순서로 늘어선 확률 변수들의 모임이고, 관측된 시계열은 그 과정의 실현 하나다.
- 시계열 모델링은 하나의 실현에 담긴 많은 관측값으로 과정의 구조를 추정하는 일이다. 이 추정이 의미가 있으려면 정상성이 필요하다.
- White noise는 평균과 분산이 일정하고 자기상관이 없는 열이다. 무상관은 독립보다 약한 조건이다.
- Random walk는 계수가 1인 AR(1)이다. 충격이 계속 쌓여 분산이 시간에 따라 커진다.

## 참고 자료

- "Probability Theory 23 | Stochastic Processes" (YouTube 강의 영상). <https://www.youtube.com/watch?v=N8OZSyCvaU4>
- 모두의연구소 블로그, "시계열 정상성 (Stationarity), 들어봤니? - 알아보자 시계열 분석 Episode 2". <https://modulabs.co.kr/blog/time-series-stationarity>
- Prasanna, "Random Walk: Unraveling Time Series with Autoregressive Models" (Medium). <https://prasanna29.medium.com/random-walk-unraveling-time-series-with-autoregressive-models-c6aa53046d58>
