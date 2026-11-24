---
title: "강화학습 2: DQN"
date: 2025-03-13
last_modified_at: 2025-03-13
category: study
tags: [강화학습, DQN, Experience Replay, Target Network, Q-Learning]
---

# 강화학습 2: DQN

[앞 글](01-q-learning.md)의 Q-Learning은 state와 action의 쌍마다 Q값을 따로 저장하고 갱신했다. 이 글에서는 Q를 신경망으로 근사하는 DQN(Deep Q-Network)을 정리한다. 2013년에 발표된 알고리즘을 한 줄씩 따라가고, 2015년에 추가된 Target Network까지 본다.

## 왜 DQN인가

DQN은 state를 convolution network에 넣어, action별 Q값을 regression으로 구한다. 이렇게 하는 이유는 state가 너무 많기 때문이다.

Atari Breakout을 예로 들면, 화면 하나하나가 state이므로 agent가 만나는 state의 대부분은 처음 겪는 state다. 모든 state에 대해 Q값을 따로 찾아 표에 적는 것은 현실적으로 어렵다. 그래서 Q를 함수로 근사한다. 모든 state를 겪어 보지는 못하더라도, 처음 보는 state에 대해서도 그럴듯한 Q값을 내놓게 하자는 아이디어다.

기존의 Q-Learning은 Q값을 직접 갱신했다. DQN은 Q값을 내놓는 network의 weight를 갱신한다.

## DQN (2013)

### Objective Function

$$
L_i(\theta_i) = \mathbb{E}_{s, a \sim p(\cdot)} \Big[ \big( y_i - Q(s, a; \theta_i) \big)^2 \Big]
$$

목표는 이 objective function을 최소화하는 것이다. Q-network가 내놓는 현재의 Q값이 TD Target에 가까워지도록 최적화한다.

- $s, a \sim p(\cdot)$: 수식적으로 특별한 의미가 있는 것은 아니다. state $s$와 action $a$가 Replay Buffer에서 transition으로 뽑혀 온다는 뜻으로, 그렇게 뽑힌 표본들이 이루는 분포를 $p(\cdot)$로 적은 것이다.
- $y_i$: TD Target이다. next state $s'$에서 Q가 가장 큰 action을 골랐을 때 기대되는 return, 곧 $r + \gamma \max_{a'} Q(s', a')$이다.

!!! question "Q. 현재의 Q값이 TD Target을 따라가도록 학습시키는 것이 논리적으로 말이 되는 이유는?"

    Bellman 방정식에 따르면, target policy를 계속 greedy selection으로 두는 것은 optimal Q에 점점 가까워지는 과정이라고 볼 수 있다. 즉 TD Target이 optimal에 가까울 것이라는 가정 아래, 현재 state의 Q값이 그 값을 따라가도록 학습시키는 것이다.

    TD Target에 들어가는 Q값은 결국 다음 state의 Q값 중 가장 큰 값이다. "이 길로 가면 앞으로 얼마나 좋을지"를 대략 확인한 값이므로, 이를 현재의 Q값에 반영해 학습해도 된다는 뜻으로 해석했다.

objective function을 미분하면 다음과 같다.

$$
\nabla_{\theta_i} L_i(\theta_i) = -2 \, \mathbb{E}_{s, a \sim p(\cdot)} \Big[ \big( y_i - Q(s, a; \theta_i) \big) \cdot \nabla_{\theta_i} Q(s, a; \theta_i) \Big]
$$

$$
\theta_{i+1} = \theta_i - \alpha \cdot \nabla_{\theta_i} L_i(\theta_i)
$$

TD Error $y_i - Q(s, a; \theta_i)$가 클수록 weight가 많이 바뀐다는 뜻이다. 이 gradient로 gradient descent를 한다.

표본 하나에 대해서는 다음 값을 계산하면 된다.

$$
\Big( R_t + \gamma \max_{a_{t+1}} Q(s_{t+1}, a_{t+1}) - Q_w(s_t, a_t) \Big)^2
$$

여기서 TD Target은 상수로 취급한다. TD Target 안에도 Q가 들어 있지만 그쪽으로는 미분하지 않는다는 뜻이다. 현재의 Q값은 Q-network가 내놓은 값이므로 weight를 뜻하는 아래 첨자 $w$를 붙였다.

### 구조

DQN은 84 × 84 크기의 흑백 화면 4장을 쌓아 입력으로 받고, CNN으로 Q값을 근사한다.

본래 Q는 $s_t$와 $a_t$를 모두 입력으로 받는 함수이고, regression은 이 함수 전체를 근사하는 일이다. 그런데 DQN은 CNN에 $(s_t, a_t)$를 함께 넣지 않고 $s_t$만 넣는다. 그리고 CNN 뒤의 fully connected layer가 action의 개수만큼 Q값을 한꺼번에 내놓게 한다. Breakout이라면 $Q_w(s_t, \text{left})$와 $Q_w(s_t, \text{right})$가 동시에 나온다.

action까지 입력에 넣으면 입력이 커지고, 모든 action의 Q값을 알아내려면 action의 개수만큼 추론을 반복해야 한다. 연산량이 너무 많아진다. 그래서 입력은 화면 4장, 출력은 action별 Q값으로 정한 것이다.

### 무엇이 새로웠는가

1. **CNN으로 Q를 근사한다.** 사람이 화면을 보고 판단하듯 화면에서 직접 Q값을 근사한다. 갱신되는 것은 Q값이 아니라 Q를 근사하는 network의 weight다.
2. **Experience Replay를 쓴다.** Q-Learning은 off-policy이므로 과거의 경험을 다시 쓸 수 있다. TD Target을 만들 때 그 경험을 모았던 behavior policy를 쓰지 않고 greedy로 다시 고르기 때문이다. 과거에 했던 경험을 지금의 Q로 다시 평가해 보는 것이라고 해석할 수도 있다.

## State Correlation

Experience Replay가 왜 필요한지는 state 사이의 상관관계로 설명된다.

Breakout에서 공이 바닥의 막대에 튕겨 벽돌에 맞는 장면을 생각해 보자. 공이 막대에 닿는 순간과 벽돌에 닿는 순간, 이 두 화면만으로 평가할 수는 없다. 연속된 화면을 봐야 공이 어디로 향하는지 알 수 있다.

그렇다고 벽돌에 맞기 직전의 연속된 16 프레임만 보면, 공이 어떤 각도로 들어와 어디서 튕겼고 어떤 각도로 나갔는지는 평가에 들어가지 않는다. 처음부터 끝까지 모든 프레임을 보자니 프레임이 너무 많다.

결정적인 문제는 연속된 state들이 서로 너무 비슷하다는 점이다. 상관관계가 높으면 SGD로 학습시키기 어렵다. SGD는 mini-batch의 표본들이 서로 독립일 때 잘 동작한다. 가로축을 state, 세로축을 Q값으로 놓고 생각하면, 한쪽에 몰려 있는 몇 개의 점만 보고 함수를 맞추면 전체 곡선이 아니라 그 근처에서만 맞는 직선을 얻는다. 전체에 대한 근사가 아니라 국소적인 근사가 되는 것이다.

그래서 전체 프레임 중 일부를 여기저기서 골라 평가한다. 이렇게 하면 상관관계도 줄고, 전체를 고르게 볼 수 있다.

## Replay Buffer

여러 시점의 state를 고르게 뽑기 위해, 겪은 transition을 저장해 두는 Replay Buffer를 둔다.

- Q-network를 갱신할 때는 저장된 transition 중에서 균등한 확률로 무작위로 골라 쓴다.
- Replay Buffer의 용량은 정해져 있다.

## 알고리즘 (2013)

논문의 알고리즘을 한 줄씩 따라가며 정리했다.

**Initialize replay memory $D$ to capacity $N$**

Replay Buffer를 크기 $N$으로 초기화한다.

**Initialize action-value function $Q$ with random weights**

CNN의 weight를 초기화한다.

**for episode = 1, $M$ do**

최대 episode 수 $M$까지 반복한다.

**Initialize sequence $s_1 = \{x_1\}$ and preprocessed sequence $\phi_1 = \phi(s_1)$**

초기 state는 첫 프레임 $x_1$만으로 만든다. $\phi$는 전처리이며 다음을 수행한다.

1. RGB 화면을 흑백으로 바꾼다.
2. 화면을 줄이고 잘라 낸다.
3. 4 프레임을 쌓는다. 처음에는 $x_1$을 4장 복사해 채우고, 이후에는 가장 오래된 프레임을 빼고 최신 프레임을 넣는다.
4. 정규화한다.

**for t = 1, $T$ do**

episode가 끝날 때까지 반복한다.

**With probability $\epsilon$ select a random action $a_t$, otherwise select $a_t = \operatorname*{arg\,max}_a Q(\phi(s_t), a; \theta)$**

$\phi(s_t)$를 Q-network에 넣어 action별 Q값을 얻고, 여기에 $\epsilon$-greedy를 적용해 현재의 action을 고른다. 이것이 behavior policy다.

DQN은 매 프레임마다 action을 고르지 않는다. 논문에서는 4 프레임 단위로 $\phi$를 구성하고 action을 뽑는다. 모든 프레임을 다 보면 연산량이 너무 많고 상관관계도 지나치게 높아지기 때문이다. 새로운 4번째 프레임이 오기 전까지는 직전의 action을 그대로 반복한다.

**Execute action $a_t$ in emulator and observe reward $r_t$ and image $x_{t+1}$**

뽑은 action $a_t$를 실행해 reward $r_t$와 다음 화면 $x_{t+1}$을 얻는다.

**Set $s_{t+1} = s_t, a_t, x_{t+1}$ and preprocess $\phi_{t+1} = \phi(s_{t+1})$**

새 state $s_{t+1}$을 만들고 전처리한다.

**Store transition $(\phi_t, a_t, r_t, \phi_{t+1})$ in $D$**

나중에 TD Target을 구하는 데 쓰기 위해 transition을 $D$에 저장한다. transition도 4 프레임 단위로 만들어 저장한다.

**Sample random minibatch of transitions $(\phi_j, a_j, r_j, \phi_{j+1})$ from $D$**

$D$에서 균등한 확률로 mini-batch를 뽑는다.

**Set $y_j$**

$$
y_j =
\begin{cases}
r_j & \phi_{j+1}\text{이 terminal인 경우} \\
r_j + \gamma \max_{a'} Q(\phi_{j+1}, a'; \theta) & \text{그렇지 않은 경우}
\end{cases}
$$

terminal state라면 reward만 쓰고, 아니라면 TD Target까지 계산한다. TD Target은 $\phi_{j+1}$을 Q-network에 넣고 greedy action을 고른 값이다.

**Perform a gradient descent step on $(y_j - Q(\phi_j, a_j; \theta))^2$**

구한 TD Target과 Q값으로 gradient descent를 한 번 수행한다.

**end for, end for**

## DQN (2015): Target Network

2015년에는 Target Network가 추가된 알고리즘이 발표되었다.

2013년의 DQN은 TD Target을 상수로 취급해 gradient descent가 가능하게 했다. 그러나 TD Target을 학습 중인 바로 그 Q-network에서 얻기 때문에, weight가 바뀔 때마다 TD Target도 함께 바뀐다. 같은 입력을 넣어도 나중에는 target이 달라진다. 맞추려는 목표가 계속 움직이는 셈이다.

그래서 TD Target을 만드는 network를 따로 둔다. 알고리즘에서 바뀌는 곳은 세 군데다.

**1. Initialize target action-value function $\hat{Q}$ with weights $\theta^- = \theta$**

TD Target을 만드는 Target Network $\hat{Q}$를 Q-network와 같은 weight로 초기화한다.

**2. Set $y_j$**

$$
y_j =
\begin{cases}
r_j & \text{terminal인 경우} \\
r_j + \gamma \max_{a'} \hat{Q}(\phi_{j+1}, a'; \theta^-) & \text{그렇지 않은 경우}
\end{cases}
$$

TD Target을 $\theta^-$로 만든다. 이렇게 하면 같은 입력에 대해 TD Target이 계속 바뀌는 문제가 완화된다.

**3. Every $C$ steps reset $\hat{Q} = Q$**

Target Network도 처음에는 좋지 않은 weight를 가지고 있으므로, $C$ step마다 Q-network의 weight로 덮어써서 개선한다.

표본 하나에 대한 objective는 다음과 같이 바뀐다.

$$
\Big( R_t + \gamma \max_{a_{t+1}} Q_{w^-}(s_{t+1}, a_{t+1}) - Q_w(s_t, a_t) \Big)^2
$$

앞에서는 현재의 Q값에만 $w$를 붙였지만, 이제는 TD Target에도 첨자를 붙인다. Target Network는 Q-network와 구분되는 별도의 network이므로 $w^-$로 적는다.

## 정리

- DQN은 Q값을 표에 저장하는 대신 CNN으로 근사한다. 입력은 state(화면 4장), 출력은 action별 Q값이다.
- objective는 현재의 Q값과 TD Target의 차이의 제곱이며, TD Target은 상수로 취급한다.
- 연속된 state는 상관관계가 높아 SGD에 불리하다. Replay Buffer에 transition을 모아 두고 무작위로 뽑아 이 문제를 줄인다. off-policy이기 때문에 가능한 방법이다.
- Target Network를 따로 두어 TD Target이 학습 도중 계속 바뀌는 문제를 완화한다.
- DQN은 action별로 Q값을 하나씩 출력하므로 discrete action space에서만 쓸 수 있다. continuous한 action을 다루려면 다른 접근이 필요하고, 이것이 [다음 글](03-reinforce.md)의 주제다.

## 참고 자료

- V. Mnih et al., "Playing Atari with Deep Reinforcement Learning", NIPS Deep Learning Workshop, 2013. <https://arxiv.org/abs/1312.5602>
- V. Mnih et al., "Human-level control through deep reinforcement learning", Nature 518, 2015. <https://doi.org/10.1038/nature14236>
