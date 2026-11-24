---
title: "강화학습 3: Policy Gradient와 REINFORCE"
date: 2025-03-12
last_modified_at: 2025-03-13
category: study
tags: [강화학습, Policy Gradient, REINFORCE, Monte Carlo, Stochastic Policy]
---

# 강화학습 3: Policy Gradient와 REINFORCE

작성일 2025-03-12 · 최종 수정일 2025-03-13
{ .post-date }

[DQN](02-dqn.md)까지는 Q값을 잘 구한 뒤 그 값이 가장 큰 action을 고르는 Value-based 방법이었다. 이 글에서는 policy 자체를 신경망으로 두고 직접 학습하는 Policy-based 방법을 정리한다. objective function의 gradient를 표본 평균으로 구할 수 있는 형태로 바꾸는 유도 과정이 중심이고, 그 결과가 REINFORCE 알고리즘이다.

## DQN과 무엇이 다른가

- **Value-based (DQN)**: objective function을 두고 Value Function을 잘 근사한 뒤, 그 값이 가장 큰 action을 고른다. policy는 명시적으로 존재하지 않는다. 학습된 Q값을 보고 greedy하게 고를 뿐이다.
- **Policy-based**: 특정한 값(Q)을 찾는 것이 목표가 아니다. policy $p(a_t \mid s_t)$ 자체를 찾는다. 앞에서는 policy를 $\epsilon$-greedy나 greedy로 고정했지만, 이제는 state마다 action을 고르는 분포를 직접 찾는다.

분포를 찾으려면 분포의 형태는 미리 정해야 한다. 예를 들어 Gaussian policy라면, 신경망에 $s_t$를 넣었을 때 그 state에서의 policy를 이루는 parameter인 평균 $\mu$와 표준편차 $\sigma$가 나온다. 그리고 그 분포에서 action 하나를 뽑는다. 분포를 예측하고 거기서 action을 sampling한다는 것이 Policy-based 방법의 큰 특징이다.

## 왜 Policy-based인가

### 1. Value-based는 continuous한 action을 다루기 어렵다

DQN은 action마다 Q값을 하나씩 출력하므로 action이 연속적이면 쓸 수 없다. 반면 policy가 Gaussian이라면 평균과 분산만 알면 연속적인 action을 얼마든지 뽑을 수 있다.

Q를 $a_t$까지 입력으로 받는 함수로 근사한다 해도 문제가 남는다. 어떤 state $s_t$에서 $a_t$에 따른 Q값이 봉우리가 여러 개인 곡선이라고 하자. 여기서 Q가 최대인 $a_t$를 찾는 것부터가 하나의 최적화 문제가 된다. gradient 기반으로 찾으려 하면, Q가 이렇게 굴곡진 함수일 때 local maximum에 갇힐 가능성이 크다. 즉 action space가 연속적이라는 이유만으로 "max Q 찾기"가 따로 풀어야 할 문제가 된다.

### 2. Stochastic Policy를 찾을 수 있다

$\epsilon$-greedy는 완전히 stochastic하다고 말하기 어렵다. $\epsilon$이 있더라도 본질은 greedy action이기 때문이다. policy의 분포를 찾고 거기서 action을 뽑아야 진짜 stochastic한 것이다.

다음과 같은 격자 환경을 생각해 보자. 윗줄의 다섯 칸이 state이고, 아랫줄에는 함정(Die)과 목표(Goal)가 있다.

| $s_0$ | $s_1$ | $s_2$ | $s_3$ | $s_4$ |
|:---:|:---:|:---:|:---:|:---:|
| Die | (벽) | Goal | (벽) | Die |

- agent가 관측하는 state는 상하좌우에 벽이 있는지 여부뿐이라고 하자. 시작 위치는 $s_0 \sim s_4$ 중 하나다.
- 그러면 $s_1$과 $s_3$은 위아래가 막혀 있고 좌우가 열려 있어 서로 구분되지 않는 같은 state가 된다. 이런 상황을 partially observable이라고 한다.
- 만약 $Q(s_1, \text{left}) > Q(s_1, \text{right})$로 학습되었다면, $s_1$에서는 반드시 $s_0$으로 간다. 학습이 끝나 exploitation만 한다면 $s_0$에서는 다시 $s_1$로 돌아오고, 이 둘 사이를 계속 오간다. $s_3$에서는 왼쪽이 정답이므로, $s_3$에 맞춰진 결과라고 볼 수 있다.
- 반대로 $Q(s_1, \text{left}) < Q(s_1, \text{right})$라면 이번에는 $s_3 \rightarrow s_4 \rightarrow s_3 \rightarrow \cdots$가 반복된다.

두 state를 구분할 수 없는 이상, 결정적인 policy로는 어느 한쪽에서 시작한 경우 절대로 goal에 도달하지 못한다. 이 상황에서는 $p(\text{left} \mid s_1) = p(\text{right} \mid s_1) = 0.5$가 되어야 한다. 그래서 Policy-based 방법은 로봇 학습에서 주로 쓰인다고 한다.

## Objective Function

Value-based와 Policy-based 모두 목표는 expected return을 최대화하는 것이다. Policy-based는 이를 직접 objective로 삼는다. 처음부터 얻는 return $G_0 = R_0 + \gamma R_1 + \gamma^2 R_2 + \cdots$의 기댓값을 최대화한다.

$$
J \triangleq \mathbb{E}[G_0] = \int_\tau G_0 \, p(\tau) \, d\tau
$$

$\tau$는 trajectory $s_0, a_0, s_1, a_1, \cdots$이고, 그 확률은 다음과 같다.

$$
p(\tau) = p(s_0) \prod_{t=0}^{\infty} p(a_t \mid s_t) \, p(s_{t+1} \mid s_t, a_t)
$$

DQN에서는 Q를 알아내기 위해 Q를 parameterize했다. Policy-based에서는 policy를 parameterize하고, 이를 $p_\theta(a_t \mid s_t)$로 적는다. network $\theta$는 하나이고, 입력 state가 달라지면 출력되는 policy가 달라진다.

$$
J_\theta = \int_\tau G_0 \, p_\theta(\tau) \, d\tau
$$

목표가 최대화이므로 gradient ascent를 쓴다.

$$
\theta \leftarrow \theta + \alpha \cdot \nabla_\theta J_\theta
$$

## Gradient를 표본 평균으로 바꾸기

문제는 $\nabla_\theta J_\theta$를 계산하기 어렵다는 것이다. 모든 trajectory에 대한 적분이고, trajectory는 너무 길다.

$$
\nabla_\theta J_\theta = \int_\tau G_0 \, \nabla_\theta p_\theta(\tau) \, d\tau
$$

미분하고 나니 적분 안이 "값 × 확률"이 아니라 "값 × 확률의 미분"이 되었다. 확률이 곱해진 형태여야 기댓값으로 읽을 수 있고, 기댓값이어야 표본 평균으로 근사할 수 있다. 그래서 "확률의 미분"을 다시 "확률 × 무언가"로 바꾸는 trick을 쓴다.

$$
\nabla_\theta p_\theta(\tau) = p_\theta(\tau) \cdot \nabla_\theta \ln p_\theta(\tau)
$$

$\ln$의 미분 $\nabla_\theta \ln p_\theta = \nabla_\theta p_\theta / p_\theta$를 뒤집어 쓴 것이다. 이를 대입하면 다음과 같다.

$$
\nabla_\theta J_\theta = \int_\tau G_0 \, \big( \nabla_\theta \ln p_\theta(\tau) \big) \, p_\theta(\tau) \, d\tau
$$

이제 적분은 $p_\theta(\tau)$에 대한 기댓값이다. trajectory를 여러 개 뽑아 $G_0 \nabla_\theta \ln p_\theta(\tau)$를 계산하고 평균 내면 gradient를 근사할 수 있다. 곧 Monte Carlo다. 실제로 적분을 계산하기는 어려우므로, 기댓값의 형태로 만들어 표본 평균으로 대신하는 것이다.

### 중간 요약

1. objective는 $G_0$의 기댓값이다: $J_\theta = \int_\tau G_0 \, p_\theta(\tau) \, d\tau$
2. gradient ascent로 $\theta$를 갱신한다: $\theta \leftarrow \theta + \alpha \cdot \nabla_\theta J_\theta$
3. 적분을 직접 계산할 수 없으므로 $\ln$ trick으로 기댓값 문제로 바꾸고, 표본 평균으로 근사한다.

## $\ln p_\theta(\tau)$ 전개

조건부 확률의 곱셈 법칙으로 $p_\theta(\tau)$를 풀어 보자.

$$
\begin{aligned}
p_\theta(\tau) &= p(s_0) \, p(a_0, s_1, a_1, s_2, \cdots \mid s_0) \\
&= p(s_0) \, p_\theta(a_0 \mid s_0) \, p(s_1, a_1, s_2, \cdots \mid s_0, a_0) \\
&\;\;\vdots \\
&= p(s_0) \, p_\theta(a_0 \mid s_0) \, p(s_1 \mid s_0, a_0) \, p_\theta(a_1 \mid s_0, a_0, s_1) \, p(s_2 \mid s_0, a_0, s_1, a_1) \cdots \\
&= p(s_0) \, p_\theta(a_0 \mid s_0) \, p(s_1 \mid s_0, a_0) \, p_\theta(a_1 \mid s_1) \, p(s_2 \mid s_1, a_1) \cdots
\end{aligned}
$$

마지막 줄에서는 Markov Property로 조건을 줄였다. 결국 $p_\theta(\tau)$는 초기 state의 확률, policy, transition probability, policy, transition probability, …의 곱이다.

중요한 점이 두 가지 있다.

- $\ln$을 씌우면 이 곱셈이 모두 덧셈으로 바뀐다.
- 전체를 $\theta$로 미분하므로, $\theta$와 관련 없는 항(초기 state의 확률, transition probability)은 모두 0이 된다. policy만 남는다.

$$
\nabla_\theta \ln p_\theta(\tau) = \nabla_\theta \sum_{t=0}^{\infty} \ln p_\theta(a_t \mid s_t)
$$

환경의 transition probability를 몰라도 gradient를 구할 수 있다는 뜻이기도 하다. 이를 대입하고 $G_0$를 풀어 쓰면 다음과 같다.

$$
\nabla_\theta J_\theta = \int_\tau \big( R_0 + \gamma R_1 + \gamma^2 R_2 + \cdots \big) \Big( \nabla_\theta \ln p_\theta(a_0 \mid s_0) + \nabla_\theta \ln p_\theta(a_1 \mid s_1) + \nabla_\theta \ln p_\theta(a_2 \mid s_2) + \cdots \Big) \, p_\theta(\tau) \, d\tau
$$

### 과거의 reward는 사라진다

여기서 또 지워지는 항이 있다. $R_0$와 $\nabla_\theta \ln p_\theta(a_1 \mid s_1)$의 곱만 떼어 보자. $R_0$는 $a_1$을 고르기 전에 이미 정해진 값이다. 그래서 $a_1$에 대해 먼저 적분하면 다음이 남는다.

$$
\int \nabla_\theta \ln p_\theta(a_1 \mid s_1) \, p_\theta(a_1 \mid s_1) \, da_1 = \int \nabla_\theta p_\theta(a_1 \mid s_1) \, da_1 = \nabla_\theta \int p_\theta(a_1 \mid s_1) \, da_1 = \nabla_\theta 1 = 0
$$

같은 이유로, 시점 $t$의 $\nabla_\theta \ln p_\theta(a_t \mid s_t)$에는 그보다 앞선 reward가 곱해진 항이 모두 사라지고 $t$ 이후의 reward만 남는다. 남는 것은 $\gamma^t R_t + \gamma^{t+1} R_{t+1} + \cdots = \gamma^t G_t$다. (노트에서는 이 부분을 "나중에 정리"로 남겨 두고 결과만 적었다. 위의 한 줄은 글을 옮기며 채운 것이다.)

$$
\nabla_\theta J_\theta = \int_\tau \sum_{t=0}^{\infty} \Big( \nabla_\theta \ln p_\theta(a_t \mid s_t) \cdot \gamma^t G_t \Big) \, p_\theta(\tau) \, d\tau
$$

### $\gamma^t$ 제거

위 식에서 $t \rightarrow \infty$이면 $\gamma^t \rightarrow 0$이다. 그러면 episode의 뒷부분은 return이 거의 무시된다. 뒷부분의 return도 반영하기 위해 $\gamma^t$를 떼어 내고 근사한다.

$$
\nabla_\theta J_\theta \approx \int_\tau \sum_{t=0}^{\infty} \Big( \nabla_\theta \ln p_\theta(a_t \mid s_t) \cdot G_t \Big) \, p_\theta(\tau) \, d\tau
$$

이 식으로 만든 알고리즘이 REINFORCE다.

## REINFORCE

위 식에서 표본 하나는 다음 값이다.

$$
\sum_{t=0}^{\infty} \nabla_\theta \ln p_\theta(a_t \mid s_t) \cdot G_t
$$

표본 하나를 얻으려면 episode 하나가 통째로 필요하다. 표본 평균을 내려면 episode를 여러 번 돌아야 하는데, 그러면 갱신이 너무 드물어진다. 그래서 $N = 1$로 두고, episode 하나에서 얻은 표본 하나를 그대로 표본 평균으로 보고 갱신한다.

### 알고리즘

1. **Initialize $\theta$**: policy network를 초기화한다. policy를 어떤 분포로 초기화한다는 뜻이기도 하다.
2. **Get $\tau$ from $p_\theta(a_t \mid s_t)$**: episode를 진행하며 현재의 policy로 trajectory를 얻는다.
3. **Update**: 얻은 표본으로 gradient ascent를 한다.

$$
\theta \leftarrow \theta + \alpha \sum_{t=0}^{\infty} \nabla_\theta \ln p_\theta(a_t \mid s_t) \cdot G_t
$$

2와 3을 반복한다.

## 특징: Unbiased, High Variance

같은 식을 기댓값 기호로 적으면 다음과 같다. $d^\pi$는 policy를 따를 때 state가 나타나는 분포다.

$$
\nabla_\theta J(\theta) = \mathbb{E}_{s \sim d^\pi,\, a \sim \pi_\theta} \big[ \nabla_\theta \log \pi_\theta(a \mid s) \, G_t \big]
$$

- $\log \pi_\theta(a \mid s)$: 현재 policy가 그 action을 고를 확률(의 로그)
- $G_t$: 그 action 이후 실제로 받은 return

**왜 unbiased인가**

Policy Gradient는 기댓값을 직접 sampling해서 추정한다. episode 전체에서 실제로 받은 return을 쓰므로, 표본이 충분하면 Monte Carlo의 성질에 따라 정확한 gradient로 수렴한다.

**왜 variance가 큰가**

1. return $G_t$의 변동이 크다. 초기의 policy는 불완전해서 같은 state에서도 다양한 action이 뽑히고, action이 달라지면 return도 크게 달라진다.
2. gradient의 noise가 크다. 식에 $G_t$가 곱해져 있으므로 $G_t$가 크면 gradient도 크게 변한다. 어떤 episode에서는 운이 좋아 $G_t$가 크고, 어떤 episode에서는 매우 작다. 갱신 방향이 계속 흔들린다.
3. on-policy라서 데이터를 재사용할 수 없다. 표본이 많지 않으면 gradient 추정의 variance가 클 수밖에 없다.

**왜 on-policy인가**

REINFORCE는 objective function에 gradient ascent를 적용해 policy를 갱신한다. 그 gradient는 현재의 policy $p_\theta$에 대한 기댓값이므로, 현재의 policy로 수집한 데이터만 쓸 수 있다. 현재 쓰고 있는 policy에서 모은 데이터로만 갱신하는 방식이 on-policy이므로, REINFORCE는 on-policy 알고리즘이다.

이런 이유로 제대로 된 $\theta$를 얻으려면 갱신을 굉장히 많이 해야 한다. 이 variance를 줄이려는 시도가 [다음 글](04-actor-critic.md)의 Actor-Critic과 A2C다.

## 정리

- Policy-based 방법은 policy를 신경망 $p_\theta(a_t \mid s_t)$로 두고 expected return $J_\theta = \mathbb{E}[G_0]$를 gradient ascent로 최대화한다.
- $\nabla_\theta p_\theta = p_\theta \nabla_\theta \ln p_\theta$로 gradient를 기댓값 형태로 바꾸면 표본 평균으로 근사할 수 있다.
- $\ln p_\theta(\tau)$를 전개하면 policy 항만 남고, 각 시점의 항에는 그 시점 이후의 return만 곱해진다.
- REINFORCE는 episode 하나를 표본 하나로 써서 갱신한다. unbiased이지만 variance가 크고, on-policy다.

## 참고 자료

- R. J. Williams, "Simple Statistical Gradient-Following Algorithms for Connectionist Reinforcement Learning", Machine Learning 8, 1992.
- R. S. Sutton, A. G. Barto, "Reinforcement Learning: An Introduction", 2nd ed., MIT Press, 2018. 13장 Policy Gradient Methods. <http://incompleteideas.net/book/the-book-2nd.html>
