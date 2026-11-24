---
title: "AIC와 BIC"
date: 2025-04-03
last_modified_at: 2025-04-03
category: study
tags: [시계열, AIC, BIC, KL Divergence, 가능도, 모델 선택]
---

# AIC와 BIC

작성일 2025-04-03
{ .post-date }

[AR 모델](04-ar-and-var.md)을 쓰려면 과거 값을 몇 시점까지 볼지, 즉 차수 $p$를 정해야 한다. 차수를 늘리면 주어진 데이터에는 더 잘 맞지만, 모델이 복잡해져 새로운 데이터에는 오히려 맞지 않을 수 있다(overfitting). AIC와 BIC는 "데이터를 얼마나 잘 설명하는가"와 "모델이 얼마나 복잡한가"를 한 숫자로 합쳐, 그 값이 가장 작은 모델을 고르게 해 주는 기준이다.

이 글에서는 두 기준이 각각 어떤 생각에서 나왔는지(AIC는 KL divergence, BIC는 Bayesian posterior)와, AR($p$) 모델에서 실제로 계산에 들어가는 가능도(likelihood)를 어떻게 구하는지 정리한다. 공부하며 가졌던 질문 네 개도 함께 적는다.

## AIC

AIC(Akaike Information Criterion)의 식은 다음과 같다.

$$
\mathrm{AIC} = -2\log(\hat{L}) + 2k
$$

- $\hat{L}$: 모델의 최대 가능도(maximum likelihood)
- $k$: 모델의 파라미터 수

첫 항은 모델이 데이터를 잘 설명할수록 작아지고, 둘째 항은 파라미터가 많을수록 커진다.

### 정보 손실을 줄인다는 관점

AIC가 하려는 일은 데이터를 실제로 만들어 낸 분포 $f(x)$와 모델의 분포 $g(x \mid \theta)$ 사이의 거리를 가장 작게 만드는 것이다. $f(x)$는 알 수 없지만, 두 분포의 거리(정보 손실)는 KL divergence로 정의할 수 있다.

$$
\begin{aligned}
D_{KL}(f \,\|\, g) &= \int f(x)\log\frac{f(x)}{g(x \mid \theta)}\,dx \\
&= \int f(x)\log f(x)\,dx - \int f(x)\log g(x \mid \theta)\,dx \\
&= \mathbb{E}_f[\log f(x)] - \mathbb{E}_f[\log g(x \mid \theta)]
\end{aligned}
$$

여기서 $\mathbb{E}_f[h(x)] = \int f(x)h(x)\,dx$는 $x$가 $f$를 따를 때 $h(x)$의 기댓값이다. $f(x)$가 가중치 역할을 한다.

첫 항 $\mathbb{E}_f[\log f(x)]$는 이미 정해져 있는 분포 $f$만으로 결정되므로, 어떤 모델을 고르든 변하지 않는 상수다. 따라서 KL divergence를 최소화하는 것은 둘째 항 $\mathbb{E}_f[\log g(x \mid \theta)]$를 최대화하는 것과 같다.

문제는 이 기댓값도 $f$를 알아야 계산할 수 있다는 것이다. 그래서 $f$에서 나온 표본, 즉 우리가 가진 데이터의 평균으로 근사한다.

$$
\mathbb{E}_f[\log g(x)] \approx \frac{1}{n}\sum_{i=1}^{n}\log g(x_i)
$$

우변의 합 $\sum_i \log g(x_i)$가 바로 로그 가능도(log-likelihood)다. 정리하면 AIC는 차수 $p$까지로 적합한 모델의 로그 가능도로 "모델 분포가 실제 분포에 얼마나 가까운가"를 근사하는 것이다.

다만 같은 데이터로 $\theta$를 추정하고 그 데이터로 다시 평가하므로, 이 근사는 실제보다 낙관적인 쪽으로 치우친다. 치우친 크기가 근사적으로 파라미터 수 $k$만큼이라는 것이 Akaike의 결과이고, 그것을 보정한 것이 벌점 항 $2k$다.

### 질문 1. $g(x)$가 $f(x)$보다 커지면 문제가 되지 않는가?

KL divergence의 식에는 $\log\frac{f(x)}{g(x)}$가 들어 있다. $g(x) > f(x)$인 곳에서는 이 값이 음수가 되는데, 그러면 "거리"가 음수가 될 수도 있는 것 아닌가 하는 의문이 들었다.

노트에는 "항상 $g(x) \le f(x)$라고 한다"고 적어 두었지만 확인해 보니 사실이 아니다. $f$와 $g$는 둘 다 전체를 적분하면 1이 되는 확률밀도함수다. 어떤 구간에서 $g$가 $f$보다 작으면 다른 구간에서는 반드시 $g$가 $f$보다 커야 한다. 두 분포가 완전히 같지 않은 한 $g(x) > f(x)$인 곳은 항상 있다.

그래도 문제가 되지 않는 이유는, 각 점의 $\log$ 값은 음수가 될 수 있어도 $f(x)$로 가중해 적분한 전체 값은 음수가 될 수 없기 때문이다. $-\log$가 볼록 함수이므로 Jensen 부등식을 적용하면 다음과 같다.

$$
D_{KL}(f \,\|\, g) = \mathbb{E}_f\!\left[-\log\frac{g(x)}{f(x)}\right] \ge -\log \mathbb{E}_f\!\left[\frac{g(x)}{f(x)}\right] = -\log\int g(x)\,dx = -\log 1 = 0
$$

등호는 두 분포가 같을 때만 성립한다.

### 질문 2. 로그 가능도로 근사하는 것이 왜 표본 평균으로 근사하는 것인가?

위의 근사식이 답이다. AR 모델에서는 아래에서 보듯이 시점마다 조건부 정규분포의 밀도 값이 하나씩 나온다. 그 하나하나가 표본 $\log g(x_i)$이고, 전체 로그 가능도는 그것들을 더한 값이다.

- 표본 평균이 되려면 합을 표본 수로 나누어야 한다. AIC에서 나누지 않는 것은, 같은 데이터에서 서로 다른 차수의 모델을 비교할 때는 나누든 나누지 않든 대소 관계가 같기 때문이다.
- 엄밀히 하면 주의할 점이 있다. AR($p$)의 조건부 가능도에는 항이 $N - p$개 들어가므로 차수마다 항의 수가 조금씩 다르다. 차수를 비교할 때는 모든 후보가 같은 구간의 데이터를 쓰도록 맞추는 것이 원칙이다.
- $-2$를 곱하는 것은 가능도비 검정(likelihood ratio test)과 척도를 맞추는 통계적 관행이다. 가능도비에 $-2\log$를 취한 값이 근사적으로 카이제곱 분포를 따르기 때문에, 가능도를 다룰 때는 $-2\log L$ 단위가 널리 쓰인다. 노트를 쓸 때는 이 설명이 무슨 뜻인지 이해하지 못한 채로 넘어갔다.

## AR(p) 모델의 가능도

AIC와 BIC 모두 $\hat{L}$이 필요하다. AR($p$) 모델에서 이것을 어떻게 구하는지 따라가 본다.

### 로그 가능도

가능도는 모델과 파라미터가 주어졌을 때 관측한 데이터가 얼마나 그럴듯한지를 수치로 나타낸 것이다. 데이터 $D$를 고정하고 파라미터 $\theta$의 함수로 본 $p(D \mid \theta)$다. 확률을 여러 개 곱하면 값이 지나치게 작아지므로 로그를 취해 곱을 합으로 바꾼다.

### 조건부 정규성

잡음이 정규분포를 따르는 AR($p$) 모델을 생각한다.

$$
X_t = \phi_1 X_{t-1} + \phi_2 X_{t-2} + \cdots + \phi_p X_{t-p} + \epsilon_t, \qquad \epsilon_t \sim N(0, \sigma^2)
$$

먼저 일반적인 회귀를 떠올리면 이해하기 쉽다. 잔차가 정규분포를 따르는 회귀 $Y = \beta X + \epsilon$, $\epsilon \sim N(0, \sigma^2)$에서 $X$가 값 $x$로 주어지면 $\beta x$는 더 이상 확률 변수가 아니라 고정된 값이다. 그러면 $Y$는 상수에 정규분포 잡음을 더한 것이므로 평균이 $\beta x$로 옮겨진 정규분포를 따른다. 즉 $Y$는 $X$가 주어졌을 때 조건부로 정규분포다.

AR($p$)도 같다. 과거 값 $X_{t-1}, \dots, X_{t-p}$가 주어지면 선형 결합 부분은 고정된 값이 되고, 남는 확률적 성분은 $\epsilon_t$뿐이다.

$$
X_t \mid X_{t-1}, \dots, X_{t-p} \ \sim\ N(\mu_t, \sigma^2), \qquad \mu_t = \phi_1 X_{t-1} + \phi_2 X_{t-2} + \cdots + \phi_p X_{t-p}
$$

$\mu_t$는 모델이 시점 $t$의 값으로 예측한 값이고, 그 시점 조건부 정규분포의 평균이다. 시점 하나의 밀도는 다음과 같다.

$$
P(X_t \mid X_{t-1}, \dots, X_{t-p}) = \frac{1}{\sqrt{2\pi\sigma^2}}\exp\!\left(-\frac{(X_t - \mu_t)^2}{2\sigma^2}\right)
$$

### 계수와 분산의 추정

차수 $p$가 정해지면 계수를 찾는 일은 선형 회귀다. $p = 3$이고 데이터가 $X_1, X_2, \dots, X_{10}$이라면 입력 행렬과 목표 벡터를 다음과 같이 만든다.

$$
\mathbf{X} =
\begin{bmatrix}
X_3 & X_2 & X_1 \\
X_4 & X_3 & X_2 \\
\vdots & \vdots & \vdots \\
X_9 & X_8 & X_7
\end{bmatrix} \in \mathbb{R}^{7 \times 3},
\qquad
\mathbf{y} =
\begin{bmatrix}
X_4 \\ X_5 \\ \vdots \\ X_{10}
\end{bmatrix} \in \mathbb{R}^{7}
$$

처음 세 시점은 과거 값 세 개를 갖추지 못하므로 목표가 될 수 없다. 그래서 행이 10개가 아니라 7개다. 이 행렬과 벡터로 다음 최소화 문제를 푼다.

$$
\min_{\phi}\ \sum_{t=p+1}^{N}\Big(X_t - \sum_{i=1}^{p}\phi_i X_{t-i}\Big)^2
$$

전역 최솟값이 있는 볼록 최적화 문제다. 풀어서 계수 벡터 $\phi = [\phi_1, \phi_2, \phi_3]^{\top}$을 얻고, 그 계수로 $t = 4, \dots, 10$의 예측값 $\hat{X}_t$를 계산한다.

분산 $\sigma^2$은 잔차를 모두 모아 평균제곱오차로 추정한다.

$$
\hat{\sigma}^2 = \frac{1}{N-p}\sum_{t=p+1}^{N}(X_t - \hat{X}_t)^2
$$

시점마다 분산을 따로 추정하지 않는 것은, AR 모델이 오차가 등분산(homoskedastic)이고 i.i.d.라고 가정하기 때문이다.

### 전체 가능도

시점별 조건부 밀도를 모두 곱하면 전체 가능도가 된다. 곱은 $t = p+1$부터 시작한다. 그 앞의 시점은 시차 $p$개를 채울 데이터가 없어 조건부 밀도를 계산할 수 없다.

$$
L(\theta) = \prod_{t=p+1}^{N} P(X_t \mid X_{t-1}, \dots, X_{t-p})
$$

로그를 취해 곱을 합으로 바꾼다.

$$
\begin{aligned}
\log L(\theta) &= \sum_{t=p+1}^{N}\log\left[\frac{1}{\sqrt{2\pi\sigma^2}}\exp\!\left(-\frac{(X_t - \mu_t)^2}{2\sigma^2}\right)\right] \\
&= -\frac{N-p}{2}\log(2\pi\sigma^2) - \frac{1}{2\sigma^2}\sum_{t=p+1}^{N}(X_t - \mu_t)^2
\end{aligned}
$$

앞의 항은 상수를 $N - p$번 더한 것이라 계수 $(N-p)$가 붙는다. 추정한 $\hat{\phi}$와 $\hat{\sigma}^2$을 대입하면 뒤의 합이 $(N-p)\hat{\sigma}^2$이 되어 식이 더 간단해진다.

$$
\log\hat{L} = -\frac{N-p}{2}\big(\log(2\pi\hat{\sigma}^2) + 1\big)
$$

잔차의 분산이 작을수록 최대 로그 가능도가 크다.

### AIC의 계산

이렇게 구한 $\log\hat{L}$을 $\mathrm{AIC} = -2\log(\hat{L}) + 2k$에 넣는다. 노트에서는 차수가 $p$일 때 $k = p + 1$로 두었다. 계수 $p$개에 잡음의 분산 $\sigma^2$ 하나를 더한 수로 볼 수 있고, 상수항을 두면 하나가 더 늘어난다. 어떻게 세든 후보 모델을 모두 같은 방식으로 세면 비교에는 지장이 없다.

## BIC

### 아이디어

BIC(Bayesian Information Criterion)는 AIC와 모양이 비슷하지만 출발점이 다르다.

$$
\mathrm{BIC} = -2\log(\hat{L}) + k\log(N)
$$

- $N$: 관측치의 개수
- $k$: 모델의 파라미터 수

BIC는 Bayesian posterior에서 나온다. 관측한 데이터 $D$가 있을 때, 차수가 $p$인 모델 $M_p$가 이 데이터에 맞는 모델일 사후 확률이 얼마인지를 보는 것이다. 벌점이 $k\log N$이므로 모델이 복잡할수록, 그리고 표본이 많을수록 벌점이 커진다. $N \ge 8$이면 $\log N > 2$이므로 AIC보다 엄격하다.

차수 $p$가 크면 AR 모델은 주어진 데이터에 더 잘 맞는다. 그러나 차수가 지나치게 크면 모델이 복잡해져 새로운 데이터에는 잘 맞지 않을 수 있다. 복잡도에 벌점을 주어 지금 데이터에 가장 적절한 차수를 찾는다는 점은 AIC와 같다.

### Bayes 규칙으로 유도하기

Bayes 규칙에 따르면 모델의 사후 확률은 주변 가능도(marginal likelihood)와 사전 확률의 곱에 비례한다.

$$
P(M_p \mid D) \propto P(D \mid M_p)\,P(M_p)
$$

주변 가능도는 파라미터에 대해 적분한 값이다.

$$
P(D \mid M_p) = \int P(D \mid \theta_p, M_p)\,P(\theta_p \mid M_p)\,d\theta_p
$$

이 적분은 일반적으로 계산하기 어려워 Laplace 근사를 쓴다. 적분 안의 로그 값을 가능도가 최대인 점 $\hat{\theta}_p$ 주변에서 2차식으로 전개하면 적분이 정규분포 모양의 적분으로 바뀌어 닫힌 형태로 풀린다. 이때 2차식의 곡률이 표본 수 $N$에 비례해 커지고 파라미터가 $k$개이므로, 적분 결과에 $N^{-k/2}$ 크기의 인자가 생긴다. 로그를 취하면 다음과 같다.

$$
\log P(D \mid M_p) \approx \log P(D \mid \hat{\theta}_p) - \frac{k}{2}\log N + \text{const}
$$

- $\hat{\theta}_p$: MLE로 추정한 파라미터
- $k$: 파라미터의 수
- $N$: 데이터의 수

$N$에 따라 커지지 않는 상수는 버리고 양변에 $-2$를 곱하면 BIC의 식이 된다. BIC가 작다는 것은 주변 가능도가 크다는 뜻이다.

### 질문 3. MLE로 파라미터를 구한다는 것은 선형 회귀로 계수를 찾는다는 뜻인가?

그렇다. 선형 회귀를 최적화 관점에서 푸는 것을 MLE로 해석할 수 있다. 다만 이것은 $\epsilon \sim N(0, \sigma^2)$라는 가정 아래에서의 해석이다.

위에서 구한 로그 가능도를 다시 보면,

$$
\log L(\theta) = -\frac{N-p}{2}\log(2\pi\sigma^2) - \frac{1}{2\sigma^2}\sum_{t=p+1}^{N}(X_t - \mu_t)^2
$$

계수 $\phi$가 들어 있는 곳은 둘째 항의 합뿐이다. 따라서 가능도를 최대화하는 것은 잔차 제곱합(residual sum of squares)을 최소화하는 것과 같다. 이것은 선형 회귀의 OLS와 같은 문제다. 잔차가 정규분포를 따른다고 가정하면 MLE와 OLS는 같은 계수를 준다.

### 질문 4. Posterior는 likelihood와 prior의 곱인데, BIC에서는 왜 prior를 무시하는가?

차수 $p$만 다른 같은 종류의 모델끼리 비교하기 때문이다. 모든 후보 모델이 같은 사전 확률 $P(M_p)$를 갖는다고 가정하면(non-informative prior), 이 항은 어떤 모델에서나 같은 값이므로 비교할 때 생략해도 결과가 달라지지 않는다.

## 정리

| | AIC | BIC |
|---|---|---|
| 식 | $-2\log(\hat{L}) + 2k$ | $-2\log(\hat{L}) + k\log(N)$ |
| 출발점 | 실제 분포와 모델 분포 사이의 KL divergence 최소화 | 모델의 사후 확률(주변 가능도) 최대화 |
| 벌점 | 파라미터 수에만 비례 | 파라미터 수와 표본 수의 로그에 비례 |

- 두 기준 모두 값이 작은 모델을 고른다.
- AR($p$)의 가능도는 "과거 값이 주어지면 현재 값은 정규분포를 따른다"는 조건부 정규성에서 나오고, 시점별 조건부 밀도를 $t = p+1$부터 곱해 구한다.
- 잡음이 정규분포라고 가정하면 MLE로 구한 계수는 OLS로 구한 계수와 같다.

두 기준이 고른 차수와 실제 예측 성능이 좋은 차수가 일치하는지는 [AirPassengers 정상화와 AR 차수 선택](../../research/sailab/06-ar-order-selection.md)에서 실험으로 비교했다.

## 참고 자료

이 노트는 참고한 자료를 따로 기록해 두지 않았다. 두 기준을 처음 제안한 논문을 적어 둔다.

- H. Akaike, "A new look at the statistical model identification", IEEE Transactions on Automatic Control, 19(6), 1974. <https://doi.org/10.1109/TAC.1974.1100705>
- G. Schwarz, "Estimating the dimension of a model", The Annals of Statistics, 6(2), 1978. <https://doi.org/10.1214/aos/1176344136>
