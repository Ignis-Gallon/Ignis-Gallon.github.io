---
title: "시차 연산자와 AR, MA의 상호 변환"
date: 2025-06-10
last_modified_at: 2025-06-10
category: study
tags: [시계열, 시차 연산자, ARMA, 정상성, 자기공분산, 단위근]
---

# 시차 연산자와 AR, MA의 상호 변환

작성일 2025-06-10
{ .post-date }

MIT OpenCourseWare의 Time Series Analysis(14.384) 1강 "Stationarity, Lag Operator, ARMA, and Covariance Structure"의 강의 노트를 읽으며 정리한 글이다. 앞의 글들에서 결과만 받아들였던 것들, 즉 random walk가 왜 비정상인지, "특성 방정식의 근이 단위원 밖에 있어야 한다"는 조건이 어디에서 오는지, AR 모델과 MA 모델이 어떻게 서로 바뀌는지를 시차 연산자(lag operator)로 직접 계산해 본다. 유도는 손으로 풀어 본 것을 옮겼다.

## 정상성

어떤 모델로 시계열의 미래를 설명하려면 정상성이 필요하다. 한번 정한 모델은 바뀌지 않는다. 그 모델로 미래도 설명하려면 시계열의 통계적 특성이 시간에 따라 변해서는 안 된다.

강의 노트의 정의는 다음과 같다.

**정의 1 (white noise).** 다음을 만족하는 확률 변수의 열 $\{e_t\}$를 white noise라고 한다.

- $\mathbb{E}[e_t] = 0$
- $\mathbb{E}[e_t^2] = \sigma^2$
- $\mathbb{E}[e_t e_s] = 0 \quad (s \ne t)$

평균이 0이고, 분산이 $\sigma^2$로 일정하며, 서로 다른 시점 사이의 공분산이 0인 잡음이다.

**정의 2 (강정상성).** 과정 $\{y_t\}$가 모든 $k, t, n$에 대해 $(y_t, \dots, y_{t+k})$의 분포와 $(y_{t+n}, \dots, y_{t+k+n})$의 분포가 같으면 strictly stationary라고 한다. 시점을 얼마나 옮기든 분포가 같다는 뜻이다. 현실에서 이런 시계열은 관찰하기 어렵다.

**정의 3 (약정상성).** 과정 $\{y_t\}$가 다음을 만족하면 second-order stationary(weakly stationary, 또는 그냥 stationary)라고 한다.

- $\mathbb{E}[y_t]$와 $\mathbb{E}[y_t^2]$이 시간에 따라 변하지 않는다.
- 자기공분산 $\mathrm{cov}(y_t, y_{t+k})$가 시점 $t$에 의존하지 않고 시차 $k$에만 의존한다.

현실에서는 강정상성을 확보하기 어려우므로 조건을 약정상성으로 완화해 분석한다. 정의에 대한 더 자세한 설명은 [2편](02-stationarity-and-unit-root-test.md)에 있다.

### 비정상의 예 1: Break

$$
y_t =
\begin{cases}
\beta + e_t & \text{if } t \le k \\
\beta + \lambda + e_t & \text{if } t > k
\end{cases}
$$

시점 $k$를 기준으로 데이터를 만들어 내는 과정이 바뀐다. 평균이 $\beta$에서 $\beta + \lambda$로 옮겨 가므로 통계적 특성이 변한 것이고, 평균 이동(mean shift)이라는 대표적인 비정상의 예다.

### 비정상의 예 2: Random walk

$$
y_t = y_{t-1} + e_t
$$

$e_t$는 white noise다. 현재 값이 바로 앞의 값과 white noise만으로 정해진다. 단위근 과정(unit root process)이라고도 부른다. 약정상성의 조건을 하나씩 확인해 본다.

**평균.** 식을 처음 시점까지 풀어 쓴다.

$$
\begin{aligned}
y_t &= y_{t-1} + e_t \\
&= y_{t-2} + e_{t-1} + e_t \\
&= \cdots \\
&= y_0 + \sum_{i=1}^{t} e_i
\end{aligned}
$$

$y_0$을 고정된 시작 값으로 두면 $\mathbb{E}[e_i] = 0$이므로

$$
\mathbb{E}[y_t] = y_0 + \mathbb{E}\Big[\sum_{i=1}^{t} e_i\Big] = y_0
$$

평균은 $y_0$으로 일정하다.

**분산.** 상수 $y_0$은 분산에 관여하지 않고, 서로 다른 시점의 $e_i$는 상관이 없으므로 분산을 각각 더할 수 있다.

$$
\mathrm{Var}(y_t) = \mathrm{Var}\Big(y_0 + \sum_{i=1}^{t} e_i\Big) = \sum_{i=1}^{t}\mathrm{Var}(e_i) = t\sigma^2
$$

분산이 시간에 따라 커진다. 분산이 일정하지 않으므로 여기서 이미 비정상이다.

**자기공분산.** $y_t$와 $y_{t-h}$를 각각 풀어 쓰면 $y_t = y_0 + \sum_{i=1}^{t} e_i$, $y_{t-h} = y_0 + \sum_{j=1}^{t-h} e_j$이다. 평균 $y_0$을 빼고 곱하면

$$
\mathrm{Cov}(y_t, y_{t-h}) = \mathbb{E}\Big[\sum_{i=1}^{t} e_i \sum_{j=1}^{t-h} e_j\Big] = \sum_{i=1}^{t}\sum_{j=1}^{t-h}\mathbb{E}[e_i e_j]
$$

$\mathbb{E}[e_i e_j]$는 $i = j$일 때만 $\sigma^2$이고 나머지는 0이다. $1 \le i \le t$, $1 \le j \le t-h$이므로 $i = j$인 쌍은 $t - h$개다.

$$
\mathrm{Cov}(y_t, y_{t-h}) = \sum_{i=1}^{t-h}\sigma^2 = (t-h)\sigma^2
$$

자기공분산이 시차 $h$뿐 아니라 시점 $t$에도 의존한다. 따라서 random walk는 비정상이다.

## 시차 연산자

### 정의

시차 연산자 $L$은 시계열 변수에 적용하면 한 시점 앞의 값을 돌려주는 연산자다.

$$
Ly_t = y_{t-1}, \qquad L^2 y_t = y_{t-2}, \qquad L^k y_t = y_{t-k}
$$

거듭제곱하면 그만큼 시차가 늘어난다. $L$의 다항식을 시차 다항식(lag polynomial)이라고 한다.

$$
a(L) = a_0 + a_1 L + \cdots + a_p L^p
$$

$$
a(L)y_t = a_0 y_t + a_1 y_{t-1} + a_2 y_{t-2} + \cdots + a_p y_{t-p}
$$

- 시차 다항식의 곱은 교환법칙이 성립한다: $a(L)b(L) = b(L)a(L)$.
- 어떤 시차 다항식은 역(inverse)을 가진다. $(1 - \rho L)^{-1}$은 $(1 - \rho L)(1 - \rho L)^{-1} \equiv 1$을 만족하는 것으로 정의한다.

### 역이 무한합이 되는 이유

$\lvert \rho \rvert < 1$이면 다음이 성립한다.

$$
(1 - \rho L)^{-1} = \sum_{i=0}^{\infty}\rho^i L^i
$$

공비가 $\rho L$인 무한등비급수의 합 공식 $\frac{1}{1 - \rho L} = 1 + \rho L + \rho^2 L^2 + \cdots$과 같은 모양이다. AR(1) 과정으로 이 식의 의미를 확인할 수 있다. $x_t = \rho x_{t-1} + \varepsilon_t$를 정의대로 반복해서 풀면

$$
\begin{aligned}
x_t &= \rho x_{t-1} + \varepsilon_t \\
&= \rho(\rho x_{t-2} + \varepsilon_{t-1}) + \varepsilon_t \\
&= \cdots \\
&= \varepsilon_t + \rho\varepsilon_{t-1} + \rho^2\varepsilon_{t-2} + \cdots
\end{aligned}
$$

같은 식을 시차 연산자로 쓰면

$$
x_t - \rho x_{t-1} = \varepsilon_t \quad\Longrightarrow\quad (1 - \rho L)x_t = \varepsilon_t \quad\Longrightarrow\quad x_t = (1 - \rho L)^{-1}\varepsilon_t
$$

두 결과를 맞추어 보면 다음과 같다. $\rho$는 상수이므로 시차 연산자의 영향을 받지 않는다.

$$
(1 - \rho L)^{-1}\varepsilon_t = \varepsilon_t + \rho\varepsilon_{t-1} + \rho^2\varepsilon_{t-2} + \cdots = \Big(\sum_{i=0}^{\infty}\rho^i L^i\Big)\varepsilon_t
$$

AR 모델을 MA 표현으로 전개할 수 있다는 뜻이기도 하다.

### 정의를 만족하는지 확인

위에서 구한 무한합이 정말 $(1 - \rho L)$의 역인지는 곱해 보면 알 수 있다.

$$
\begin{aligned}
(1 - \rho L)\sum_{i=0}^{\infty}\rho^i L^i &= \sum_{i=0}^{\infty}\rho^i L^i - \rho L\,(\rho^0 L^0 + \rho^1 L^1 + \cdots) \\
&= \sum_{i=0}^{\infty}\rho^i L^i - (\rho^1 L^1 + \rho^2 L^2 + \cdots) \\
&= \sum_{i=0}^{\infty}\rho^i L^i - \sum_{i=1}^{\infty}\rho^i L^i \\
&= \rho^0 L^0 = 1
\end{aligned}
$$

### 무한합은 정말 수렴하는가

여기까지는 형식적인 계산이다. 궁금했던 것은 이 무한합이 실제로 의미 있는 값으로 수렴하는지, 그리고 수렴한 결과가 여전히 약정상인지였다.

**왜 $L^2$ 수렴을 보는가.** 확률 변수의 열이 수렴한다는 것을 말하려면 어떤 의미의 수렴인지 정해야 한다. 시계열에서는 보통 $L^2$ 수렴, 즉 평균제곱오차(MSE) 기준으로 점점 가까워지는지를 본다. 처음 $J$개 항까지만 더한 부분합을 $x_t^{(J)} = \sum_{i=0}^{J}\rho^i\varepsilon_{t-i}$, 무한합 전체를 $x_t$라 하면 $L^2$ 수렴은 다음을 뜻한다.

$$
\mathbb{E}\big[(x_t^{(J)} - x_t)^2\big] \to 0 \quad \text{as } J \to \infty
$$

$L^2$ 공간은 $\lVert x \rVert^2_{L^2} = \mathbb{E}[x^2] < \infty$인 확률 변수들의 공간이다. 여기에 속한다는 것은 분산이 발산하지 않는다는 뜻이다.

**Cauchy 수열.** 극한이 무엇인지 모르는 상태에서 수렴을 보이려면 부분합들이 서로 가까워지는지를 확인한다.

$$
\mathbb{E}\big[(x_t^{(J)} - x_t^{(j)})^2\big] \to 0 \quad \text{as } \min(J, j) \to \infty
$$

이 조건을 만족하는 열을 Cauchy 수열이라고 한다. 각 부분합이 $L^2$ 공간에 속한다는 것만으로는 Cauchy 수열이 되지 않으므로 따로 확인해야 한다. $J > j$일 때 두 부분합의 차이는 $\sum_{i=j+1}^{J}\rho^i\varepsilon_{t-i}$이고, 서로 다른 시점의 $\varepsilon$은 상관이 없으므로

$$
\mathbb{E}\Big[\Big(\sum_{i=j+1}^{J}\rho^i\varepsilon_{t-i}\Big)^2\Big] = \sigma^2\sum_{i=j+1}^{J}\rho^{2i}
$$

이다. $\lvert \rho \rvert < 1$이면 $\sum\rho^{2i}$가 수렴하는 등비급수이므로, 그 꼬리 부분인 이 값은 $j$가 커질수록 0으로 간다. 부분합의 열은 Cauchy 수열이다.

Cauchy 수열이라고 해서 언제나 극한이 있는 것은 아니고, 수열이 놓인 공간이 완비(complete)여야 한다. $L^2$ 공간은 완비 공간이다. 따라서 부분합의 열은 $L^2$ 안의 어떤 값으로 수렴하고, 그것이 $x_t$다.

강의 노트는 더 일반적으로, 약정상인 $x_t$에 대해 $y_{t,J} = \sum_{i=0}^{J}\rho^i x_{t-i}$가 $J \to \infty$에서 수렴한다는 것을 같은 방식으로 다룬다. 예를 들어 $J = 2$이면 $y_{t,2} = x_t + \rho x_{t-1} + \rho^2 x_{t-2}$다.

**수렴한 결과는 약정상인가.** 강의 노트는 이것을 자명하다고 한다. 더해지는 $x_{t-i}$들이 모두 같은 통계적 특성을 갖는 약정상 과정이고, 시점에 의존하지 않는 계수로 만든 선형 결합은 약정상성을 보존하기 때문이다.

## AR, MA, ARMA

### Autoregressive (AR)

$$
\text{AR(1)}:\quad y_t = \rho y_{t-1} + e_t \quad\Longleftrightarrow\quad (1 - \rho L)y_t = e_t, \qquad \lvert \rho \rvert < 1
$$

AR($p$)는 시차 $p$까지의 값으로 현재 값을 설명한다. 같은 방식으로 정리하면 시차 다항식 하나로 쓸 수 있다.

$$
\begin{aligned}
y_t &= a_1 y_{t-1} + a_2 y_{t-2} + \cdots + a_p y_{t-p} + e_t \\
y_t - a_1 y_{t-1} - a_2 y_{t-2} - \cdots - a_p y_{t-p} &= e_t \\
(1 - a_1 L - a_2 L^2 - \cdots - a_p L^p)\,y_t &= e_t
\end{aligned}
$$

$$
\text{AR}(p):\quad a(L)y_t = e_t, \qquad a(L) = 1 - a_1 L - \cdots - a_p L^p
$$

AR(1)에서 $\lvert \rho \rvert < 1$이라는 조건이 중요하다. AR(1)을 풀어 쓰면 과거의 모든 충격을 더한 무한합 $\sum_{i=0}^{\infty}\rho^i e_{t-i}$가 되므로, 이 값이 의미를 가지려면 합이 수렴해야 한다.

### Moving Average (MA)

MA(1)은 현재 시점의 충격과 바로 앞 시점의 충격의 선형 결합이다.

$$
\text{MA(1)}:\quad y_t = e_t + \theta e_{t-1} = (1 + \theta L)e_t
$$

$$
\text{MA}(q):\quad y_t = e_t + \theta_1 e_{t-1} + \cdots + \theta_q e_{t-q} = b(L)e_t, \qquad b(L) = 1 + \theta_1 L + \cdots + \theta_q L^q
$$

### ARMA

ARMA는 AR과 MA를 함께 쓴다.

$$
\text{ARMA}(p, q):\quad a(L)y_t = b(L)e_t
$$

$p$는 $a(L)$의 차수, $q$는 $b(L)$의 차수다. $a(L)$과 $b(L)$은 공통 인수를 갖지 않는다고(relatively prime) 둔다. 공통 인수가 있으면 양변에서 약분되어 더 낮은 차수의 모델과 같아지기 때문이다.

## AR과 MA의 상호 변환

### AR에서 MA(∞)로

AR 과정이 정상성 조건을 만족하면 MA($\infty$) 표현이 항상 존재한다. AR의 식을 재귀적으로 계속 풀면

$$
y_t = \epsilon_t + \psi_1\epsilon_{t-1} + \psi_2\epsilon_{t-2} + \cdots
$$

꼴이 된다. 약정상 과정을 이렇게 white noise의 무한 이동 평균으로 쓰는 것을 Wold 표현이라고 부른다. 이 전개가 가능하려면 AR 다항식

$$
a(z) = 1 - \phi_1 z - \cdots - \phi_p z^p
$$

의 근이 모두 단위원 밖, 즉 $\lvert z \rvert > 1$에 있어야 한다.

### 왜 단위원 밖인가

단위원(unit circle)은 복소평면에서 반지름이 1인 원이다.

$$
\{z \in \mathbb{C} : \lvert z \rvert = 1\}
$$

AR(1)로 확인해 본다. AR(1)의 MA 표현 $y_t = \epsilon_t + \rho\epsilon_{t-1} + \rho^2\epsilon_{t-2} + \cdots$이 수렴하려면 $\lvert \rho \rvert < 1$이어야 한다. 이것을 근의 관점에서 다시 보면, AR(1)의 시차 다항식은 $a(L) = 1 - \rho L$이고 특성 방정식 $1 - \rho z = 0$의 근은 $z = 1/\rho$다. 수렴 조건 $\lvert \rho \rvert < 1$을 넣으면 $\lvert 1/\rho \rvert > 1$이다. 계수가 1보다 작다는 조건과 근이 단위원 밖에 있다는 조건은 같은 말이다.

근의 위치에 따라 충격이 어떻게 되는지를 정리하면 다음과 같다.

| 근의 위치 | AR(1)의 계수 | 과거 충격의 영향 |
|---|---|---|
| 단위원 밖 | $\lvert \rho \rvert < 1$ | $\rho^i$로 줄어든다. 충격을 흡수하고 원래 수준으로 돌아온다. 정상 |
| 단위원 위 | $\lvert \rho \rvert = 1$ | 줄어들지 않고 계속 남는다. Random walk, 단위근 과정 |
| 단위원 안 | $\lvert \rho \rvert > 1$ | 시간이 갈수록 커진다. 발산한다 |

[2편](02-stationarity-and-unit-root-test.md)의 단위근 검정은 가운데 줄에 해당하는지를 데이터로 검정하는 것이다.

### MA에서 AR(∞)로

반대 방향도 같은 생각으로 된다. MA 과정이 가역성(invertibility) 조건을 만족하면 AR($\infty$) 표현이 존재한다. MA를 AR로 바꾸려면 $y_t = b(L)e_t$를 $e_t = b(L)^{-1}y_t$로 뒤집을 수 있어야 하고, 그러려면 MA 다항식 $b(z)$의 근이 단위원 밖에 있어야 한다. MA(1)로 확인한다.

$$
y_t = (1 + \theta L)e_t \quad\Longrightarrow\quad e_t = (1 + \theta L)^{-1}y_t
$$

앞에서 $(1 - \rho L)^{-1} = \sum_i \rho^i L^i$가 성립하려면 $\lvert \rho \rvert < 1$이어야 했다. $\rho$ 자리에 $-\theta$를 넣으면 조건은 $\lvert \theta \rvert < 1$이 되고,

$$
e_t = \Big(\sum_{i=0}^{\infty}(-\theta)^i L^i\Big)y_t = \sum_{i=0}^{\infty}(-\theta)^i y_{t-i}
$$

현재 충격이 현재와 과거의 관측값으로 표현된다. 이것이 AR($\infty$) 표현이다.

정리하면, 정상성이 있으면 AR($p$)를 MA($\infty$)로, 가역성이 있으면 MA($q$)를 AR($\infty$)로 바꿀 수 있다.

## 공분산 구조

분산, 공분산, 자기공분산, 자기상관의 정의는 [3편](03-autocovariance-acf-pacf.md)에 있다. 여기서는 표기만 정한다. 시차 $k$의 자기공분산을 $\gamma_k = \mathrm{cov}(y_t, y_{t+k})$, 자기상관을 $\gamma_k / \gamma_0$으로 쓴다.

### AR(1)의 분산

$y_t = \rho y_{t-1} + e_t$의 양변에 분산을 취한다. $y_{t-1}$은 시점 $t-1$까지의 충격만으로 만들어지므로 $e_t$와 상관이 없고, 따라서 합의 분산을 분산의 합으로 나눌 수 있다. 정상 과정이므로 $\mathrm{Var}(y_t) = \mathrm{Var}(y_{t-1}) = \gamma_0$이다.

$$
\begin{aligned}
\mathrm{Var}(y_t) &= \mathrm{Var}(\rho y_{t-1}) + \mathrm{Var}(e_t) \\
\gamma_0 &= \rho^2\gamma_0 + \sigma^2 \\
\gamma_0 &= \frac{\sigma^2}{1 - \rho^2}
\end{aligned}
$$

MA 표현에서 출발해도 같은 값이 나온다.

$$
\mathrm{Var}(y_t) = \mathrm{Var}\Big(\sum_{i=0}^{\infty}\rho^i e_{t-i}\Big) = \sum_{i=0}^{\infty}\rho^{2i}\,\mathrm{Var}(e_{t-i}) = \sigma^2\sum_{i=0}^{\infty}\rho^{2i} = \frac{\sigma^2}{1 - \rho^2}
$$

마지막 등호에서 등비급수가 수렴하려면 $\lvert \rho \rvert < 1$이어야 한다. 정상성 조건이 여기서 다시 나타난다.

### AR(1)의 자기공분산

MA 표현을 써서 계산한다. 먼저 white noise의 성질을 확인해 둔다. $e_t$의 평균이 0이므로 $\mathrm{Cov}(e_t, e_{t+k}) = \mathbb{E}[e_t e_{t+k}]$이고, 이 값은 두 시점이 다르면 0, 같으면 $\mathrm{Var}(e_t) = \sigma^2$이다.

$$
\begin{aligned}
\gamma_k = \mathrm{Cov}(y_t, y_{t+k}) &= \mathrm{Cov}\Big(\sum_{i}\rho^i e_{t-i},\ \sum_{j}\rho^j e_{t+k-j}\Big) \\
&= \sum_{i}\sum_{j}\rho^i\rho^j\,\mathrm{Cov}(e_{t-i}, e_{t+k-j})
\end{aligned}
$$

공분산 항은 $t - i = t + k - j$, 즉 $j = i + k$일 때만 $\sigma^2$으로 살아남는다.

$$
\gamma_k = \sigma^2\sum_{i=0}^{\infty}\rho^{i + (i + k)} = \sigma^2\rho^k\sum_{i=0}^{\infty}\rho^{2i} = \frac{\rho^k\sigma^2}{1 - \rho^2}
$$

따라서 AR(1)의 자기상관은 $\gamma_k / \gamma_0 = \rho^k$다. 시차가 늘어날수록 지수적으로 줄어든다. [3편](03-autocovariance-acf-pacf.md)의 표에서 AR의 ACF가 지수적으로 감소한다고 한 것이 이 결과다.

### 일반적인 MA(∞) 과정

같은 계산을 일반화할 수 있다. 시계열이 $y_t = \sum_{i=0}^{\infty}c_i e_{t-i}$로 표현되면

$$
\gamma_k = \mathrm{Cov}\Big(\sum_{i=0}^{\infty}c_i e_{t-i},\ \sum_{j=0}^{\infty}c_j e_{t+k-j}\Big) = \sigma^2\sum_{l=0}^{\infty}c_l c_{l+k}
$$

### 공분산 정상성

$y_t = \sum_{i=0}^{\infty}c_i e_{t-i}$에서 $\sum_{j=0}^{\infty}c_j^2 < \infty$이면 $y_t$는 covariance stationary하다. Covariance stationary는 약정상성과 같은 말이다. 세 조건을 하나씩 확인하면 된다.

1. $\mathbb{E}[y_t] = \sum_i c_i\,\mathbb{E}[e_{t-i}] = 0$. 평균이 일정하다.
2. $\mathrm{Var}(y_t) = \sum_i c_i^2\,\mathrm{Var}(e_{t-i}) = \sigma^2\sum_i c_i^2$. $\sum_i c_i^2 < \infty$이면 분산이 유한하고 시간에 따라 일정하다.
3. $\mathrm{Cov}(y_t, y_{t-k}) = \sum_{i,j}c_i c_j\,\mathrm{Cov}(e_{t-i}, e_{t-k-j}) = \sigma^2\sum_{j}c_{j+k}c_j$. 시차 $k$에만 의존한다.

AR(1)은 $c_i = \rho^i$인 경우이고, $\sum\rho^{2i} < \infty$가 곧 $\lvert \rho \rvert < 1$이다.

## 공분산 함수

자기공분산은 보통 $\gamma_0, \gamma_1, \dots$처럼 시차별로 하나씩 다룬다. 이것을 급수 하나에 모은 것이 공분산 함수(covariance function)다.

$$
\gamma(\xi) = \sum_{k=-\infty}^{\infty}\gamma_k\,\xi^k
$$

$\gamma_k$는 시차 $k$의 자기공분산이고 $\xi$는 복소수다. 시계열의 자기공분산 구조 전체를 하나의 형식적 멱급수로 담은 것이다. $\xi = e^{-i\omega}$($i$는 허수 단위)를 넣으면 주파수 $\omega$에 대한 함수가 되므로, 시계열을 주파수 영역에서 분석하는 스펙트럼 분석에 쓰인다. 푸리에 변환에 대한 메모는 [9편](09-fourier-transform.md)에 있다.

### MA 표현의 공분산 함수

$y_t = c(L)e_t$이면 $\gamma(\xi) = \sigma^2 c(\xi)c(\xi^{-1})$이다. $c(\xi) = \sum_{i=0}^{\infty}c_i\xi^i$, $c(\xi^{-1}) = \sum_{j=0}^{\infty}c_j\xi^{-j}$를 곱해 보면 된다.

$$
c(\xi)c(\xi^{-1}) = \Big(\sum_{i}c_i\xi^i\Big)\Big(\sum_{j}c_j\xi^{-j}\Big) = \sum_{i}\sum_{j}c_i c_j\,\xi^{i-j}
$$

$k = i - j$로 치환한다. $i, j \ge 0$이므로 $k$의 범위는 $-\infty$부터 $+\infty$까지가 된다.

$$
c(\xi)c(\xi^{-1}) = \sum_{k=-\infty}^{\infty}\xi^k\sum_{j=0}^{\infty}c_j c_{j+k}
$$

안쪽 합에 $\sigma^2$을 곱한 것이 앞에서 구한 $\gamma_k$다. 따라서

$$
\sigma^2 c(\xi)c(\xi^{-1}) = \sum_{k=-\infty}^{\infty}\gamma_k\,\xi^k = \gamma(\xi)
$$

### ARMA의 공분산 함수

$a(L)y_t = b(L)e_t$를 $y_t = \frac{b(L)}{a(L)}e_t$로 쓰고 $c(L) = b(L)/a(L)$로 두면 MA 표현 $y_t = c(L)e_t$가 된다. 위의 결과에 그대로 대입한다.

$$
\gamma(\xi) = \sigma^2\,\frac{b(\xi)\,b(\xi^{-1})}{a(\xi)\,a(\xi^{-1})}
$$

MA의 결과만 있으면 대입만으로 얻어진다.

## 정리

- Random walk는 평균은 일정하지만 분산이 $t\sigma^2$, 자기공분산이 $(t-h)\sigma^2$으로 시점에 의존하므로 비정상이다.
- 시차 연산자로 쓰면 AR은 $a(L)y_t = e_t$, MA는 $y_t = b(L)e_t$, ARMA는 $a(L)y_t = b(L)e_t$다.
- $\lvert \rho \rvert < 1$이면 $(1 - \rho L)^{-1} = \sum_i\rho^i L^i$이고, 이것으로 AR(1)을 MA($\infty$)로 바꿀 수 있다. 같은 방법으로 $\lvert \theta \rvert < 1$인 MA(1)을 AR($\infty$)로 바꿀 수 있다.
- "근이 단위원 밖에 있어야 한다"는 조건은 AR(1)에서 $\lvert \rho \rvert < 1$과 같은 말이고, 과거 충격의 영향이 줄어들어 무한합이 수렴한다는 뜻이다.
- AR(1)의 분산은 $\sigma^2/(1-\rho^2)$, 시차 $k$의 자기공분산은 $\rho^k\sigma^2/(1-\rho^2)$이다.

## 참고 자료

- A. Mikusheva, MIT OpenCourseWare 14.384 "Time Series Analysis" (Fall 2013), Lecture 1: Stationarity, Lag Operator, ARMA, and Covariance Structure. <https://ocw.mit.edu/courses/14-384-time-series-analysis-fall-2013/resources/mit14_384f13_lec1/>
