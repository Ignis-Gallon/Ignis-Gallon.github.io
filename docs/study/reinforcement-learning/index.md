---
title: "강화학습"
---

# 강화학습

강화학습으로 시계열의 변화점을 고르는 연구를 읽고 직접 실험하기 위해, 강화학습 알고리즘을 기초부터 차례로 정리한 노트입니다. Value-based 방법에서 시작해 Policy Gradient, Actor-Critic 계열을 거쳐 PPO와 DDPG까지, 공부한 순서대로 여섯 편으로 나누었습니다. 수식은 유도 과정을 따라가며 직접 적었고, 공부하면서 스스로 던진 질문도 함께 남겼습니다.

- [1. Bellman 방정식에서 Q-Learning까지](01-q-learning.md): State/Action Value Function과 Bellman 방정식, Optimal Policy, Monte Carlo와 Temporal Difference, SARSA와 Q-Learning, bias와 variance, on-policy와 off-policy
- [2. DQN](02-dqn.md): Q를 신경망으로 근사하는 이유, objective function과 그 미분, state correlation과 Replay Buffer, 2013년 알고리즘, 2015년의 Target Network
- [3. Policy Gradient와 REINFORCE](03-reinforce.md): policy를 직접 학습하는 이유, objective function $J$의 gradient를 log 미분 trick으로 표본 평균 형태로 바꾸는 과정, REINFORCE 알고리즘과 그 variance
- [4. Actor-Critic, A2C, A3C](04-actor-critic.md): Policy Gradient 식에서 $Q$를 꺼내는 유도, Advantage와 baseline, 비동기 worker, n-step TD, entropy
- [5. PPO](05-ppo.md): Importance Sampling으로 표본을 재사용하는 원리, Clipping, GAE
- [6. DDPG](06-ddpg.md): Deterministic Policy의 gradient 유도, off-policy가 되는 이유, Replay Buffer와 Target Network를 쓰는 알고리즘

이 시리즈에서 정리한 DDPG로 실제 실험을 해 본 기록은 [DDPG 주식 거래 논문 재현과 단일 종목 실험](../../research/sailab/05-ddpg-stock-trading.md)에 있습니다.
