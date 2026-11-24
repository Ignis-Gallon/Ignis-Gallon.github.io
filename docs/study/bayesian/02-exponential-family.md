---
title: "지수족: 여러 분포를 하나의 꼴로 쓰기"
date: 2025-03-04
last_modified_at: 2025-03-07
category: study
tags: [베이즈 추론, 지수족, 충분통계량, MLE, 켤레 사전분포]
---

# 지수족: 여러 분포를 하나의 꼴로 쓰기

작성일 2025-03-04 · 최종 수정일 2025-03-07
{ .post-date }

Gaussian, Bernoulli, Poisson, Beta처럼 따로 배운 분포들은 사실 하나의 식으로 묶인다. 이 묶음을 지수족(exponential family)이라고 한다. 지수족에 속하는 분포는 MLE가 깔끔한 조건 하나로 정리되고, conjugate prior도 언제나 같은 방식으로 만들 수 있어서 베이즈 추론에서 계산하기 편하다. [1편](01-conjugate-prior.md)에서 본 Bernoulli와 Beta의 관계가 그 한 예다.

이 글은 두 자료를 보고 정리한 노트를 합친 것이다. 하나는 Mutual Information 채널의 YouTube 강의 "The Exponential Family" 1, 2편이고, 다른 하나는 Gregory Gundersen의 블로그 글 "The Exponential Family"다. 강의는 $h$, $t$를 직접 골라 분포를 만들어 가는 방향으로, 블로그는 익숙한 분포의 식을 변형해 지수족 꼴로 바꾸는 방향으로 설명한다. 이 글에서는 두 방향을 함께 쓴다. 공부하면서 적은 질문과 그에 대해 찾은 답도 해당하는 자리에 함께 적었다.

## 표기

두 자료와 변화점 탐지 논문(BOCPD)의 표기가 서로 달라 처음에 헷갈렸다. 이 글에서는 아래 첫 열의 표기로 통일한다.

| 이 글 | YouTube 강의 | 블로그 | 의미 |
|---|---|---|---|
| $\eta$ | $\theta$ | $\eta$ | natural parameter(자연 모수) |
| $t(x)$ | $t(x)$ | $u(x)$ 또는 $T(x)$ | sufficient statistic(충분통계량) |
| $h(x)$ | $h(x)$ | $h(x)$ | base measure |
| $Z(\eta)$ | $Z(\theta)$ | $1 / g(\eta)$ | normalizer(정규화 상수) |
| $A(\eta) = \log Z(\eta)$ | $\log Z(\theta)$ | $A(\eta) = -\log g(\eta)$ | log-normalizer |

## 출발점: Gaussian의 MLE

히스토그램으로 그렸을 때 종 모양으로 보이는 데이터가 있다고 하자. 이 데이터를 만든 Gaussian의 모수를 구하는 가장 쉬운 방법은 MLE다. 표본이 서로 독립이므로 각 표본의 밀도를 곱한 것을 최대화한다.

$$
\mu^*, \sigma^* = \arg\max_{\mu, \sigma} \prod_{i=1}^{N} \mathcal{N}(x_i \mid \mu, \sigma)
$$

여기에 Gaussian의 식을 넣고 지수 안의 제곱을 전개한다.

$$
\begin{aligned}
\mathcal{N}(x_i \mid \mu, \sigma) &= \frac{1}{\sigma\sqrt{2\pi}} \exp\left(-\frac{(x_i-\mu)^2}{2\sigma^2}\right) \\
&= \frac{1}{\sigma\sqrt{2\pi}\, \exp\left(\frac{\mu^2}{2\sigma^2}\right)} \exp\left( \begin{bmatrix} x_i & x_i^2 \end{bmatrix} \begin{bmatrix} \dfrac{\mu}{\sigma^2} \\[2mm] -\dfrac{1}{2\sigma^2} \end{bmatrix} \right)
\end{aligned}
$$

전개해서 생긴 세 항 중 데이터 $x_i$와 곱해지지 않는 $-\mu^2 / 2\sigma^2$은 exp 밖으로 꺼냈고, 데이터와 곱해지는 두 항은 벡터의 내적으로 묶었다. 이제 exp 안의 모수 벡터를 새 이름으로 부른다.

$$
\eta_1 = \frac{\mu}{\sigma^2}, \qquad \eta_2 = -\frac{1}{2\sigma^2}
$$

exp 앞의 분모도 $\mu$와 $\sigma$로만 이루어져 있으므로 $\eta_1, \eta_2$의 함수 $Z(\eta_1, \eta_2)$로 쓸 수 있다. 결과적으로 Gaussian은 "데이터로 만든 벡터 $[x, x^2]$과 모수 벡터의 내적을 exp에 넣고, 모수에만 의존하는 값으로 나눈 것"이 된다. 지수족은 이 구조를 일반화한 것이다.

## 일반형

지수족에 속하는 분포의 확률(또는 밀도)은 다음 꼴로 쓸 수 있다.

$$
p(x \mid \eta) = \frac{1}{Z(\eta)}\, h(x) \exp\left(\eta^\top t(x)\right) = h(x) \exp\left(\eta^\top t(x) - A(\eta)\right)
$$

두 번째 표현은 $Z(\eta) = e^{A(\eta)}$, 즉 $\frac{1}{Z(\eta)} = e^{-A(\eta)}$를 이용해 정규화 상수를 exp 안으로 넣은 것일 뿐 같은 식이다. 블로그는 이 일반형을 프로그래밍의 superclass에 비유한다. 어떤 분포든 이 식으로 정리할 수 있으면 지수족에 속한다. 블로그가 예로 든 것만 해도 Gaussian, Exponential, Gamma, Chi-squared, Beta, Dirichlet, Bernoulli, Categorical, Poisson, Wishart, Inverse Wishart, Geometric이 있다.

읽다가 "지수족은 likelihood를 계산하는 식인가"라는 생각이 들었는데, 그렇지는 않다. 지수족은 분포의 모임이다. 다만 이 꼴을 따르는 분포는 likelihood가 깔끔하게 정리되어 MLE를 구할 때 이점이 크다.

구성 요소를 하나씩 본다.

### $x$와 $\eta$

$x$는 대개 scalar지만 항상 그런 것은 아니다. 다변량 정규분포에서는 벡터다. 그래서 일반형에서는 $x$를 벡터로 두고, scalar는 길이 1인 벡터로 본다.

$\eta$는 natural parameter다. 우리가 평소에 쓰는 모수(Gaussian의 $\mu, \sigma^2$, Bernoulli의 성공 확률 $p$)가 그대로 $\eta$인 것은 아니고, 지수족 꼴에 맞게 변환해야 한다. 그래서 $\eta(\theta)$처럼 평소의 모수 $\theta$의 함수로 적기도 한다.

| 분포 | 평소의 모수 | natural parameter |
|---|---|---|
| Gaussian | $\mu, \sigma^2$ | $\eta_1 = \dfrac{\mu}{\sigma^2},\ \eta_2 = -\dfrac{1}{2\sigma^2}$ |
| Bernoulli | 성공 확률 $p$ | $\eta = \log\dfrac{p}{1-p}$ |

Bernoulli의 $\eta$는 logit이다. $\frac{p}{1-p}$를 odds라고 하고, 여기에 자연로그를 취한 것이 logit(log-odds)이다. logit은 0과 1 사이의 확률을 $(-\infty, \infty)$ 범위의 실수로 바꾼다. $p = 0.5$이면 0이고, $p$가 1에 가까워지면 양의 무한대로, 0에 가까워지면 음의 무한대로 간다.

### $t(x)$: 충분통계량

$t(x)$는 벡터 $x$를 받아 벡터를 내놓는 함수다. $\eta$와 내적해 숫자 하나를 만들고, 그 값은 양수일 수도 음수일 수도 있지만 exp를 거치므로 결과는 항상 양수다.

분포의 식에서 모수와 데이터가 만나는 곳은 $\eta^\top t(x)$뿐이다. 따라서 $x$가 바뀌어도 $t(x)$가 그대로면 모수가 확률에 주는 영향은 달라지지 않는다. 모수에 관해 데이터가 알려 주는 것은 $t(x)$에 다 들어 있다는 뜻이고, 그래서 충분통계량(sufficient statistic)이라고 부른다. Gaussian에서는 $t(x) = [x, x^2]$이다.

$h(x)$가 1이 아닌 경우에도 $t(x)$의 역할이 같은지 궁금했는데, 같다. $h(x)$에는 모수가 들어 있지 않으므로 모수와 데이터가 만나는 곳은 여전히 $\eta^\top t(x)$뿐이다.

### $h(x)$: base measure

$h(x)$는 $x$를 음수가 아닌 수로 보내는 함수이고 모수에 의존하지 않는다. 강의는 이것을 $x$의 intrinsic volume, 곧 모수와 상관없이 어떤 $x$가 원래 더 잘 나오는지를 나타내는 부피라고 설명한다. 많은 분포에서 $h(x) = 1$이고, 이때는 아무 효과가 없다.

처음에는 이 항이 왜 필요한지 알 수 없었다. 확률은 모수가 정하는 것인데, 모수와 무관하게 "더 잘 나오는 $x$"가 있다는 말이 이상했다. 아래 다항분포 예제를 보고 나서야 이해했으므로, 답은 그 절에 적는다.

### $Z(\eta)$: normalizer

$Z(\eta)$는 전체 확률이 1이 되게 하는 값이다. 가능한 모든 $x$에 대해 나머지 부분을 합한 것이다.

$$
Z(\eta) = \int h(x) \exp\left(\eta^\top t(x)\right) dx, \qquad A(\eta) = \log Z(\eta)
$$

$x$를 적분해 없애므로 $Z$는 $\eta$에만 의존한다. 강의는 $dx$ 자리에 measure라는 표기를 써서 "$x$의 영역에서 어떻게 합하는가"까지 분포를 정하는 선택의 하나로 둔다. 연속형이면 적분이고 이산형이면 단순한 합인데, 둘을 하나의 식으로 다루기 위한 표기다. 이 글에서는 $dx$로 적고, 이산형일 때는 합으로 읽는다.

이 적분이 항상 유한한 것은 아니다. $\eta$에 따라 $Z(\eta)$가 무한대가 되기도 하는데, 그런 $\eta$는 쓰지 않는다. $Z(\eta)$가 유한한 $\eta$만 모수로 인정한다.

BOCPD 논문(Adams & MacKay, 2007)에는 log-normalizer가 $A(\eta) = \log \int d\eta\, h(x) \exp(\eta^\top U(x))$로 적혀 있다. normalizer를 log로 바꾼 것이 $A$라는 점은 위와 같은데, 적분 변수가 $d\eta$로 적힌 것을 이해할 수 없어서 노트에 질문으로 남겨 두었다. 위의 정의를 놓고 보면 답은 단순하다. $A(\eta)$는 $\eta$의 함수여야 하므로 적분으로 없애는 변수는 $x$여야 하고, $\eta$에 대해 적분하면 $\eta$의 함수가 남지 않는다. 논문의 $d\eta$는 $dx$를 잘못 적은 표기로 읽는 것이 맞다.

## 익숙한 분포를 지수족으로 쓰기

블로그의 방법은 일정하다. 분포의 식 전체에 exp와 log를 함께 씌운 다음(둘은 서로 상쇄되므로 값은 그대로다), exp 안에서 $x$와 곱해진 항만 남기고 나머지는 밖으로 꺼낸다.

### Bernoulli

성공 확률이 $\mu$인 Bernoulli 분포에서 시작한다.

$$
\begin{aligned}
p(x \mid \mu) &= \mu^x (1-\mu)^{1-x} \\
&= \exp\left\{ x\log\mu + \log(1-\mu) - x\log(1-\mu) \right\} \\
&= (1-\mu) \exp\left\{ x \log\frac{\mu}{1-\mu} \right\}
\end{aligned}
$$

$x$와 곱해진 $\log\frac{\mu}{1-\mu}$가 natural parameter $\eta$다. 이 관계를 $\mu$에 대해 풀면 sigmoid 함수 $\sigma(\cdot)$가 나온다.

$$
\eta = \log\frac{\mu}{1-\mu} \ \Longrightarrow\ e^{\eta}(1-\mu) = \mu \ \Longrightarrow\ \mu = \frac{e^{\eta}}{1+e^{\eta}} = \frac{1}{1+e^{-\eta}} = \sigma(\eta)
$$

exp 밖에 남은 $1-\mu$도 $\eta$로 쓸 수 있다.

$$
1 - \mu = 1 - \frac{1}{1+e^{-\eta}} = \frac{1}{1+e^{\eta}} = \sigma(-\eta)
$$

정리하면 다음과 같다.

$$
p(x \mid \eta) = \sigma(-\eta) \exp(\eta x)
$$

$$
h(x) = 1, \qquad t(x) = x, \qquad Z(\eta) = 1 + e^{\eta}, \qquad A(\eta) = \log(1 + e^{\eta})
$$

강의는 같은 결과에 반대 방향으로 도달한다. 0과 1로 이루어진 i.i.d. 데이터를 지수족으로 모델링한다고 하고 구성 요소를 고른다.

1. $h(x)$: 모수와 무관하게 0이 1보다 더 잘 나오거나 덜 나올 이유가 없으므로 $h(x) = 1$.
2. $t(x)$: $x$는 0 아니면 1이므로 $x$ 자체면 충분하다. $t(x) = [x]$.
3. 합하는 영역: 가능한 값이 $\{0, 1\}$뿐이므로 두 경우를 더한다.

이렇게 고르면 normalizer는 선택의 여지 없이 정해진다.

$$
Z(\eta) = \sum_{x \in \{0, 1\}} \exp(x\eta) = 1 + e^{\eta}
$$

$$
p(1 \mid \eta) = \frac{e^{\eta}}{1 + e^{\eta}}, \qquad p(0 \mid \eta) = \frac{1}{1 + e^{\eta}}
$$

각 결과에 고정된 확률을 주는 분포, 곧 Bernoulli 분포다.

### Poisson

모수가 $\mu > 0$인 Poisson 분포도 같은 방법으로 바꾼다.

$$
p(x \mid \mu) = \frac{e^{-\mu}\mu^x}{x!} = \frac{1}{x!} \exp(-\mu) \exp(x \log\mu)
$$

$$
\eta = \log\mu, \qquad h(x) = \frac{1}{x!}, \qquad t(x) = x, \qquad Z(\eta) = \exp(e^{\eta}), \qquad A(\eta) = e^{\eta}
$$

$h(x)$가 1이 아닌 첫 예다.

### Gaussian

Gaussian은 이미 exp가 있으므로 제곱만 전개하면 된다.

$$
\begin{aligned}
p(x \mid \mu, \sigma^2) &= \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left\{ -\frac{1}{2\sigma^2}(x-\mu)^2 \right\} \\
&= \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left\{ -\frac{\mu^2}{2\sigma^2} \right\} \exp\left\{ \frac{\mu}{\sigma^2}x - \frac{1}{2\sigma^2}x^2 \right\}
\end{aligned}
$$

충분통계량과 natural parameter는 벡터가 된다.

$$
t(x) = \begin{bmatrix} x \\ x^2 \end{bmatrix}, \qquad \eta = \begin{bmatrix} \eta_1 \\ \eta_2 \end{bmatrix} = \begin{bmatrix} \dfrac{\mu}{\sigma^2} \\[2mm] -\dfrac{1}{2\sigma^2} \end{bmatrix}
$$

exp 밖의 항 중 모수와 무관한 $\frac{1}{\sqrt{2\pi}}$을 $h(x)$로 두면, 나머지 $\frac{1}{\sigma}\exp\{-\frac{\mu^2}{2\sigma^2}\}$이 $\frac{1}{Z(\eta)}$이다. 이것도 $\eta$로 쓸 수 있다.

$$
(-2\eta_2)^{1/2} = \sqrt{\frac{1}{\sigma^2}} = \frac{1}{\sigma}, \qquad \frac{\eta_1^2}{4\eta_2} = \frac{\mu^2/\sigma^4}{-2/\sigma^2} = -\frac{\mu^2}{2\sigma^2}
$$

$$
h(x) = (2\pi)^{-1/2}, \qquad \frac{1}{Z(\eta)} = (-2\eta_2)^{1/2} \exp\left( \frac{\eta_1^2}{4\eta_2} \right), \qquad A(\eta) = -\frac{\eta_1^2}{4\eta_2} - \frac{1}{2}\log(-2\eta_2)
$$

앞 절의 강의 방식에서는 $h(x) = 1$로 두고 $\sqrt{2\pi}$까지 $Z$에 넣었다. 모수와 무관한 상수는 $h$와 $Z$ 중 어느 쪽에 두어도 분포는 같으므로 두 방식 모두 맞다. $A(\eta)$가 상수 $\frac{1}{2}\log 2\pi$만큼 달라질 뿐이고, 뒤에서 쓰는 것은 $A$의 미분이라 결과에 차이가 없다.

### 다항분포와 $h(x)$의 의미

강의의 두 번째 예제는 $h(x)$가 왜 필요한지를 보여 준다. 데이터의 각 관측이 길이 3인 벡터 $x = [x_a, x_b, x_c]$이고, 성분은 음수가 아닌 정수이며 합이 항상 10이라고 하자. 예를 들면 $[6, 0, 4]$, $[3, 3, 4]$, $[5, 3, 2]$ 같은 값이다.

- 합하는 영역: 합이 10인 음이 아닌 정수 세 개의 조합 전체를 $\mathcal{X}$라 하고, 이 집합 위에서 더한다.
- $t(x) = [x_a, x_b]$: 열마다 모수를 하나씩 두는 것이 자연스럽지만, 합이 10이라는 제약 때문에 둘을 알면 나머지 하나는 정해진다. 그래서 길이는 2면 충분하다.
- $h(x)$: 여기가 핵심이다.

$x$가 횟수라고 생각하자. $a$, $b$, $c$ 중 하나가 나오는 사건이 10번 일어났고, $x$는 각각이 나온 횟수다. 수열 `aabbcababc`는 $[4, 4, 2]$가 된다. 이렇게 보면 모수와 무관하게 어떤 $x$는 다른 $x$보다 나오기 쉽다. $[10, 0, 0]$을 만드는 수열은 `aaaaaaaaaa` 하나뿐이지만, $[9, 1, 0]$을 만드는 수열은 `b`가 들어갈 자리에 따라 10개다. 그래서 $h(x)$를 "$x$를 만드는 수열의 개수"로 둔다.

$$
h(x) = \frac{10!}{x_a!\, x_b!\, x_c!}
$$

$[10, 0, 0]$을 넣으면 $\frac{10!}{10!} = 1$이고, $[9, 1, 0]$을 넣으면 $\frac{10!}{9!} = 10$이다. 이 선택들을 일반형에 넣으면 다음과 같다.

$$
p(x \mid \eta) = \frac{1}{Z(\eta_a, \eta_b)} \frac{10!}{x_a!\, x_b!\, x_c!} \exp(x_a\eta_a + x_b\eta_b), \qquad Z(\eta_a, \eta_b) = \sum_{x \in \mathcal{X}} \frac{10!}{x_a!\, x_b!\, x_c!} \exp(x_a\eta_a + x_b\eta_b)
$$

이것은 각 사건의 확률이 $p_a, p_b, p_c$인 다항분포(multinomial distribution)와 같다.

$$
p(x \mid \eta) = \frac{10!}{x_a!\, x_b!\, x_c!}\, p_a^{x_a} p_b^{x_b} p_c^{x_c}
$$

이 예제를 보며 적은 질문이 둘 있었다.

**확률 $p_a, p_b, p_c$가 왜 $\eta$로 정해지는가.** Bernoulli에서 $\eta$가 logit이었던 것과 같은 구조다. $x_c = 10 - x_a - x_b$를 넣어 다항분포의 식을 고쳐 쓰면

$$
p_a^{x_a} p_b^{x_b} p_c^{10 - x_a - x_b} = p_c^{10} \exp\left( x_a \log\frac{p_a}{p_c} + x_b \log\frac{p_b}{p_c} \right)
$$

이므로 $\eta_a = \log\frac{p_a}{p_c}$, $\eta_b = \log\frac{p_b}{p_c}$이고 $Z = p_c^{-10} = (1 + e^{\eta_a} + e^{\eta_b})^{10}$이다. $\eta$를 정하면 세 확률이 정해진다. Gaussian처럼 $\eta$가 확률값 자체가 아닌 경우에도, $\eta$가 바뀌면 평균과 분산이 바뀌고 그에 따라 각 $x$의 확률(밀도)이 바뀐다는 점은 같다.

**$h(x)$는 왜 모든 수열을 똑같이 세는가. 사건마다 확률이 다른데 이를 무시하는 것 아닌가.** 무시하는 것이 아니라 역할이 나뉘어 있다. 횟수가 $x$인 수열 하나가 나올 확률은 $p_a^{x_a} p_b^{x_b} p_c^{x_c}$이고, 이 값은 횟수가 같은 수열이면 순서와 상관없이 모두 같다. 이 부분이 모수에 의존하는 $\frac{1}{Z(\eta)}\exp(\eta^\top t(x))$에 해당한다. $h(x)$는 그 확률을 똑같이 갖는 수열이 몇 개인지를 센다. 확률은 모수에 따라 변하지만 경우의 수는 문제의 설정만으로 정해지므로, 모수와 무관한 항으로 따로 둘 수 있는 것이다.

이렇게 보면 $h(x) = 1$의 의미도 정리된다. 모수를 고려하지 않을 때 모든 $x$가 같은 부피를 가진다는 뜻이다. 1편에서 순서가 정해진 수열의 확률과 이항분포 사이에 조합 수 $\binom{n}{m}$만큼의 차이가 있었던 것도 같은 이야기다. 이항분포를 지수족으로 쓰면 그 조합 수가 $h$가 된다.

### 그 밖의 분포

$h$, $t$, 합하는 영역을 자유롭게 고를 수 있으므로 다른 분포도 같은 방식으로 얻는다. 강의에 나온 표를 옮긴다.

| 분포 | 합하는 영역 | $h(x)$ | $t(x)$ |
|---|---|---|---|
| Lognormal | $(0, \infty)$에서 적분 | $1/x$ | $[\log x,\ (\log x)^2]$ |
| Poisson | $\{0, 1, 2, \dots\}$에서 합 | $1/x!$ | $[x]$ |
| Exponential | $[0, \infty)$에서 적분 | $1$ | $[x]$ |
| Chi-squared | $[0, \infty)$에서 적분 | $\exp(-x/2)$ | $[\log x]$ |
| Beta | $(0, 1)$에서 적분 | $1$ | $[\log x,\ \log(1-x)]$ |
| 다변량 정규 | $\mathbb{R}^D$에서 적분 | $1$ | $[x,\ \mathrm{vec}(xx^\top)]$ |
| Gamma | $(0, \infty)$에서 적분 | $1$ | $[x,\ \log x]$ |

## Log-normalizer를 미분하면

지수족의 유용한 성질 가운데 첫 번째는 log-normalizer의 미분이 충분통계량의 평균과 분산을 준다는 것이다.

$$
\nabla_\eta A(\eta) = \mathbb{E}[t(x) \mid \eta], \qquad \nabla^2_\eta A(\eta) = \mathrm{Var}[t(x) \mid \eta]
$$

### Bernoulli로 확인

Bernoulli에서 $t(x) = x$이고 $A(\eta) = \log(1 + e^{\eta})$다. $\eta$를 움직이면서 $t(x)$의 평균과 분산이 어떻게 변하는지 본다.

"$\eta$는 확률인데 어떻게 $-4$에서 $4$까지 넓은 범위로 그릴 수 있는가"가 처음의 의문이었다. $\eta$는 확률이 아니라 logit $\log\frac{\mu}{1-\mu}$이므로 음의 무한대부터 양의 무한대까지 움직일 수 있다.

평균은 $\mathbb{E}[x] = \mu = \sigma(\eta)$다. $\eta = 0$이면 0과 1이 반반으로 나오므로 0.5이고, $\eta$가 커지면 1로, 작아지면 0으로 가는 S자 곡선이다. $A$를 미분해도 같은 것이 나온다.

$$
\frac{dA}{d\eta} = \frac{e^{\eta}}{1 + e^{\eta}} = \sigma(\eta)
$$

분산 곡선은 $\eta = 0$에서 0.25로 가장 높고 양쪽으로 갈수록 0에 가까워지는 종 모양인데, 노트에는 왜 이런 모양인지 모르겠다고 적어 두었다. Bernoulli의 분산은 $\mu(1-\mu)$다. 앞면과 뒷면이 반반일 때 결과가 가장 불확실하므로 $\mu = 0.5$에서 최대이고, $\eta$가 한쪽으로 치우치면 결과가 거의 정해지므로 0에 가까워진다. $A$를 두 번 미분한 결과도 이와 같다.

$$
\frac{d^2A}{d\eta^2} = \sigma(\eta)\left(1 - \sigma(\eta)\right) = \mu(1-\mu)
$$

강의는 두 분포를 더 보여 준다.

| 분포 | $\eta$ | $A(\eta)$ | $A'(\eta)$ = 평균 | $A''(\eta)$ = 분산 |
|---|---|---|---|---|
| Bernoulli | $\log\frac{\mu}{1-\mu}$ | $\log(1 + e^{\eta})$ | $\mu$ | $\mu(1-\mu)$ |
| Exponential (비율 $\lambda$) | $-\lambda$ | $-\log(-\eta)$ | $-1/\eta = 1/\lambda$ | $1/\eta^2 = 1/\lambda^2$ |
| Poisson (평균 $\mu$) | $\log\mu$ | $e^{\eta}$ | $e^{\eta} = \mu$ | $e^{\eta} = \mu$ |

### 증명

1차 미분은 chain rule과 $Z$의 정의에서 바로 나온다. $x$의 영역이 $\eta$에 의존하지 않으므로 미분을 적분 안으로 넣을 수 있다.

$$
\begin{aligned}
\nabla_\eta \log Z(\eta) &= \frac{1}{Z(\eta)} \nabla_\eta \int h(x) \exp\left(\eta^\top t(x)\right) dx \\
&= \int \underbrace{\frac{1}{Z(\eta)}\, h(x) \exp\left(\eta^\top t(x)\right)}_{p(x \mid \eta)}\, t(x)\, dx \\
&= \mathbb{E}[t(x) \mid \eta]
\end{aligned}
$$

2차 미분은 이 결과를 한 번 더 미분한다. $p(x \mid \eta) = h(x)\exp(\eta^\top t(x) - \log Z(\eta))$를 $\eta_j$로 미분하면 $p(x \mid \eta)\,(t_j(x) - \mathbb{E}[t_j])$가 되므로, 성분별로 쓰면

$$
\begin{aligned}
\frac{\partial^2 \log Z(\eta)}{\partial\eta_i\, \partial\eta_j} &= \frac{\partial}{\partial\eta_j} \int p(x \mid \eta)\, t_i(x)\, dx \\
&= \int p(x \mid \eta)\, t_i(x) \left( t_j(x) - \mathbb{E}[t_j] \right) dx \\
&= \mathbb{E}[t_i t_j] - \mathbb{E}[t_i]\,\mathbb{E}[t_j]
\end{aligned}
$$

이고, 이것은 $t(x)$의 공분산 행렬의 $(i, j)$ 성분이다.

### 의미

분포를 설명하는 대표적인 값인 평균과 분산을 얻고 싶으면 log-normalizer의 gradient와 Hessian을 구하면 된다. 적분으로 기댓값을 계산하는 대신 미분만 하면 된다는 뜻이다.

평균과 분산은 분포의 첫 두 누적량(cumulant)이다. 모멘트(moment)가 $\mathbb{E}[X] = \mu$, $\mathbb{E}[X^2] = \mu^2 + \sigma^2$처럼 거듭제곱의 기댓값이라면, 누적량은 $\kappa_1 = \mu$, $\kappa_2 = \sigma^2$처럼 분포의 성질을 직접 나타내는 값이다. log-normalizer를 더 높은 차수로 미분하면 더 높은 차수의 누적량이 나온다. 그래서 $A(\eta)$를 cumulant function이라고도 부른다.

## 최대가능도 추정

데이터 $\mathcal{D} = \{x_i\}_{i=1}^{N}$이 지수족 분포에서 독립으로 뽑혔다고 하자. 각 표본의 확률을 곱하면 exp 안의 내적은 합으로 모인다.

$$
p(\mathcal{D} \mid \eta) = \prod_{i=1}^{N} p(x_i \mid \eta) = \underbrace{\frac{1}{Z(\eta)^N}}_{1/Z_{\mathcal{D}}(\eta)}\ \underbrace{\left[ \prod_{i=1}^{N} h(x_i) \right]}_{h_{\mathcal{D}}(\mathcal{D})} \exp\Big( \eta^\top \underbrace{\sum_{i=1}^{N} t(x_i)}_{t_{\mathcal{D}}(\mathcal{D})} \Big)
$$

같은 것을 $N$번 곱하는 $Z(\eta)$는 거듭제곱이 되고, 표본마다 다른 $h(x_i)$는 곱으로 남는다. 데이터 전체의 분포도 다시 지수족 꼴이다. likelihood를 최대화하는 대신 negative log-likelihood를 최소화한다.

$$
-\log p(\mathcal{D} \mid \eta) = N A(\eta) - \log h_{\mathcal{D}}(\mathcal{D}) - \eta^\top t_{\mathcal{D}}(\mathcal{D})
$$

세 항의 성격이 모두 다르다. 강의는 둘째 항은 상수, 셋째 항은 선형, 첫째 항은 convex라고 짚고 넘어간다. 노트에는 셋째 항이 왜 선형이고 첫째 항이 왜 convex인지 모르겠다고 적어 두었는데, 앞 절의 결과를 쓰면 설명된다.

- $N A(\eta)$는 convex다. 앞 절에서 본 것처럼 $A$의 Hessian은 $t(x)$의 공분산 행렬이고, 공분산 행렬은 항상 positive semidefinite이기 때문이다.
- $\log h_{\mathcal{D}}(\mathcal{D})$는 $\eta$와 무관한 상수다.
- $\eta^\top t_{\mathcal{D}}(\mathcal{D})$는 $\eta$에 대해 선형이다. 데이터가 주어지면 $t_{\mathcal{D}}(\mathcal{D})$는 고정된 벡터이고, 고정된 벡터와의 내적은 선형 함수다.

convex 함수에 선형 함수를 더해도 convex이므로 negative log-likelihood 전체가 convex다. 따라서 gradient가 0인 지점은 local minimum이 아니라 global minimum이다. 다만 그런 지점이 하나라는 보장은 없다.

gradient를 0으로 두면 앞 절의 결과 $\nabla A(\eta) = \mathbb{E}[t(x) \mid \eta]$에 의해 다음 조건이 나온다.

$$
N\, \nabla_\eta A(\eta) - \sum_{i=1}^{N} t(x_i) = 0 \quad\Longleftrightarrow\quad \mathbb{E}[t(x) \mid \eta^*] = \frac{1}{N}\sum_{i=1}^{N} t(x_i)
$$

여기서 $t_{\mathcal{D}}(\mathcal{D})$가 왜 $t(x_i)$의 합인지 잠깐 헷갈렸는데, log를 취하기 전의 식에서 이미 exp 안이 $x_i$에 대한 합으로 묶여 있었기 때문이다.

이 조건이 말하는 바는 이렇다. **MLE는 모델이 예상하는 충분통계량의 평균이 데이터에서 관측한 충분통계량의 평균과 같아지는 $\eta$다.** 블로그는 같은 식을 $-\nabla\log g(\eta_{\mathrm{ML}}) = \frac{1}{n}\sum_i u(x_i)$로 적는다.

여기서 두 가지를 알 수 있다.

첫째, MLE를 구하는 데 필요한 것은 데이터 자체가 아니라 충분통계량의 합뿐이다. $t(x)$를 "충분"통계량이라고 부르는 이유가 여기서 다시 확인된다. 데이터를 모두 저장해 둘 필요 없이 합만 들고 있다가, 새 데이터가 올 때마다 더해 주면 추정을 순차적으로 갱신할 수 있다.

둘째, 분포를 지정하는 방법이 둘이라는 것이다. $\eta$를 정하면 분포가 정해지고 그에 따라 충분통계량의 기댓값도 정해진다. 거꾸로 충분통계량의 기댓값을 알면 그것으로도 분포를 지정할 수 있다. 이 기댓값을 mean parameter라고 하고, 이와 구분해 $\eta$를 canonical parameter라고 부른다. MLE 조건은 관측한 평균을 mean parameter로 삼고, 그에 대응하는 canonical parameter를 찾는 일이다.

Bernoulli에 적용하면 $\sigma(\eta^*) = \frac{m}{N}$, 즉 $\mu_{\mathrm{ML}} = \frac{m}{N}$으로 1편의 결과와 같다.

## 지수족의 어려움

강의는 지수족을 쓸 때 생기는 문제 세 가지도 짚는다.

**1. $Z(\eta)$를 계산할 수 없을 수 있다.** $h$, $t$를 어떻게 고르느냐에 따라 normalizer가 닫힌 형태로 구해지지 않는다. 이미지 위의 분포를 생각하면 normalizer는 가능한 모든 이미지에 대한 합인데, 이는 계산할 수 없다. normalizer를 모르면 확률값도 계산할 수 없다.

**2. 표현이 최소가 아닐 수 있다.** $t(x)$의 성분 사이에 선형 종속이 있으면, 예를 들어 $t(x) = (x, x^2, 2x^2)$처럼 한 성분이 다른 성분의 배수이면 minimal representation이 아니다. 이 경우 $\eta$에 불필요한 차원이 생겨서, 위의 MLE 조건을 만족하는 $\eta$가 하나가 아니라 부분공간 전체가 된다. 모수를 하나로 정할 수 없으므로(not identifiable) Hessian이 역행렬을 갖지 않고, 2차 미분을 쓰는 최적화 방법에서 문제가 된다. 베이즈 방식에서도 모수의 posterior를 정하기 어려워진다. mean parameter와 canonical parameter 사이의 일대일 대응은 표현이 minimal일 때만 성립한다.

**3. MLE가 존재하지 않을 수 있다.** 관측한 충분통계량의 평균이, 그 평균이 가질 수 있는 값의 영역 가장자리에 놓일 때 생긴다. 이때는 $\eta$를 최적화하면 양이나 음의 무한대로 발산한다. Bernoulli에서는 관측 평균이 1일 때, 곧 모든 관측이 1일 때다. $\sigma(\eta) = 1$을 만족하는 유한한 $\eta$는 없다. 1편에서 동전이 세 번 모두 앞면이었을 때 $\mu_{\mathrm{ML}} = 1$이 나온 상황이 바로 이것이다.

## 베이즈 추론과 conjugate prior

지금까지의 목표는 MLE로 $\eta$ 하나를 찾는 것이었다. 베이즈 방식은 가장 그럴듯한 $\eta$ 하나가 아니라 데이터가 주어졌을 때의 $\eta$의 분포 전체, posterior $p(\eta \mid \mathcal{D})$를 구한다. 그러려면 likelihood $p(\mathcal{D} \mid \eta)$와 prior $p(\eta)$가 필요하다.

Conjugacy는 posterior가 prior와 같은 꼴이 되도록 prior와 likelihood를 고르는 것이다([1편](01-conjugate-prior.md)). 이렇게 하지 않으면 posterior를 구할 때 너무 큰 공간에서 합을 계산해야 해서 다룰 수 없게 될 수 있다. 다행히 지수족과 베이즈 추론은 잘 맞는다. likelihood가 지수족이면 conjugate prior를 만드는 방법이 정해져 있다.

### 일반형

블로그는 conjugate prior를 다음과 같이 쓴다. $\chi$와 $\nu$는 prior의 hyperparameter이고 $f(\chi, \nu)$는 prior의 정규화 상수다.

$$
p(\eta \mid \chi, \nu) = f(\chi, \nu)\, g(\eta)^{\nu} \exp\left( \eta^\top \chi \right) = f(\chi, \nu) \exp\left( \eta^\top\chi - \nu A(\eta) \right)
$$

여기에 $n$개 관측의 likelihood를 곱하고 $\eta$와 무관한 항을 버린다.

$$
\begin{aligned}
p(\eta \mid X, \chi, \nu) &\propto \left[ \prod_{i=1}^{n} h(x_i) \right] g(\eta)^{n} \exp\Big( \eta^\top \sum_{i=1}^{n} t(x_i) \Big) \cdot f(\chi, \nu)\, g(\eta)^{\nu} \exp\left( \eta^\top\chi \right) \\
&\propto g(\eta)^{\nu + n} \exp\Big( \eta^\top \Big( \chi + \sum_{i=1}^{n} t(x_i) \Big) \Big)
\end{aligned}
$$

posterior는 prior와 같은 꼴이고 hyperparameter만 바뀌었다.

$$
\nu \leftarrow \nu + n, \qquad \chi \leftarrow \chi + \sum_{i=1}^{n} t(x_i)
$$

$\nu$는 관측의 개수를, $\chi$는 충분통계량의 합을 누적한다. 데이터가 하나씩 들어오는 순차 학습에서는 관측 $x$ 하나마다 $\nu \leftarrow \nu + 1$, $\chi \leftarrow \chi + t(x)$로 갱신하면 된다. 데이터를 다시 볼 필요가 없다.

Bernoulli에 적용하면 1편의 Beta prior가 나온다. $g(\eta) = 1 - \mu$이고 $e^{\eta} = \frac{\mu}{1-\mu}$이므로

$$
g(\eta)^{\nu} \exp(\eta\chi) = (1-\mu)^{\nu} \left( \frac{\mu}{1-\mu} \right)^{\chi} = \mu^{\chi}(1-\mu)^{\nu - \chi}
$$

로, $\mu$에 대해 Beta 분포의 꼴이다. $\eta$의 밀도를 $\mu$의 밀도로 바꿀 때 붙는 $\frac{d\eta}{d\mu} = \frac{1}{\mu(1-\mu)}$까지 넣으면 정확히 $\mathrm{Beta}(\mu \mid \chi,\ \nu - \chi)$다. 앞면 $m$번, 뒷면 $l$번을 관측하면 $\chi \leftarrow \chi + m$, $\nu \leftarrow \nu + m + l$이 되어 1편의 갱신 $a \leftarrow a + m$, $b \leftarrow b + l$과 일치한다.

### Prior도 지수족이다

강의는 같은 내용을 "prior도 지수족에서 고른다"는 관점으로 설명한다. likelihood와 prior를 구분하기 위해 아래첨자 $l$(likelihood)과 $p$(prior)를 붙인다. prior는 $\eta$에 대한 분포이므로 새 모수 벡터 $\tau$가 필요하다. $\tau$는 $\eta$의 분포를 정하는 hyperparameter이고, scalar $\tau_0$과 벡터 $\tau_1$으로 이루어진다.

$$
p(\eta \mid \tau) = \frac{h_p(\eta)}{Z_p(\tau)} \exp\left( \begin{bmatrix} -\log Z_l(\eta) & \eta^\top \end{bmatrix} \begin{bmatrix} \tau_0 \\ \tau_1 \end{bmatrix} \right)
$$

여기서 prior의 충분통계량은 고를 수 있는 것이 아니다. conjugate하려면 반드시 다음과 같아야 한다.

$$
t_p(\eta) = \begin{bmatrix} -\log Z_l(\eta) \\ \eta \end{bmatrix}
$$

likelihood의 log-normalizer가 prior의 충분통계량 안에 들어간다. 또 prior와 likelihood에서 $\eta$의 정의역이 같아야 한다. $\tau_0 = \nu$, $\tau_1 = \chi$, $h_p = 1$로 두면 블로그의 식과 같다.

posterior를 직접 계산해 본다. prior가 $\tau$에 의존하므로 posterior에도 $\tau$가 조건으로 붙는다. $N$개 관측의 likelihood는 앞 절의 $\frac{h_{\mathcal{D}}(\mathcal{D})}{Z_l(\eta)^N}\exp(\eta^\top t_{\mathcal{D}}(\mathcal{D}))$이다.

$$
\begin{aligned}
p(\eta \mid \mathcal{D}, \tau) &= \frac{p(\mathcal{D} \mid \eta)\, p(\eta \mid \tau)}{p(\mathcal{D} \mid \tau)} \\
&= \frac{1}{p(\mathcal{D} \mid \tau)} \cdot \frac{h_{\mathcal{D}}(\mathcal{D})}{Z_l(\eta)^N} \exp\left( \eta^\top t_{\mathcal{D}}(\mathcal{D}) \right) \cdot \frac{h_p(\eta)}{Z_p(\tau)} \exp\left( -\tau_0 \log Z_l(\eta) + \eta^\top\tau_1 \right) \\
&= \frac{1}{p(\mathcal{D} \mid \tau)} \frac{h_{\mathcal{D}}(\mathcal{D})}{Z_p(\tau)}\, h_p(\eta) \exp\left( -(\tau_0 + N)\log Z_l(\eta) + \eta^\top\left( \tau_1 + t_{\mathcal{D}}(\mathcal{D}) \right) \right)
\end{aligned}
$$

마지막 줄로 넘어갈 때 $\frac{1}{Z_l(\eta)^N} = e^{-N\log Z_l(\eta)}$를 써서 likelihood의 normalizer를 exp 안으로 넣었다. 손으로 따라 쓰면서 가장 눈에 들어온 부분이다. likelihood의 $\frac{1}{Z_l(\eta)}$이 prior의 충분통계량 $-\log Z_l(\eta)$와 같은 자리에 합쳐지도록 prior를 설계한 것이다.

$\eta$에 의존하는 부분 $h_p(\eta)\exp(\cdot)$은 prior와 꼴이 같고 $\tau$만 달라졌다. posterior는 정규화된 분포여야 하므로 앞의 상수는 새 $\tau$에 대한 prior의 정규화 상수와 같을 수밖에 없다.

$$
p(\eta \mid \mathcal{D}, \tau) = \frac{h_p(\eta)}{Z_p(\tau')} \exp\left( \begin{bmatrix} -\log Z_l(\eta) & \eta^\top \end{bmatrix} \tau' \right), \qquad \tau' = \begin{bmatrix} \tau_0 + N \\ \tau_1 + t_{\mathcal{D}}(\mathcal{D}) \end{bmatrix}
$$

데이터를 조건으로 주는 일은 prior의 $\tau$를 데이터에 의존하는 새 벡터로 바꾸는 것뿐이고, 그러기 위해 필요한 것은 충분통계량의 합 $t_{\mathcal{D}}(\mathcal{D})$뿐이다. 두 상수를 같다고 놓으면 evidence도 $p(\mathcal{D} \mid \tau) = h_{\mathcal{D}}(\mathcal{D})\, Z_p(\tau') / Z_p(\tau)$로 바로 얻는다.

강의의 화면에는 갱신이 $\tau_0 \to 1 + \tau_0$으로 적혀 있다. 강의는 데이터 전체를 관측 하나로 보고 $Z_l$을 데이터 전체의 normalizer($Z^N$)로 두었기 때문이다. 이 글에서는 블로그의 $\nu \leftarrow \nu + n$과 맞추어 관측 하나의 normalizer를 기준으로 적었다.

### Gaussian 예제

강의의 마지막 예제는 종 모양 히스토그램 데이터에 Gaussian likelihood를 쓰는 경우다. natural parameter가 $(\eta_1, \eta_2)$ 둘이므로 모수 공간은 2차원 평면이고, 평면 위의 점 하나가 Gaussian 하나에 대응한다. MLE는 이 평면에서 점 하나를 고른다. 베이즈 방식은 평면 위의 분포를 구하고, 거기서 $\eta$를 여러 개 뽑아 그럴듯한 Gaussian 여러 개를 함께 본다.

conjugate prior는 위의 규칙으로 정해진다. $h_l(x) = 1$로 둔 Gaussian에서 $\log Z_l(\eta) = -\frac{\eta_1^2}{4\eta_2} + \frac{1}{2}\log\left(-\frac{\pi}{\eta_2}\right)$이므로

$$
t_p(\eta) = \begin{bmatrix} \dfrac{\eta_1^2}{4\eta_2} - \dfrac{1}{2}\log\left( -\dfrac{\pi}{\eta_2} \right) \\[2mm] \eta_1 \\ \eta_2 \end{bmatrix}, \qquad h_p(\eta) = 1, \qquad \eta \in \mathbb{R} \times \mathbb{R}^{-}
$$

이다. $\eta_2 = -\frac{1}{2\sigma^2}$은 항상 음수이므로 정의역이 $\mathbb{R} \times \mathbb{R}^{-}$이다.

노트에는 "likelihood의 모수는 2개인데 왜 $\tau$는 길이가 3인가"라는 질문이 남아 있었다. 위 식이 답이다. prior의 충분통계량은 $-\log Z_l(\eta)$ 하나에 $\eta$의 성분 둘을 더한 길이 3의 벡터이고, $\tau$는 이것과 내적해야 하므로 길이가 3이다. 일반적으로 likelihood의 natural parameter가 $k$차원이면 conjugate prior의 hyperparameter는 $k + 1$차원이다. 늘어난 하나가 관측의 개수를 세는 $\tau_0$이다.

## MLE와 베이즈 추론의 차이

정리하다 보니 "MLE도 여러 $\eta$를 비교하고 베이즈도 여러 $\eta$를 고려하는데 무엇이 다른가"가 궁금해져서 따로 적어 두었다.

- **MLE**는 데이터를 가장 잘 설명하는 모수를 찾는다. $p(\mathcal{D} \mid \eta)$를 최대화하는 값 하나가 답이다.
- **베이즈 추론**은 모수를 확률변수로 보고, prior를 반영해 데이터를 본 뒤의 posterior를 구한다. 답은 값 하나가 아니라 모수의 분포다.

데이터가 충분히 많으면 likelihood가 매우 뾰족해져서 prior의 영향이 미미해지고, 두 방식의 결과가 비슷해진다. 데이터가 적으면 MLE는 과적합하기 쉽고 정확한 추정이 어렵다. 베이즈 방식은 prior로 이를 완화하고 불확실성을 분포로 남긴다. 다만 베이즈 방식은 계산량이 많아서 실제 머신러닝에서는 MLE나 MAP가 더 많이 쓰인다.

이전의 posterior를 다음의 prior로 써도 되는 이유도 같은 맥락에서 이해했다. posterior는 지금까지의 데이터를 반영한 모수의 분포다. 데이터가 적을 때 구해 둔 posterior는 데이터가 더 들어오는 시점에서 보면 "이미 알고 있는 것"이므로 그때의 prior로 쓸 수 있고, 데이터가 쌓일수록 분포는 더 정교해진다.

## 정리

- 지수족은 $p(x \mid \eta) = h(x)\exp(\eta^\top t(x) - A(\eta))$ 꼴로 쓸 수 있는 분포의 모임이다. $t(x)$는 모수와 데이터가 만나는 유일한 통로이고, $h(x)$는 모수와 무관하게 $x$가 가지는 부피(예: 경우의 수)다.
- $A(\eta)$의 gradient는 $t(x)$의 평균, Hessian은 $t(x)$의 공분산이다. 그래서 $A$는 convex다.
- MLE는 "모델이 예상하는 $t(x)$의 평균 = 데이터에서 관측한 $t(x)$의 평균"이라는 조건으로 정해지고, 필요한 것은 충분통계량의 합뿐이다.
- conjugate prior는 $\exp(\eta^\top\chi - \nu A(\eta))$ 꼴이고, 데이터를 보면 $\nu$에 관측 수를, $\chi$에 충분통계량의 합을 더하면 된다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. convex 함수에서 gradient가 0인 점은 왜 global minimum인가? Hessian이 positive semidefinite라는 것과는 어떻게 이어지는가?**

Convex 함수의 정의는 두 점을 이은 선분이 그래프 위쪽에 있다는 것이다. 모든 $x, y$와 $t \in [0, 1]$에 대해

$$
f\big(x + t(y - x)\big) \le (1 - t)\, f(x) + t\, f(y)
$$

이다. 양변에서 $f(x)$를 빼고 $t$로 나누면 $\frac{f(x + t(y - x)) - f(x)}{t} \le f(y) - f(x)$이고, $t \to 0$으로 보내면 좌변은 $y - x$ 방향의 방향 미분 $\nabla f(x)^{\top}(y - x)$가 된다. 따라서 미분 가능한 convex 함수는 어느 점에서 그은 접평면보다 항상 위에 있다.

$$
f(y) \ge f(x) + \nabla f(x)^{\top}(y - x) \qquad \text{(모든 } x, y\text{)}
$$

여기에 $\nabla f(x^*) = 0$인 점을 넣으면 모든 $y$에 대해 $f(y) \ge f(x^*)$다. 주변에서만 가장 낮은 것이 아니라 전체에서 가장 낮다. 접평면이 수평인데 함수가 그 아래로 내려갈 수 없기 때문이다.

Hessian과는 Taylor 전개로 이어진다. 두 번 미분 가능하면 $x$와 $y$ 사이의 어떤 점 $\xi$에 대해 $f(y) = f(x) + \nabla f(x)^{\top}(y - x) + \frac{1}{2}(y - x)^{\top}\nabla^2 f(\xi)(y - x)$이다. Hessian이 어디서나 positive semidefinite이면 마지막 항이 0 이상이므로 위의 접평면 부등식이 성립하고, 이 부등식이 모든 점에서 성립하는 것은 convex인 것과 같다. 지수족의 negative log-likelihood는 Hessian이 $N$배 한 공분산 행렬이어서 이 조건을 만족한다.

두 가지를 덧붙인다. Hessian이 positive definite이면 함수가 strictly convex여서 최솟값을 주는 점이 많아야 하나다. 지수족에서는 표현이 minimal일 때가 여기에 해당하고, 본문의 "표현이 최소가 아닐 수 있다"는 이 조건이 깨져 최솟값을 주는 점이 여럿이 되는 경우다. 그리고 convex라는 것이 gradient가 0인 점의 존재까지 보장하지는 않는다. $e^{-x}$처럼 계속 내려가기만 하는 convex 함수도 있고, 본문의 "MLE가 존재하지 않을 수 있다"가 바로 이 경우다.

## 참고 자료

- Mutual Information, "The Exponential Family (Part 1)", YouTube. <https://www.youtube.com/watch?v=xwM9XcnQ4Us&list=PLzvYlJMoZ02BCWFTETEiupwhDBpP4gw-q>
- Mutual Information, "The Exponential Family (Part 2)", YouTube. <https://www.youtube.com/watch?v=9EgkOn5mWcc&list=PLzvYlJMoZ02BCWFTETEiupwhDBpP4gw-q&index=2>
- Gregory Gundersen, "The Exponential Family", 2019. <https://gregorygundersen.com/blog/2019/03/19/exponential-family/>
- R. P. Adams, D. J. C. MacKay, "Bayesian Online Changepoint Detection", 2007. <https://arxiv.org/abs/0710.3742>
