---
title: "학습 기법 복습: regularization, optimizer, weight initialization"
date: 2024-12-01
last_modified_at: 2024-12-01
category: study
tags: [Regularization, Batch Normalization, Optimizer, Adam, Weight Initialization, 딥러닝]
---

# 학습 기법 복습: regularization, optimizer, weight initialization

작성일 2024-12-01
{ .post-date }

신경망을 학습시킬 때 거의 항상 따라오는 세 가지 주제를 한 번에 복습한 노트다. 과적합을 막는 regularization, 파라미터를 갱신하는 optimizer, 학습의 출발점을 정하는 weight initialization을 차례로 본다. 이미 배운 내용을 다시 꺼내 정리한 것이어서, 유도보다는 "왜 그렇게 하는가"를 내 말로 적는 데 무게를 두었다. Optimizer의 갱신 식은 노트에 강의 슬라이드의 코드로 남아 있던 것을 수식으로 옮겼다.

## 1. Regularization

### Weight constraint: L1과 L2

가중치의 값이 일정한 범위 안에 머물도록 제한하는 방법이다. Loss에 가중치의 크기에 대한 항을 더하므로, 제약이 붙은 최적화 문제로도 볼 수 있다. 두 식에서 $\lambda$는 규제 항을 얼마나 반영할지를 정하는 hyperparameter다.

**L1 regularization (Lasso).** Cost function에 가중치의 절댓값의 합(L1 norm)을 더한다.

$$
L = \mathrm{Loss} + \lambda \sum_i |w_i|
$$

최적화 과정에서 일부 가중치가 0이 되어 feature selection과 같은 효과를 낸다. 필요한 가중치만 남으므로 sparse한 모델이 된다.

왜 0이 되는지는 미분으로 이해했다. $|w|$를 미분하면 값의 크기와 상관없이 부호에 따라 $+1$ 또는 $-1$만 남는다. 그래서 규제 항은 가중치가 크든 작든 매번 $\lambda$에 비례하는 일정한 크기만큼을 0 쪽으로 밀어 준다. 작은 가중치는 이 일정한 힘을 버티지 못하고 0에 도달한다.

!!! question "Q. 가중치가 정확히 0이 되는 것은 어떻게 구현되는가?"
    다음과 같은 갱신을 쓴다. 가중치의 크기가 $\lambda$보다 작으면 0으로 만들고, 크면 $\lambda$만큼 줄인다. 이를 soft thresholding이라고 한다.

    $$
    w_i \leftarrow \mathrm{sign}(w_i)\, \max\big(|w_i| - \lambda,\ 0\big)
    $$

    이렇게 따로 처리하는 이유를 "절댓값이 0에서 미분되지 않기 때문인가?"라고 적어 두었는데, 그렇게 이해하면 된다. $|w|$는 0에서 미분이 정의되지 않으므로 gradient를 그대로 쓰면 가중치가 0 근처에서 부호를 바꿔 가며 진동할 뿐 정확히 0에 멈추지 않는다. Soft thresholding은 이 지점을 직접 처리해 주는 갱신 규칙이다.

**L2 regularization (Ridge).** Cost function에 가중치의 제곱합(L2 norm의 제곱)을 더한다.

$$
L = \mathrm{Loss} + \lambda \sum_i w_i^2
$$

가중치가 너무 큰 값을 갖지 못하게 억제해 안정적인 모델을 만든다. 달리 말하면 모든 가중치가 고르게 쓰이도록 유도한다. 합이 같다면 제곱합은 값들이 서로 같을 때 가장 작아지기 때문이다.

L2는 가중치를 0에 가깝게 만들지만 정확히 0으로 만들지는 않는다. $w^2$의 미분은 $2w$여서 가중치가 작아질수록 미는 힘도 함께 작아지기 때문이다.

거리로 비유하면 L1은 Manhattan distance, L2는 Euclidean distance에 해당한다.

### Dropout

학습 단계에서 무작위로 뉴런을 비활성화해 네트워크를 단순하게 만든다. 네트워크가 일부 뉴런의 정보에만 의존하지 못하도록 강제하는 것이다. 추론 단계에서는 dropout을 쓰지 않는다.

### Early stopping

학습 도중 검증 데이터에서의 성능이 더 좋아지지 않으면 학습을 멈춘다. 과적합이 시작되기 전에 멈춰 일반화 성능을 확보한다.

### Data augmentation

기존 데이터를 회전, 확대, 축소 등으로 변형해 학습 데이터를 인위적으로 늘린다. 모델이 다양한 데이터를 경험하게 해서 과적합을 막는다.

### Batch Normalization

각 층의 입력을 정규화해 학습 속도를 높이고 과적합을 줄인다. 따로 떨어진 전처리 과정이 아니라 신경망 안에 layer 하나로 들어가, 학습 중에 평균과 분산을 조정한다.

**왜 필요한가.** Activation function을 거치면 비선형 변환이 일어나면서 데이터가 원래 갖고 있던 퍼짐(standard deviation)이 사라진다. 이 말을 풀면 이렇다. 값들이 한쪽으로 몰려서 activation의 미분값이 0에 가까운 구간에 모이면 vanishing gradient가 생길 수 있고, 반대의 경우에는 exploding gradient가 생길 수 있다. 그래서 activation을 거치기 전에 Batch Normalization을 두어, 분산이 너무 크면 줄이고 너무 작으면 키워 준다.

**계산.** 학습은 대부분 mini-batch 단위로 하므로, batch 안에서 feature(unit)별로 평균과 표준편차를 구해 정규화한다. 정규화한 값은 평균이 0, 표준편차가 1이 된다. Layer $l$의 unit 수가 $n^{[l]}$이라면 평균과 표준편차의 shape은 $(n^{[l]}, 1)$이다.

$$
\hat{z} = \frac{z - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}}, \qquad \tilde{z} = \gamma \hat{z} + \beta
$$

그런데 모든 층의 입력을 평균 0 근처로 맞춰 버리면, ReLU 같은 activation에서는 값의 절반가량이 잘려 나간다. 그래서 학습되는 파라미터 $\gamma$와 $\beta$를 두어 층마다 적절한 분포를 스스로 찾게 한다. $\gamma$가 새로운 표준편차, $\beta$가 새로운 평균의 역할을 한다.

**위치.** 대체로 activation 앞에 둔다.

**Test time.** 테스트 때는 평균과 표준편차를 구할 batch가 충분하지 않으므로 학습 때 구한 값을 빌려 쓴다. 학습 중에 exponentially weighted moving average로 값을 계속 갱신해 두고, 테스트 때 그 값을 쓴다. Layer마다 이 값을 저장해 둔다.

$$
\mu \leftarrow 0.9\,\mu + 0.1\,\mu_B, \qquad \sigma^2 \leftarrow 0.9\,\sigma^2 + 0.1\,\sigma_B^2
$$

**Bias가 필요 없다.** Batch Normalization을 쓰는 층에서는 bias를 따로 두지 않아도 된다. Bias는 데이터의 평균을 옮기는 역할인데, 바로 뒤에서 평균을 빼 버리므로 의미가 없어진다. 그 역할은 $\beta$가 대신한다.

**이점.**

- 학습이 빨라진다. Internal covariate shift를 줄여 모델이 더 빠르게 학습한다고 설명된다.
- 규제 효과가 있다. Batch마다 통계량이 조금씩 달라 약한 잡음이 더해지는 셈이어서, 상황에 따라 dropout 같은 다른 규제 기법을 쓰지 않아도 될 수 있다.
- 더 큰 learning rate를 쓸 수 있다.

**Internal covariate shift.** Covariate shift는 입력의 분포 $P(X)$가 학습 데이터와 테스트 데이터에서 달라지는 현상이다. Internal covariate shift는 이것이 네트워크 내부에서 일어나는 것으로, 앞 층의 가중치가 갱신될 때마다 뒤 층이 받는 입력의 분포가 달라지는 현상을 말한다. Batch Normalization은 층이 깊어져도 입력이 일관된 분포를 갖게 해서 안정적인 학습과 빠른 수렴을 만든다는 것이 원래의 설명이다.

그런데 이 설명이 사실이 아니라는 MIT의 연구가 있다는 것을 중요하게 적어 두었다. VGG 네트워크의 Batch Normalization 뒤에 무작위 잡음을 넣어 분포를 일부러 흔들었는데도, Batch Normalization이 없는 네트워크보다 성능이 좋았다는 것이다. Internal covariate shift를 줄이는 것이 성능 향상의 원인이라면 나올 수 없는 결과다. 이 연구는 Batch Normalization의 이점이 loss function의 표면을 매끄럽게 만드는(smoothing) 효과에서 온다고 주장한다. 여전히 이견은 있다.

## 2. Optimizer

### 한 번에 얼마나 보고 갱신하는가

**(Batch) Gradient Descent.** 데이터 전체를 보고 파라미터를 한 번 갱신한다. 갱신 횟수가 적고, vectorization을 온전히 활용할 수 있으며, 전체 데이터로 gradient를 계산하므로 잡음이 매우 적어 안정적으로 수렴한다. 그러나 하드웨어의 한계 때문에 큰 데이터셋에서는 실용적이지 않다.

**Stochastic Gradient Descent.** 샘플 하나마다 파라미터를 갱신한다. 갱신 횟수가 매우 많고 vectorization을 쓰지 못하며 잡음도 매우 크다. 이 형태 그대로는 거의 쓰지 않는다.

**Mini-batch Stochastic Gradient Descent.** Mini-batch마다 갱신한다. 앞의 두 방법의 중간이고 가장 실용적이다.

다만 여기까지는 learning rate가 고정되어 있다는 문제가 남는다. 학습이 learning rate의 설정에 크게 의존한다.

### 갱신 규칙을 바꾼다

지금까지의 갱신은 gradient에 learning rate를 곱해 파라미터에서 빼는 것이 전부였다.

$$
w \leftarrow w - \eta\, dw
$$

이 규칙을 바꾸면 학습이 훨씬 빨라진다. 아래에서 $\eta$는 learning rate, $dw$는 gradient다.

**Momentum.** Mini-batch를 쓰더라도 gradient에는 잡음이 섞여 있어 loss가 요동친다. Momentum은 이 요동을 moving average로 줄인다. 속도를 뜻하는 $v$와 hyperparameter $\mu$(보통 0.9)를 두고, 과거의 갱신 방향을 지수적으로 누적한다.

$$
v \leftarrow \mu\, v - \eta\, dw, \qquad w \leftarrow w + v
$$

부호가 계속 바뀌는 방향(가파른 골짜기를 좌우로 오가는 방향)의 변화는 서로 상쇄되어 작아지고, 같은 부호가 이어지는 방향(완만하게 내려가는 방향)의 변화는 쌓여서 커진다. 기억해 둘 점은 momentum에서는 계산한 velocity를 파라미터에 그대로 **더한다**는 것이다.

**RMSprop.** Root Mean Squared Propagation. Momentum과 비슷한 목표를 다른 방식으로 이룬다. Decay rate라는 hyperparameter(예: 0.99)를 두고, gradient의 제곱에 대한 moving average를 cache에 쌓는다.

$$
\mathrm{cache} \leftarrow \rho \cdot \mathrm{cache} + (1 - \rho)\, dw^2, \qquad
w \leftarrow w - \eta\, \frac{dw}{\sqrt{\mathrm{cache}} + \epsilon}
$$

제곱을 하므로 큰 gradient는 더 크게, 작은 gradient는 더 작게 cache에 반영된다. 갱신할 때는 gradient를 cache의 제곱근으로 나눈다. 그러면 변화량이 컸던 가중치는 작게, 작았던 가중치는 크게 움직여서 모든 가중치가 비슷한 크기로 갱신된다. 일종의 정규화다. $\epsilon$은 0으로 나누는 것을 막기 위한 아주 작은 값이다.

**Adam.** Momentum과 RMSprop을 합친 방식이다. Momentum의 velocity에 해당하는 $m$과 RMSprop의 cache에 해당하는 $v$를 함께 계산하고, $m$을 $\sqrt{v}$로 나눈 값으로 갱신한다. 요동을 줄이는 효과와 모든 가중치를 고르게 갱신하는 효과를 동시에 얻는다.

$$
\begin{aligned}
m &\leftarrow \beta_1 m + (1 - \beta_1)\, dw \\
v &\leftarrow \beta_2 v + (1 - \beta_2)\, dw^2 \\
\hat{m} &= \frac{m}{1 - \beta_1^t}, \qquad \hat{v} = \frac{v}{1 - \beta_2^t} \\
w &\leftarrow w - \eta\, \frac{\hat{m}}{\sqrt{\hat{v}} + \epsilon}
\end{aligned}
$$

셋째 줄은 bias correction이다. $m$과 $v$를 0에서 시작하므로 처음 몇 step 동안은 값이 실제보다 작게 나오는데, 이를 보정한다. $t$가 커지면 분모가 1에 가까워져 영향이 사라진다.

## 3. Weight initialization

가중치 초기화가 중요한 이유는, activation function의 특성에 맞는 방식을 고르지 않으면 layer를 거칠 때마다 값의 표준편차가 줄어들어 결국 0으로 수렴할 수 있기 때문이다.

- **아주 작은 난수로 초기화하면**: 깊은 층으로 갈수록 activation이 대부분 0이 된다. 그러면 gradient도 0이 되어 파라미터가 갱신되지 않는 vanishing gradient가 생긴다.
- **조금 큰 값(1.0 안팎)으로 초기화하면**: activation function에 따라서는 값이 포화 구간으로 몰려 역시 gradient가 사라질 수 있다.

**Xavier initialization.** Layer의 입력과 출력의 크기를 고려해 초기값의 분산을 정한다. 아주 작은 난수를 쓰는 대신 unit 수의 제곱근으로 scale을 맞춰 주어, activation을 거쳐도 표준편차가 어느 정도 유지되게 한다.

$$
W \sim U\left(-\sqrt{\frac{6}{n_{in} + n_{out}}},\ \sqrt{\frac{6}{n_{in} + n_{out}}}\right)
\quad \text{또는} \quad
W \sim \mathcal{N}\left(0,\ \frac{2}{n_{in} + n_{out}}\right)
$$

다만 ReLU에서는 효과가 크게 떨어진다. ReLU가 음수 쪽을 0으로 잘라 버리기 때문이다.

**He initialization.** Xavier와 큰 차이는 없다. ReLU에서 절반이 잘려 나가는 것을 감안해 값을 조금 더 크게 초기화한다.

$$
W \sim U\left(-\sqrt{\frac{6}{n_{in}}},\ \sqrt{\frac{6}{n_{in}}}\right)
\quad \text{또는} \quad
W \sim \mathcal{N}\left(0,\ \frac{2}{n_{in}}\right)
$$

초기화가 0 근처의 값에서 시작한다는 사실은 [ResNet](01-resnet.md)에서 skip connection이 학습을 쉽게 만드는 이유를 설명할 때도 쓰였다.

## 정리

- L1은 일정한 크기로 가중치를 0 쪽으로 밀어 sparse한 모델을 만들고, L2는 가중치에 비례해 줄여서 작지만 0은 아닌 값들로 만든다.
- Batch Normalization은 층의 입력을 정규화하되 $\gamma$, $\beta$로 분포를 다시 학습한다. 왜 잘 되는지에 대해서는 internal covariate shift 설명과 loss smoothing 설명이 맞서 있다.
- Momentum은 gradient의 방향을, RMSprop은 gradient의 크기를 moving average로 다루고, Adam은 둘을 합친다.
- 초기화는 층을 지나도 값의 분산이 유지되도록 정한다. tanh 계열에는 Xavier, ReLU에는 He가 맞는다.

## 참고 자료

- S. Ioffe, C. Szegedy, "Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift", ICML 2015. <https://arxiv.org/abs/1502.03167>
- S. Santurkar, D. Tsipras, A. Ilyas, A. Madry, "How Does Batch Normalization Help Optimization?", NeurIPS 2018. <https://arxiv.org/abs/1805.11604>
- D. P. Kingma, J. Ba, "Adam: A Method for Stochastic Optimization", ICLR 2015. <https://arxiv.org/abs/1412.6980>
- X. Glorot, Y. Bengio, "Understanding the difficulty of training deep feedforward neural networks", AISTATS 2010.
- K. He, X. Zhang, S. Ren, J. Sun, "Delving Deep into Rectifiers: Surpassing Human-Level Performance on ImageNet Classification", ICCV 2015. <https://arxiv.org/abs/1502.01852>
