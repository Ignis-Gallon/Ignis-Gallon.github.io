---
title: "강화학습 1: Bellman 방정식에서 Q-Learning까지"
date: 2025-03-12
last_modified_at: 2025-03-13
category: study
tags: [강화학습, Bellman 방정식, Monte Carlo, Temporal Difference, SARSA, Q-Learning]
---

# 강화학습 1: Bellman 방정식에서 Q-Learning까지

강화학습 시리즈의 첫 글이다. Value Function의 정의에서 출발해 Bellman 방정식을 유도하고, Optimal Q를 구하는 두 가지 방법인 Monte Carlo와 Temporal Difference를 거쳐 SARSA와 Q-Learning까지 정리한다. 그 과정에서 계속 등장하는 bias와 variance, on-policy와 off-policy의 구분도 함께 다룬다.

## 배경: MDP와 표기

강화학습의 문제는 Markov Decision Process(MDP)로 정의한다. MDP는 알고리즘이 아니라 문제를 적는 형식이다.

- **Markov Property**: 현재 state $s_t$만 알면, 과거 전체를 아는 것과 똑같이 다음 state $s_{t+1}$을 추론할 수 있다.
- **Transition Probability** $p(s_{t+1} \mid s_t, a_t)$: state $s_t$에서 action $a_t$를 했을 때 $s_{t+1}$로 옮겨 갈 확률이다. 환경이 정한다.
- **Policy** $p(a_t \mid s_t)$: state $s_t$에서 action $a_t$를 고를 확률이다. agent가 정한다.
- **Return** $G_t = R_t + \gamma R_{t+1} + \gamma^2 R_{t+2} + \cdots$: 시점 $t$부터 받는 reward의 할인된 합이다. $\gamma$는 미래 reward의 중요도를 정하는 discount factor다.

state와 action이 모두 확률 변수이고 policy와 transition도 확률적이므로, return 역시 확률 변수다. 그래서 return 자체가 아니라 그 기댓값을 다룬다.

이 글에서 $a_t{:}a_\infty$는 $a_t, s_{t+1}, a_{t+1}, s_{t+2}, \cdots$를 줄여 쓴 것이다. 그리고 이 글에서는 reward $R_t$를 next state $s_{t+1}$에 도달하면서 받는 것으로 본다.

## Bellman Equation

Bellman Equation은 State Value Function $V$를 Action Value Function $Q$나 다음 시점의 $V$로 표현하거나, $Q$를 $V$나 다음 시점의 $Q$로 표현하는 식이다. 이렇게 재귀적으로 적어 두면 뒤에서 여러 알고리즘이 여기서 나온다.

### State Value Function

$$
V(s_t) \triangleq \int G_t \, p(a_t, s_{t+1}, a_{t+1}, \cdots \mid s_t) \, d(a_t{:}a_\infty)
$$

현재 state $s_t$가 주어졌을 때, 앞으로 일어날 수 있는 모든 경우의 확률에 $G_t$를 곱해 적분한 것이다. 즉 $s_t$에서 기대되는 return이다.

여기서 $p$는 policy 하나를 뜻하는 것이 아니라, 앞으로 나올 모든 확률 변수를 한꺼번에 고려하는 결합 확률이다. 전체 trajectory의 확률은 다음과 같이 풀린다.

$$
p(s_0, a_0, s_1, a_1, s_2, a_2, \cdots) = p(s_0) \prod_{t=0}^{\infty} p(a_t \mid s_t) \, p(s_{t+1} \mid s_t, a_t)
$$

따라서 $s_t, a_t$의 나열만 고려하면 policy와 transition probability가 모두 고려된다.

### Action Value Function

$$
Q(s_t, a_t) \triangleq \int G_t \, p(s_{t+1}, a_{t+1}, s_{t+2}, a_{t+2}, \cdots \mid s_t, a_t) \, d(s_{t+1}{:}a_\infty)
$$

$s_t$와 $a_t$가 모두 주어졌을 때, 그 뒤에 나올 모든 state와 action에 대해 $G_t$를 적분한 것이다. 뒤따르는 변수를 모두 marginalize(integrate out)한다고 볼 수 있다.

### 네 가지 관계식

조건부 확률의 곱셈 법칙과 Markov Property를 이용하면 $V$와 $Q$를 서로, 그리고 다음 시점의 값으로 표현할 수 있다.

**V를 Q로 표현**

$$
V(s_t) = \int Q(s_t, a_t) \, p(a_t \mid s_t) \, da_t
$$

시점 $t$의 $V$는 그 state에서 고를 수 있는 모든 action에 대한 $Q$의 평균이다. $Q$에 policy를 곱해 $a_t$에 대해 적분한다.

**V를 next V로 표현**

$$
V(s_t) = \iint \big( R_t + \gamma V(s_{t+1}) \big) \, p(s_{t+1} \mid s_t, a_t) \, p(a_t \mid s_t) \, da_t \, ds_{t+1}
$$

reward $R_t$에 next state의 $V$에 $\gamma$를 곱한 값을 더하고, 여기에 시점 $t$의 policy와 transition probability를 곱한다. 모든 action $a_t$와 모든 next state $s_{t+1}$에 대해 적분한다.

**Q를 next V로 표현**

$$
Q(s_t, a_t) = \int \big( R_t + \gamma V(s_{t+1}) \big) \, p(s_{t+1} \mid s_t, a_t) \, ds_{t+1}
$$

바로 위 식과 거의 같다. $Q$는 action 하나가 이미 정해진 상태를 다루므로 policy를 곱하는 부분만 빠진다.

**Q를 next Q로 표현**

$$
Q(s_t, a_t) = \iint \big( R_t + \gamma Q(s_{t+1}, a_{t+1}) \big) \, p(a_{t+1} \mid s_{t+1}) \, p(s_{t+1} \mid s_t, a_t) \, ds_{t+1} \, da_{t+1}
$$

$a_t$를 하고 $s_{t+1}$로 옮겨 갈 transition probability와, 그 next state에서 $a_{t+1}$을 고를 policy를 모두 곱한다. 뒤에 나오는 TD와 SARSA가 이 식에서 출발한다.

## Optimal Policy

Optimal Policy는 $V$, 곧 지금부터 기대되는 return을 최대로 만드는 policy다.

$$
V(s_t) = \int Q(s_t, a_t) \, p(a_t \mid s_t) \, da_t
$$

이 식의 끝에는 policy $p(a_t \mid s_t)$가 곱해져 있다. 만약 optimal한 $Q$를 알고 있다면, 이 적분을 최대로 만드는 policy만 찾으면 된다. 다만 $Q$ 안에는 미래의 policy가 이미 들어 있으므로, optimal $Q$를 안다고 가정하려면 미래의 policy는 이미 optimal하다고 가정해야 한다.

!!! question "Q. Optimal한 Q값이란 무엇을 의미하는가?"

    Q값은 state와 action의 쌍마다 정해진다. 그러므로 Q가 optimal하다는 것은 각 쌍의 Q값이 잘 정해져 있다는 것, 곧 학습이 잘 되어 실제 값을 잘 배운 경우라고 이해했다. Q값은 잠재적으로 받을 수 있는 return의 기댓값이고, 이 값이 크다는 것은 미래에 받을 것으로 기대되는 return이 크다는 뜻이다. 그러므로 여러 Q값 중 가장 큰 것을 고르면 return이 커진다.

    조금 더 정확히 적으면, 위에서 말한 대로 "앞으로는 계속 optimal하게 행동한다"고 가정했을 때의 Q가 optimal Q, 곧 $Q^*$다.

$s_t$가 주어졌을 때 $Q^*(s_t, a_t)$는 $a_t$에 따라 값이 달라지는 함수다. 가로축을 $a_t$로 놓고 그리면 어딘가에 봉우리가 있고, 그 봉우리의 위치가 $a_t^*$다.

$$
a_t^* \triangleq \operatorname*{arg\,max}_{a_t} Q^*(s_t, a_t)
$$

위의 적분을 최대로 만들려면 policy가 이 $a_t^*$만 집중적으로 뽑으면 된다. 즉 optimal policy는 $a_t^*$에 놓인 $\delta$ function과 같은 모양이 된다. 이것이 Q값이 가장 큰 action을 고르는 greedy action이다.

다만 greedy action만 하면 처음 찾은 길에만 머물 수 있으므로 $\epsilon$-greedy로 exploration도 해야 한다. 그리고 지금까지의 논의는 optimal Q를 이미 구했다는 가정 위에 서 있다. 그렇다면 optimal Q는 어떻게 구하는가? 아래의 두 방법이 그 답이다.

## Monte Carlo Method

Monte Carlo(MC)는 큰 수의 법칙에 근거한다. 확률 변수 $x$에 확률을 곱해 적분하면 기댓값을 얻는데, 이는 표본을 충분히 많이 뽑아 평균 내는 것으로 근사할 수 있다.

$$
\mathbb{E}[x] \triangleq \int x \, p(x) \, dx \approx \frac{1}{N} \sum_{i=1}^{N} x_i
$$

이 아이디어를 Q에 그대로 적용한다. Q의 정의에 있는 적분을 직접 계산할 수는 없으므로, return의 표본을 충분히 모아 평균 낸다.

$$
Q(s_t, a_t) \approx \frac{1}{N} \sum_{i=1}^{N} G_t^{(i)}
$$

구체적으로는 episode를 끝까지 한 번 진행한 뒤, 지나온 경로를 거꾸로 되짚으며 각 $(s, a)$ 이후의 return $G_t$를 계산한다. 모든 $(s, a)$ 쌍마다 따로 평균을 낸다. 한 episode 안에서 같은 $(s, a)$를 여러 번 지났다면 return도 여러 개 얻는데, 이를 모두 평균에 넣을지 처음 한 번만 쓸지는 선택의 문제다.

학습은 다음과 같이 흘러간다.

1. 처음에는 Q값이 모두 0이므로 policy는 완전히 무작위로 움직인다. 성능이 나쁜 policy다.
2. exploration으로 우연히 좋은 경로를 찾으면, greedy action이 그 경로를 따라가게 만든다.
3. 이를 반복하면 MC로 구한 Q가 실제 Q에 가까워진다.
4. exploration의 비율 $\epsilon$은 점차 줄인다(decaying $\epsilon$-greedy). policy는 점점 optimal policy로 개선되고, 마지막에는 exploration 없이 exploitation만으로 optimal policy를 얻는다.

## Temporal Difference (TD)

TD의 기본 아이디어는 MC와 같다. 다만 $G_t$ 전체의 평균을 내는 대신, "Q를 next Q로 표현"한 Bellman 방정식에 표본 평균을 적용한다.

$$
Q(s_t, a_t) \approx \frac{1}{N} \sum_{i=1}^{N} \Big( R_t^{(i)} + \gamma Q\big(s_{t+1}^{(i)}, a_{t+1}^{(i)}\big) \Big)
$$

next state에서 얻는 reward와 그 state의 Q값만 있으면 표본 하나가 만들어진다. episode가 끝나기를 기다릴 필요가 없다.

### 1-step TD: 평균을 점진적으로 갱신하기

표본 $N$개의 평균 $\bar{Q}_N$을, 직전까지의 평균 $\bar{Q}_{N-1}$과 $N$번째 표본으로 적어 보자.

$$
\begin{aligned}
\bar{Q}_N &= \frac{1}{N} \sum_{i=1}^{N} \Big( R_t^{(i)} + \gamma Q\big(s_{t+1}^{(i)}, a_{t+1}^{(i)}\big) \Big) \\
&= \frac{1}{N} \Big( (N-1)\,\bar{Q}_{N-1} + R_t^{(N)} + \gamma Q\big(s_{t+1}^{(N)}, a_{t+1}^{(N)}\big) \Big) \\
&= \bar{Q}_{N-1} + \frac{1}{N} \Big( R_t^{(N)} + \gamma Q\big(s_{t+1}^{(N)}, a_{t+1}^{(N)}\big) - \bar{Q}_{N-1} \Big)
\end{aligned}
$$

둘째 줄에서는 앞의 $N-1$개 표본의 합을 평균에 $N-1$을 곱한 값으로 바꿔 썼다. 셋째 줄은 이를 전개해 정리한 것이다. 여기서 $\frac{1}{N}$을 $\alpha$로 바꾸면 다음과 같다.

$$
\bar{Q}_N = (1 - \alpha)\,\bar{Q}_{N-1} + \alpha \Big( R_t^{(N)} + \gamma Q\big(s_{t+1}^{(N)}, a_{t+1}^{(N)}\big) \Big)
$$

- $R_t^{(N)} + \gamma Q\big(s_{t+1}^{(N)}, a_{t+1}^{(N)}\big)$은 가장 최근의 표본이라는 뜻에서 **TD Target**이라고 한다.
- TD Target과 지금까지의 값의 차이 $R_t + \gamma Q(s_{t+1}, a_{t+1}) - Q(s_t, a_t)$를 **TD Error**(Temporal Difference)라고 한다.
- $\alpha$가 크면 새로 들어온 값에, 작으면 기존 값에 더 무게를 둔다.

정리하면, 표본 9개의 평균을 구해 둔 상태에서 10번째 표본이 들어왔을 때 10개를 처음부터 다시 평균 낼 필요가 없다. 9개까지의 평균을 가지고 있다가 10번째 값만 반영해 갱신하면 된다. 이것이 한 step마다 Q를 갱신할 수 있는 이유다.

### SARSA

이 TD 방식으로 Q값을 갱신하는 알고리즘이 SARSA다.

$$
Q(s_t, a_t) \leftarrow (1 - \alpha)\,Q(s_t, a_t) + \alpha \big( R_t + \gamma Q(s_{t+1}, a_{t+1}) \big)
$$

지금까지의 값을 바탕으로, reward와 next state에서의 Q값으로 현재의 Q값을 조금씩 고친다. 식에 필요한 것이 $(s_t, a_t, R_t, s_{t+1}, a_{t+1})$이라서 이름이 SARSA다.

## Bias와 Variance

MC와 TD의 차이는 bias와 variance로 요약된다. 이 구분은 뒤의 Policy Gradient 계열에서도 계속 나온다.

| | Monte Carlo | Temporal Difference |
|---|---|---|
| 표본 | 실제 return $G_t$ | $R_t + \gamma Q(s_{t+1}, a_{t+1})$ |
| 갱신 시점 | episode가 끝난 뒤 | 매 step |
| bias | 없다 | 있다 |
| variance | 크다 | 작다 |

**Monte Carlo**

- Unbiased: 표본 return을 직접 써서 기댓값을 추정하므로, 표본이 충분하면 실제 기댓값으로 수렴한다.
- High variance: episode마다 return이 크게 다를 수 있다. $G_t$의 변동이 크면 수렴이 늦고 학습이 불안정해질 수 있다.
- 모든 $(s, a)$를 충분히 방문해야 한다. 방문 횟수가 적은 쌍은 추정값을 믿기 어렵다.
- 여기서 다룬 형태의 MC는 on-policy라서 한 번 쓴 데이터를 다시 쓰지 못한다.

**Temporal Difference**

- Low variance: 한 번에 transition 하나만 보고 갱신하므로 값의 변동이 크지 않다.
- Biased: 실제 return이 아니라, 지금까지의 reward와 "추정된" Value Function으로 갱신한다. 추정값을 다시 추정에 쓰기 때문에 편향이 생긴다.
- off-policy가 가능해 MC보다 데이터를 재사용하기 쉽다.

## On-policy와 Off-policy

두 가지 policy를 구분한다.

- **Behavior Policy**: $s_t$에서 $s_{t+1}$로 옮겨 가기 위해 agent가 실제로 행동하는 policy
- **Target Policy**: TD Target(표본)을 만들 때 쓰는 policy, 곧 $s_{t+1}$에서 $a_{t+1}$을 고르는 policy

### On-policy

Behavior Policy와 Target Policy가 같은 경우다. SARSA가 여기에 속한다. agent가 $\epsilon$-greedy로 행동하고, TD Target을 만들 때도 같은 $\epsilon$-greedy로 $a_{t+1}$을 뽑는다.

- 항상 지금의 policy로만 표본을 만들기 때문에 과거의 데이터를 쓰지 못한다.
- 늘 새로운 결과만 반영하므로 수렴이 느릴 수 있다.
- 대신 계속 exploration을 하므로 조금 더 안전하게 학습될 가능성이 있다.

### Off-policy

Behavior Policy와 Target Policy가 다른 경우다. Q-Learning이 여기에 속한다. behavior policy는 $\epsilon$-greedy이지만 target policy는 greedy다.

- 지금 어떻게 행동하고 있는지와 무관하게 "가장 좋은 action을 했다면"을 기준으로 학습하므로, 과거에 모은 표본도 다시 평가해 쓸 수 있다. 표본 재사용성이 높다.
- 그래서 수렴이 빠르다.
- 다만 환경이 바뀌면 적응이 어려울 수 있고, 안정적이지 않을 수 있다.

두 방식의 차이를 쉽게 생각하면 이렇다. SARSA는 target을 뽑을 때도 $\epsilon$-greedy를 해야 한다. "과거에 좋았던 곳은 저기인데, 나는 또 $\epsilon$의 확률로 엉뚱한 곳에 가야 한다"는 느낌이다. Q-Learning은 "과거에 저기가 좋았으니 저쪽을 따라가 보자"에 가깝다. SARSA도 학습이 진행될수록 $\epsilon$이 낮아지므로 끝에 가서는 Q-Learning과 거의 같아진다.

## Q-Learning

$$
Q(s_t, a_t) \leftarrow (1 - \alpha)\,Q(s_t, a_t) + \alpha \Big( R_t + \gamma \max_{a_{t+1}} Q(s_{t+1}, a_{t+1}) \Big)
$$

SARSA의 식에서 $a_{t+1}$을 고르는 방법만 바꾼 것이다. Q-Learning은 Target Policy를 greedy selection으로 하는 off-policy 방법이다.

초기 Q값이 모두 0일 때의 차이를 생각해 볼 수 있다. Q-Learning의 target은 next state의 Q값 중 최댓값이다. next state에서 어떤 action이 음수 reward를 받아 Q값이 음수가 되었더라도, max는 그 action을 고르지 않으므로 그 음수 값은 앞의 state로 전달되지 않는다. 나쁜 결과를 덜 반영한다는 점에서 risky한 학습이고, 경우에 따라 SARSA보다 optimal을 먼저 찾을 수 있다.

## 정리

- $V$는 state에서, $Q$는 state와 action의 쌍에서 기대되는 return이다. Bellman 방정식은 이 둘을 서로, 그리고 다음 시점의 값으로 적은 것이다.
- Optimal Q를 안다면 optimal policy는 Q가 가장 큰 action을 고르는 것이다.
- Optimal Q는 표본 평균으로 구한다. return 전체를 평균 내면 Monte Carlo(unbiased, high variance), 한 step 뒤의 Q로 대신하면 Temporal Difference(biased, low variance)다.
- TD Target을 behavior policy와 같은 policy로 만들면 SARSA(on-policy), greedy로 만들면 Q-Learning(off-policy)이다.
- Q-Learning은 Q값을 표로 저장한다. state가 너무 많아지면 이 방식은 쓸 수 없고, 이 문제를 신경망으로 푸는 것이 [다음 글](02-dqn.md)의 DQN이다.

## 참고 자료

- R. S. Sutton, A. G. Barto, "Reinforcement Learning: An Introduction", 2nd ed., MIT Press, 2018. <http://incompleteideas.net/book/the-book-2nd.html>
- C. J. C. H. Watkins, P. Dayan, "Q-learning", Machine Learning 8, 1992.
- MDP, Bellman 방정식, Monte Carlo와 TD를 처음 정리할 때 참고한 블로그 글: <https://skidrow6122.tistory.com/4>, <https://skidrow6122.tistory.com/6>, <https://skidrow6122.tistory.com/7>
