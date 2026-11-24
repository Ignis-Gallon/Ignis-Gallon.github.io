---
title: "Granger causality의 확장: Lasso, 커널, 신경망"
date: 2025-05-06
last_modified_at: 2025-05-20
category: study
tags: [인과 발견, Granger causality, Lasso, 커널 방법, 딥러닝, 시계열]
---

# Granger causality의 확장: Lasso, 커널, 신경망

작성일 2025-05-06 · 최종 수정일 2025-05-20
{ .post-date }

Granger causality는 시계열에서 인과를 다루는 가장 오래된 도구이고, 지금도 새로운 변형이 계속 나온다. 원래의 정의는 선형 모델과 변수 쌍에 대한 것이어서 변수가 많거나 관계가 비선형이면 그대로 쓰기 어렵다. 이 글에서는 기본 정의를 확인한 뒤, 고차원을 다루기 위한 Lasso 계열, 비선형성을 다루기 위한 커널 계열과 신경망 계열을 순서대로 정리하고자 한다.

## 1. 기본 정의

시계열 $X^p$의 과거 값을 반영하는 것이 $X^q$의 미래 값 예측에 통계적으로 유의한 정보를 준다면, $X^p$가 $X^q$를 Granger-cause한다고 말한다. 몇 가지 약점에도 불구하고 예측 성능을 높이는 도구로 널리 쓰여 왔다.

### Pairwise Granger causality

가장 단순한 형태는 정상(stationary) 선형 시스템을 가정하고 두 자기회귀 모델을 비교한다. $X^q$ 자신의 과거만 쓰는 제한 모델과

$$
X_t^q = a_{q,0} + \sum_{i=1}^{\tau} a_{q,i} X_{t-i}^q + \xi_t^q
$$

$X^p$의 과거를 추가한 전체 모델이다.

$$
X_t^q = a_{q,0} + \sum_{i=1}^{\tau} a_{q,i} X_{t-i}^q + \sum_{i=1}^{\tau} a_{p,i} X_{t-i}^p + \xi_t^q
$$

전체 모델의 예측 오차가 제한 모델보다 유의하게 작으면 $X^p$가 $X^q$를 Granger-cause한다고 판정한다. 판정에는 F 통계량, AIC/BIC, p-value 같은 지표를 쓴다.

### Multivariate Granger causality

변수 쌍만 보면 공통 원인을 반영하지 못한다. 이를 보완하려면 관련된 정보를 모두 분석에 넣어야 한다. $d$차원 시계열에서 VAR 모델을 쓰되, 제한 모델에서는 $X^p$만 뺀다. conditional Granger causality라고도 한다.

$$
X_t^q = a_{q,0} + \sum_{\substack{r=1 \\ r \ne p}}^{d} \sum_{i=1}^{\tau} a_{r,i} X_{t-i}^r + \xi_t^q
\qquad \text{vs.} \qquad
X_t^q = a_{q,0} + \sum_{r=1}^{d} \sum_{i=1}^{\tau} a_{r,i} X_{t-i}^r + \xi_t^q
$$

결과는 pairwise보다 일반적으로 좋지만 계산량이 커서, 많은 연구가 pairwise 형태를 다룬다.

### 한계

- 선형 가정 때문에 비선형 관계에 대응하기 어렵다.
- 원래의 형태는 비정상 과정을 다루지 못한다.
- 원인이 항상 과거에 있어야 한다는 전제 때문에 동시점 관계를 다루지 못한다.
- 분석에 포함되지 않은 공통 원인이 있으면 가짜 인과가 나타난다. Granger 자신이 spurious causality라고 부른 현상이다.
- 변수가 많으면 VAR의 추정이 불안정해지고 다중공선성 문제가 생긴다.

아래의 확장들은 이 가운데 주로 고차원과 비선형성을 겨냥한다. 동시점 관계와 숨은 교란 변수를 다루는 방법은 일부에 그친다.

## 2. 고차원: Lasso 계열

### Graphical Granger와 Lasso-Granger

"Temporal Causal Modeling with Graphical Granger Methods"는 Granger causality를 그래프 모델의 구조 학습과 결합하는 방법들을 비교한다.

논문의 관심사는 조금 독특하다. 시점별 변수 사이의 관계가 아니라, 시간에 걸쳐 유지되는 feature 사이의 인과 구조를 알고자 한다. 예로 드는 것이 기업의 핵심 성과 지표(KPI)다. "주가가 재고회전율의 인과적 영향을 받는가"를 물을 때 알고 싶은 것은 재고회전율이 며칠 뒤의 주가에 영향을 주는지가 아니라, 영향을 주는지 여부와 그 정도다. 그래서 논문은 feature를 노드로 하는 방향 그래프인 feature causal network를 정의하고, 이를 복원하는 것을 목표로 삼는다. 시간 지연이나 데이터 생성 모델의 세부는 목표에서 제외된다. 일반적인 시계열 모델링이 데이터를 생성하는 모델 자체를 복원하려는 것과 목적이 다르다. 알고리즘의 성능은 출력한 그래프와 정답 그래프의 유사도로 평가한다.

**Exhaustive Granger.** 가장 단순한 방법은 모든 쌍에 대해 Granger 검정을 하는 것이다.

```text
1. 시계열 X를 최대 지연 T까지의 지연 변수로 펼친다
2. 모든 feature 쌍을 잇는 그래프에서 시작한다
3. 각 쌍 (x, y)에 대해
   - x가 y를 Granger-cause하고 반대는 아니면   x → y
   - y가 x를 Granger-cause하고 반대는 아니면   x ← y
   - 양쪽 모두 성립하면                        방향 없는 간선 (x ↔ y)
   - 둘 다 아니면                              간선 없음
```

이 방법은 feature의 수 $p$에 대해 $O(p^2)$번의 회귀가 필요해 $p$가 크면 적용하기 어렵다. 통계적으로도 문제가 있다. 모든 쌍을 따로 검정하므로 여러 feature가 동시에 주는 영향을 고려하지 못한다.

**Lasso-Granger.** 회귀를 이웃 선택(neighborhood selection) 문제에 적용한다. 이웃 선택은 주어진 변수 하나에 대해, 다른 변수들을 조건으로 두어도 여전히 그 변수와 의존 관계에 있는 변수들의 집합을 찾는 문제다. 그래프 구조 학습의 핵심 개념이다. 근거는 다음 사실이다. 어떤 변수를 최소 제곱 오차 기준으로 가장 잘 설명하는 회귀식은 그 변수의 이웃에 해당하는 변수에 대해서만 0이 아닌 계수를 갖는다.

그래서 각 변수 $y$를 모든 변수의 과거 값 $\vec{x}$에 대해 Lasso로 회귀한다.

$$
\vec{w} = \arg\min_{\vec{w}} \frac{1}{n} \sum_{(\vec{x}, y) \in S} \big\vert \vec{w} \cdot \vec{x} - y \big\vert^2 + \lambda \lVert \vec{w} \rVert_1
$$

계수가 0이 아닌 변수가 $y$의 이웃 후보가 된다. 변수마다 이웃을 찾아 합치면 전체 그래프가 된다. L1 벌점이 변수 선택을 겸하므로 변수 쌍마다 검정을 반복할 필요가 없다.

Lasso의 해를 구하는 데는 LARS(Least Angle Regression)가 쓰인다. LARS는 모든 계수를 0에서 시작해, 응답 변수와 가장 상관이 높은 변수 하나를 골라 그 계수만 조금씩 키운다. 다른 변수가 같은 정도의 상관을 갖게 되는 시점까지 계속한 뒤 그 변수를 추가한다. 한 번에 한 변수씩 점진적으로 계수를 갱신하므로 희소성이 유지된다. 이름은 변수를 추가할 때 이미 선택된 변수들과 이루는 각도를 고려한다는 데서 왔다.

Lasso를 구조 학습에 쓰는 큰 장점은 일관성(consistency)이다. 이웃이 아닌 변수의 수가 관측 수에 비해 빠르게 늘어나더라도, 이웃이 아닌 변수를 이웃으로 잘못 고를 확률은 지수적으로 빠르게 줄어든다. 표본이 충분히 많아지면 Lasso-Granger가 정답 그래프를 점점 정확하게 복원한다는 이론적 보장이 된다.

같은 계열의 후속 연구로 "Discovering graphical Granger causality using the truncating lasso penalty"가 있다.

### 고차원 VAR에서의 검정

"Granger causality testing in high dimensional VARs: A post-double-selection procedure"(2021)는 선택이 아니라 검정의 문제를 다룬다.

Granger causality 검정의 가설은 다음과 같다.

- $H_0$: $X$는 $Y$의 미래 값에 영향을 주지 않는다(Granger-cause하지 않는다).
- $H_1$: $X$는 $Y$를 Granger-cause한다.

회귀식에서 해당 변수의 계수가 0인지를 검정하는 것이며, 여기에는 LM(Lagrange Multiplier) 검정을 쓴다. LM 검정은 제약이 있는 모형과 없는 모형의 차이를 검정하는 방법이다.

고차원 VAR에서는 Lasso로 관련 변수를 먼저 고른 뒤 그 위에서 검정을 하면 된다고 생각하기 쉽다. 그러나 변수 선택을 거친 뒤의 검정은 검정의 크기(size)가 틀어진다. 논문은 Lasso 선택 이후에도 적절한 크기를 얻도록, 관심 대상이 아닌 변수(nuisance variable)의 효과를 제거하는 post-double-selection 절차를 제안하고 그 점근적 유효성을 보인다.

## 3. 비선형: 커널 계열

비선형 인과 관계를 모델 기반으로 잡는 일반적인 방법은 비선형 모델을 쓰는 것이다. Granger causality에서는 먼저 커널 방법으로의 확장이 이루어졌다. radial basis function을 써서 이변량 비선형 경우로 확장한 연구가 있었고, 이후 reproducing kernel Hilbert space(RKHS)를 이용한 확장과 다변량으로의 확장이 이어졌다.

"Kernel method for nonlinear Granger causality"(2008)의 생각은 이렇다.

- 커널 함수로 입력을 고차원 feature 공간에 사상한다. 원래 공간에서 비선형이던 관계가 이 공간에서는 선형으로 나타난다. Gaussian RBF 커널이나 다항 커널을 쓴다.
- 그 공간에서 선형 Granger causality를 수행한다. RKHS에서는 함수가 내적으로 표현되고 커널 함수 $K(x, x')$가 바로 그 공간에서의 내적이므로, 고차원 사상을 직접 계산하지 않아도 된다.
- 모델이 유연해진 만큼 과적합이 문제가 된다. 논문은 RKHS의 기하학, 곧 벡터 사이의 거리, 직교성, 사영 같은 개념을 이용해 모델의 복잡도를 조절하는 전략을 함께 제시한다.

최근에는 Hilbert–Schmidt independence criterion에 기반한 HSIC-Lasso-GC(2020)가 제안되었다.

## 4. 비선형: 신경망 계열

신경망을 쓰는 Granger 계열 방법은 수가 많아, 서베이 "Causal Discovery from Temporal Data: An Overview and New Perspectives"의 분류를 따라 묶었다.

| 묶음 | 방법 | 핵심 |
|---|---|---|
| Non-uniform embedding | NN-GC (2015), RNN-GC (2018) | 입력 지연 변수를 골라 가며 유의한 원인을 찾는다 |
| Information regularization | MPIR (2019) | 입력에 잡음을 넣고 상호정보량을 정규화 항으로 쓴다 |
| Component-wise 모델 | Neural Granger Causality (2022), eSRU (2020) | 변수마다 신경망을 따로 두고 입력 가중치의 희소성으로 판정한다 |
| Low-rank 근사 | SCGL (2019) | 인접 행렬을 낮은 rank로 근사한다 |
| Self-explaining network | GVAR (2021) | 시점마다 해석 가능한 계수 행렬을 내놓는다 |
| Attention | TCDF (2019) | 변수별 attention CNN의 attention 점수로 판정한다 |
| Recurrent VAE | CR-VAE (2023) | 생성 모델의 학습 과정에 Granger 인과 학습을 넣는다 |
| Inductive 모델링 | InGRA (2020), ACD (2022) | 표본마다 구조가 다른 데이터에서 공통의 모델로 추론한다 |

### Non-uniform embedding

- **NN-GC**: non-uniform embedding(NUE)이라는 변수 선택 기법으로 MLP에서 유의한 Granger 원인을 찾는다.
- **RNN-GC**: MLP 기반의 방법을 RNN 기반으로 바꾼 것이다. 고차원에서 계산 복잡도가 크다.

### Information regularization

**MPIR**(Minimum Predictive Information Regularization)은 예측 변수에 일부러 잡음을 주입해 모델이 핵심 정보에 집중하게 하고, 상호정보량을 정규화로 넣은 손실을 최소화한다. 불안정성과 비효율성이 약점으로 꼽힌다.

### Component-wise 모델

component-wise 틀은 선형 VAR의 일반화로 볼 수 있다. 변수마다 독립적인 신경망을 두고, 모든 변수의 과거가 그 변수의 미래를 어떻게 결정하는지 학습한다.

$$
\mathbf{x}_j^{t} := g_j\big(\mathbf{x}_1^{1:(t-1)}, \ldots, \mathbf{x}_i^{1:(t-1)}, \ldots, \mathbf{x}_d^{1:(t-1)}\big) + u_j^t, \qquad 1 \le j \le d
$$

$g_j$는 정규화된 신경망으로 구현한 연속 함수다. 이 틀에서 Granger 비인과는 다음과 같이 정의된다. $x_i$가 $x_j$의 Granger 원인이 아니라면, 다른 입력은 그대로 두고 $x_i$의 과거만 바꾸었을 때 출력이 달라지지 않아야 한다.

$$
g_j\big(\mathbf{x}_1^{1:(t-1)}, \ldots, \mathbf{x}_i^{1:(t-1)}, \ldots, \mathbf{x}_d^{1:(t-1)}\big)
= g_j\big(\mathbf{x}_1^{1:(t-1)}, \ldots, \mathbf{x}_{i'}^{1:(t-1)}, \ldots, \mathbf{x}_d^{1:(t-1)}\big)
$$

- **Neural Granger Causality**: component-wise MLP(cMLP)와 component-wise LSTM(cLSTM)을 쓴다. 입력층 가중치에 convex group-lasso 벌점을 걸어, 한 변수에서 들어오는 가중치 묶음이 통째로 0이 되도록 유도한다. 묶음이 0이 된 변수는 Granger 원인이 아니라고 읽는다. 시계열 사이의 긴 의존성은 RNN으로 잡거나, MLP에서는 자동 지연 선택으로 잡는다.
- **eSRU**(economy Statistical Recurrent Units): SRU는 다변량 시계열에서 시간 지연이 있는 비선형 의존성을 잡도록 설계된 RNN이다. gating 구조가 없어 기울기 소실과 폭주를 겪지 않고, 짧은 의존성과 긴 의존성을 모두 잡는다. cLSTM과 마찬가지로 입력층 가중치로 Granger 인과 관계를 읽는다. 원래의 SRU는 많은 데이터를 요구하고 데이터가 부족하면 쉽게 과적합되는데, eSRU는 표본 효율을 높인 구조다.

### Low-rank 근사

**SCGL**(Scalable Causal Graph Learning)은 데이터의 비선형성을 단변량 수준과 다변량 수준으로 나누어 따로 모델링한다. 핵심은 변수 수 $d$ 크기의 인접 행렬 전체를 학습하는 것인데, $d$가 크면 행렬이 커져 확장되지 않는다. 그래서 $k < d$인 rank-$k$ 분해로 행렬을 근사한다. low-rank 근사는 인과 발견에서 잡음의 영향을 줄이고, 후속 시계열 분석에서 해석 가능성을 준다.

### Self-explaining network

**GVAR**(generalized vector autoregression)은 self-explaining neural network를 확장해 해석 가능성을 높인다. self-explaining neural network는 깊은 신경망이면서 해석 가능한 형태를 갖는다.

$$
f(\mathbf{x}) = g\big(\theta(\mathbf{x})_1 h(\mathbf{x})_1, \ldots, \theta(\mathbf{x})_k h(\mathbf{x})_k\big)
$$

$h(\mathbf{x})$는 해석 가능한 기저 개념, $\theta(\mathbf{x})$는 각 개념의 중요도를 정하는 가중치, $g(\cdot)$는 출력 함수다. GVAR은 이를 VAR의 모양으로 옮긴다.

$$
\mathbf{x}^{t} = \sum_{l=1}^{\tau} \Psi_{\theta_l}\big(\mathbf{x}^{t-l}\big)\, \mathbf{x}^{t-l} + \mathbf{u}^{t}
$$

$\Psi_{\theta_l}$는 신경망으로 파라미터화한 계수 행렬이다. 입력 $\mathbf{x}^{t-l}$에 따라 계수가 달라지며, $(j, i)$ 성분은 과거 $x_i^{t-l}$가 현재 $x_j^{t}$에 주는 영향의 세기를 나타낸다. 손실은 세 항으로 이루어진다.

$$
\frac{1}{T - \tau} \sum_{t=\tau+1}^{T} \big\lVert \mathbf{x}^t - \hat{\mathbf{x}}^t \big\rVert_2^2

+ \frac{\lambda}{T - \tau} \sum_{t=\tau+1}^{T} R(\Psi_t)
+ \frac{\gamma}{T - \tau - 1} \sum_{t=\tau+1}^{T-1} \big\lVert \Psi_{t+1} - \Psi_t \big\rVert_2^2
$$

첫째 항은 예측 오차, 둘째 항은 인과 관계의 희소성을 유도하는 정규화, 셋째 항은 계수 행렬이 시간에 따라 부드럽게 변하도록 하는 벌점이다.

각 시점의 계수 행렬을 얻을 수 있으므로 변수 사이의 영향을 직접 읽을 수 있고, 시간에 따라 변하는 인과성(time-varying causality)을 분석할 수 있다. 계산도 효율적이다. 기존의 신경망 기반 Granger 방법은 변수 수 $d$만큼의 신경망을 학습하지만, GVAR은 최대 지연 $\tau$에 대해 $2\tau$개의 신경망만 학습한다.

### Attention

**TCDF**(Temporal Causal Discovery Framework)는 attention 기반 dilated CNN을 쓴다. 변수 수만큼의 독립적인 CNN이 있고, 각각이 변수 하나를 타깃으로 예측한다. 타깃마다 예측값, attention 점수, 커널 가중치가 나온다. $X_j$를 예측할 때 $X_i$에 대한 attention 점수가 높다는 것은 $X_i$가 $X_j$에 대한 예측 정보를 준다는 뜻으로 해석한다.

TCDF는 자기 자신에 대한 인과(self-causation)와 지연 효과를 찾을 수 있다. 또 양방향 인과가 동시점에 나타날 수 없다는 가정 아래에서 숨은 교란 변수의 존재를 찾아낼 수 있다.

### Recurrent VAE

**CR-VAE**(Causal Recurrent Variational Autoencoder)는 생성 모델이 데이터 생성 과정 안에 Granger 인과 학습을 포함하도록 한다. 처음에는 이것이 데이터 생성 모델인지 인과 발견 모델인지 헷갈렸는데, 둘 다였다. 어떤 변수가 어떤 변수의 영향을 받는지를 인과 행렬로 학습하고, 그 구조를 반영해 데이터를 생성한다.

- 미래의 정보가 디코딩 전에 들어오지 못하게 막아, 인코딩이 Granger causality의 규칙을 따르게 한다. 원인이 결과보다 앞선다는 temporal priority에 해당한다.
- error-compensation 모듈로 동시점 효과를 잡는다. 기존의 Granger causality가 다루지 못하는 부분이다.
- 학습된 인과 행렬 덕분에 데이터 생성 과정을 투명하게 볼 수 있다.

### Inductive 모델링

- **InGRA**(Inductive Granger Causal Modeling): Granger attention으로 각 변수가 예측에 기여하는 정도를 재고, 이를 prototype learning과 결합한다. Granger attention만으로는 충분히 강건하지 않기 때문에, 전형적인 사례(prototype)와의 유사도를 계산해 여러 표본에 공통인 인과 구조를 찾는다. 구조가 서로 다른 개체가 섞인 다변량 시계열을 대상으로 한다.
- **ACD**(Amortized Causal Discovery): 인과 구조가 서로 다른 여러 표본에 대해 Granger 인과 관계를 추론하도록 하나의 모델을 학습한다. 표본마다 구조는 달라도 dynamics는 비슷하다고 가정한다. 인코더는 입력 표본의 인과 관계를 추론하고, 디코더는 그 관계가 주어졌을 때 다음 시점을 예측한다. 처음 보는 표본의 인과 관계도 모델을 다시 적합하지 않고 추론할 수 있다.

## 5. 방법별 특성

같은 서베이의 표에서 Granger 계열에 해당하는 부분을 옮기면 다음과 같다. 모든 방법이 비선형 관계를 다루며, 서베이는 이들 방법이 인과 충분성, Markov, faithfulness, minimality 가정을 명시적으로 두지 않는 것으로 분류한다.

| 방법 | 출력 그래프 | Instantaneous effect | Hidden confounder |
|---|---|---|---|
| HSIC-Lasso-GC (2020) | Summary | 아니오 | 아니오 |
| (R)NN-GC (2015, 2018) | Summary | 예 | 아니오 |
| MPIR (2019) | Summary | 아니오 | 아니오 |
| NGC (2022) | Summary | 아니오 | 아니오 |
| eSRU (2020) | Summary | 아니오 | 아니오 |
| SCGL (2019) | Summary | 아니오 | 아니오 |
| GVAR (2021) | Summary | 아니오 | 아니오 |
| TCDF (2019) | Window | 예 | 예 |
| CR-VAE (2023) | Summary | 예 | 아니오 |
| InGRA (2020) | Summary | 아니오 | 아니오 |
| ACD (2022) | Summary | 아니오 | 예 |

표에서 두 가지를 읽었다. 첫째, 대부분의 방법이 시계열 하나를 노드 하나로 보는 summary graph를 출력한다. 어느 지연에서 영향이 있는지까지 알려 주는 것은 TCDF뿐이다. 둘째, 동시점 효과와 숨은 교란 변수를 다루는 방법은 소수다. 비선형성은 신경망으로 해결되었지만, Granger causality의 정의에서 오는 나머지 한계는 대부분 그대로 남아 있다.

## 6. 그 이후의 방법

서베이 이후에 나온 것으로 확인한 방법들이다.

- **CUTS+** (AAAI 2024): CUTS의 확장이다. coarse-to-fine 방식으로 처음에는 변수 그룹 단위로 인과 관계를 학습하고 점차 세분화해 정밀도를 높인다. message-passing 기반의 그래프 신경망을 쓰고, 누락된 데이터를 채우는 일과 인과 그래프 학습을 함께 한다.
- **TSCI** (NeurIPS 2024): Granger causality의 한계를 지적하고 다른 원리의 방법을 제안한다. Granger 기반으로 분류하기는 어렵다.
- **Attention-Driven Causal Discovery**: 비정상 다변량 시계열을 위한 2단계 방법이다. 변형한 Transformer(TAFNet)로 예측을 학습해 attention 행렬을 얻고, 그 행렬을 반복 절차에 넣어 Granger 인과 관계를 찾는다.
- **NeuroKoopman Dynamic Causal Discovery**: 목록에만 올려 두었다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. Graphical Granger 논문의 feature causal network는 summary graph와 어떻게 다른가.**

구조만 놓고 보면 같은 대상이다. 논문은 feature causal network를 feature를 노드로 하는 방향 그래프로 정의하고 각 간선에 지연을 나타내는 자연수를 붙인다. 간선 $x \to y$의 지연이 $k$라는 것은 시점을 펼친 그래프에 $x_{T-k} \to y_T$가 있다는 뜻이다. 그런 다음 학습의 목표를 지연 표시를 뺀 그래프 구조로 한정한다. 이는 "어느 지연에서든 영향이 있으면 간선을 그린다"는 summary graph의 정의와 같다. 차이는 알고리즘이 내놓는 그래프의 세부에 있다. 과거 값($t-T, \ldots, t-1$)만 설명 변수로 쓰므로 동시점 관계는 다루지 않는다. 서로 다른 feature의 쌍만 판정하므로 자기 자신으로의 간선이 없다. 양쪽 방향이 모두 판정되면 방향 간선 두 개가 아니라 방향 없는 간선 하나로 둔다. 지연이 중요한 문제에서는 두 그래프 모두 그 정보를 버린 것이므로, window causal graph를 출력하는 방법을 써야 한다.

**Q. GVAR의 희소성 정규화 $R(\Psi_t)$는 L1 벌점인가.**

L1만은 아니다. 논문은 $R(\cdot)$을 계수 행렬의 norm에 대한 적절한 벌점이라고만 정의하고, 선형 VAR에서 쓰이는 여러 벌점을 가져다 쓸 수 있다고 한다. 구현에서는 L1과 L2를 섞은 elastic-net 형태를 쓴다.

$$
R(\Psi_t) = \alpha \lVert \Psi_t \rVert_1 + (1 - \alpha) \lVert \Psi_t \rVert_2^2, \qquad \alpha = 0.5
$$

**Q. CausalFormer 논문의 지적은 입력 가중치나 attention 점수로 판정하는 방법들에 얼마나 해당하는가.**

방법에 따라 다르다. 논문의 지적은 기존 방법이 attention 가중치나 convolution 가중치처럼 모델 일부 구성 요소의 파라미터만 인과로 대응시키고, fully connected layer나 activation 함수 같은 나머지 구성 요소를 살피지 않는다는 것이다. attention 점수로 후보를 고르는 TCDF에는 이 지적이 그대로 해당한다. attention 점수는 그 뒤에 오는 층의 연산을 반영하지 않는다. TCDF는 attention으로 고른 후보를, 그 입력의 값을 섞었을 때 예측 손실이 커지는지로 한 번 더 검증한다. 반면 cMLP, cLSTM, eSRU처럼 입력층 가중치 묶음이 통째로 0인지를 보는 방법은 사정이 다르다. 어떤 변수에서 들어오는 첫 층의 가중치가 모두 0이면 뒤의 층이 무엇이든 출력은 그 변수에 의존하지 않으므로, "원인이 아니다"라는 판정은 학습된 모델에 대해서는 정확하다. 이 방법들에 남는 문제는 0이 아닌 가중치의 크기가 곧 영향의 세기는 아니라는 점과, 어떤 묶음이 0이 되는지가 벌점의 세기에 좌우된다는 점이다.

### 아직 남은 질문

- 여기서 다룬 방법들이 동시점 효과, feedback, 교란 변수를 실제 데이터에서 얼마나 잡아내는지는 표의 분류만으로 알 수 없다. 세 요소를 구분해 평가할 수 있는 정답 그래프가 있는 데이터에서 직접 돌려 보아야 한다.

## 참고 자료

- C. K. Assaad, E. Devijver, E. Gaussier, "Survey and Evaluation of Causal Discovery Methods for Time Series". (Granger causality의 정의와 한계)
- C. Gong 외, "Causal Discovery from Temporal Data: An Overview and New Perspectives". (신경망 계열의 분류와 특성 표)
- A. Arnold, Y. Liu, N. Abe, "Temporal Causal Modeling with Graphical Granger Methods". <https://andrewoarnold.com/frp781-arnold.pdf>
- "Discovering graphical Granger causality using the truncating lasso penalty", Bioinformatics, 2010.
- "Granger causality testing in high dimensional VARs: A post-double-selection procedure", 2021.
- "Kernel method for nonlinear Granger causality", 2008.
- "A novel Granger causality method based on HSIC-Lasso for revealing nonlinear relationship between multivariate time series", Knowledge-Based Systems, 2020.
- "Neural networks with non-uniform embedding and explicit validation phase to assess Granger causality", Neural Networks, 2015.
- "Estimating Brain Connectivity With Varying-Length Time Lags Using a Recurrent Neural Network", IEEE Transactions on Biomedical Engineering, 2018.
- "Discovering Nonlinear Relations with Minimum Predictive Information Regularization", ICML Time Series Workshop, 2019.
- "Neural Granger Causality", 2022.
- "Economy Statistical Recurrent Units For Inferring Nonlinear Granger Causality", ICLR 2020.
- "Scalable Causal Graph Learning through a Deep Neural Network", CIKM 2019.
- "Interpretable Models for Granger Causality Using Self-explaining Neural Networks", ICLR 2021.
- "Causal Discovery with Attention-Based Convolutional Neural Networks", Machine Learning and Knowledge Extraction, 2019.
- "Causal Recurrent Variational Autoencoder for Medical Time Series Generation", AAAI 2023.
- "Inductive Granger Causal Modeling for Multivariate Time Series", ICDM 2020.
- "Amortized Causal Discovery: Learning to Infer Causal Graphs from Time-Series Data", CLeaR 2022.
- "CUTS+", AAAI 2024.
- "Tangent Space Causal Inference" (TSCI), NeurIPS 2024.
- "Attention-Driven Causal Discovery: From Transformer Matrices to Granger Causal Graphs for Non-Stationary Time-series Data". <https://ieeexplore.ieee.org/abstract/document/10889219>
- "NeuroKoopman Dynamic Causal Discovery". <https://arxiv.org/abs/2404.16326>
- "CausalFormer: An Interpretable Transformer for Temporal Causal Discovery", 2024.
