---
title: "시계열 인과 발견 문헌은 instantaneous effect, feedback, confounder를 어떻게 정의하는가"
date: 2025-04-22
last_modified_at: 2025-05-01
category: study
tags: [인과 발견, 시계열, PCMCI, Granger causality, 문헌 조사]
---

# 시계열 인과 발견 문헌은 instantaneous effect, feedback, confounder를 어떻게 정의하는가

시계열 인과 발견 논문을 읽다 보면 instantaneous effect, feedback loop, confounder라는 세 단어가 거의 빠지지 않고 나온다. 그런데 막상 "정확히 무엇을 가리키는가"를 수식으로 적으려고 하면 논문마다 설명 방식이 달라 막힌다. 그래서 서베이와 개별 논문 45편가량을 모아 세 개념을 각각 어떻게 정의하거나 가정하는지 표로 정리해 보았다. 이 글은 그 조사의 기록이다. 먼저 시계열 인과 발견의 접근법을 분류하고, 세 개념을 차례로 살핀 뒤 조사에서 얻은 결론을 적는다.

## 1. 시계열에서 인과 발견이 어려운 이유

PC, GES, LiNGAM, NOTEARS 같은 대표적인 인과 발견 알고리즘은 대부분 시간 순서가 없는 i.i.d. 데이터를 가정한다. 병원 기록, 기업 재무제표, 설문 응답처럼 표본 하나하나가 독립인 표 형태의 데이터다. 시계열은 사정이 다르다. 주가, 기온, 뇌파, 월별 경제 지표는 값이 서로 영향을 주고(non-i.i.d.), 분포가 시간에 따라 달라질 수도 있다(non-stationary).

시계열에서 추가로 생기는 어려움은 다음과 같다.

- **시간 지연(lag)**: 영향이 있는지뿐 아니라 언제 영향을 주는지를 찾아야 한다.
- **자기상관**: 자기상관만으로도 상호정보량이나 전이 엔트로피가 크게 나올 수 있어 거짓 양성이 생긴다. PCMCI는 초기 단계에서 후보를 가지치기하고, 조건부 독립성 검정의 조건 집합에 자기상관 성분을 넣어 이를 줄인다.
- **feedback**: 서로 영향을 주고받는 구조는 비순환 그래프라는 가정과 충돌할 수 있다.
- **잠재 교란 변수**: 관측되지 않은 공통 원인이 시간에 걸쳐 영향을 준다.
- **고차원**: 변수 수에 시간 지연의 수가 곱해져 탐색 공간과 계산량이 급격히 커진다.

## 2. 접근법의 분류

### Granger causality와 인과 발견

두 용어는 섞여 쓰이지만 출발점이 다르다.

| | Granger causality | 인과 발견 |
|---|---|---|
| 목표 | 시계열에서 한 변수가 다른 변수의 예측에 도움이 되는지 판단 | 관측 데이터의 분포로부터 인과 그래프를 복원 |
| 방법 | 회귀 기반 예측력 비교(보통 VAR) | 조건부 독립성 검정, 구조 학습, 최적화 |
| 지표 | F 통계량, AIC/BIC, p-value | 조건부 독립성, 점수(BIC/MDL), 경사 기반 목적 함수 |
| 결과 | 변수 쌍 사이의 방향 | 그래프 전체 또는 일부 |

Granger causality는 시계열 제약 기반 방법의 출발점으로 소개되기도 한다. 이후의 연구는 비선형 관계, 동시점 관계, 잠재 교란 변수, 강한 자기상관을 다루는 방향으로 확장되었다.

### 서베이의 분류

Assaad 등의 서베이 "Survey and Evaluation of Causal Discovery Methods for Time Series"는 방법을 다음과 같이 나눈다. 서베이의 결론은 특별히 앞서 나가는 방식은 없다는 것이다. 방법마다 특정한 가정에 의존하므로 데이터에 따라 적합한 방법이 달라진다.

| 분류 | 생각 | 대표 방법 |
|---|---|---|
| Granger causality | 과거 값이 미래 값의 예측에 통계적으로 유의한 정보를 주는가 | pairwise, multivariate(conditional) Granger |
| 제약 기반 | 조건부 독립성 검정으로 뼈대를 만들고 규칙으로 방향을 정한다 | oCSE, PCMCI, PCMCI+ |
| 잡음 기반(noise-based) | 잡음을 방해 요소가 아니라 방향을 판단하는 정보원으로 쓴다 | VarLiNGAM, TiMINo |
| 점수 기반 | 데이터의 가능도와 벌점 항의 균형을 나타내는 점수로 그래프를 찾는다 | DYNOTEARS |
| 그 밖 | logic-based, topology-based, difference-based | |

여기에 최근 논문을 더해 다음과 같이 정리했다.

| 분류 | 방법 |
|---|---|
| 제약 기반 | PCMCI, PCMCI+, LPCMCI, J-PCMCI+, PCMCI$\Omega$ |
| 점수 기반 | DYNOTEARS, NTiCD(2023), RealTCD(2024), SPACETIME(2025) |
| 함수적 인과 모델 기반 | VarLiNGAM, TiMINo, NBCB(2024, 제약 기반과 잡음 기반의 결합) |
| 딥러닝 기반 | TCDF(2019), CUTS(2023), STIC(2024), MCD(2024), CausalFormer(2024), TS-CausalNN(2024) |

딥러닝 기반 방법은 주로 비선형성에 대한 한계를 넘기 위해 도입되고 있다.

### 방법별 메모

- **oCSE**: causation entropy로 조건부 독립성을 찾는다. 모든 인과 관계의 시간 지연이 1이라고 가정하는데, 이 가정이 항상 맞지는 않는다.
- **PCMCI**: 대규모 시계열에서 지연된(lagged) 인과 관계를 window causal graph 형태로 찾는다. 두 단계로 이루어진다. 첫 단계(PC1)는 PC 알고리즘의 뼈대 탐색을 적용해 각 변수의 부모 후보를 고르고, 둘째 단계(MCI, momentary conditional independence)는 그 후보를 조건으로 삼아 자기상관 때문에 생긴 잘못된 간선을 걸러 낸다. 선형 검정이든 비선형 검정이든 어떤 조건부 독립성 검정과도 결합할 수 있다. 동시점 관계는 다루지 못한다.
- **PCMCI+**: PCMCI의 확장으로, 인과 충분성 아래에서 동시점 관계와 지연 관계를 모두 허용한다. 뼈대 탐색을 지연 관계를 찾는 단계와 동시점 관계를 찾는 단계로 나눈다.
- **LPCMCI**: 또 다른 확장으로, FCI와 비슷한 방식으로 잠재 변수를 반영한다. 선형과 비선형, 지연과 동시점 관계를 잠재 교란 변수가 있는 상황에서 다룬다.
- **DYNOTEARS**: 인과 충분성과 선형성 가정 아래에서 동시점 관계와 지연 관계를 함께 추정한다. 비순환 제약을 매끄러운 등식 제약으로 바꾸어 벌점이 붙은 손실을 최소화한다.
- **VarLiNGAM**: 자기회귀 모델에 비가우시안 동시점 모델을 결합한다. 가우시안 선형 모델은 식별 가능성 문제가 있는데, 비가우시안 잡음을 가정하면 구조에 대한 사전 지식 없이 식별할 수 있음을 보였다.
- **TCDF**: attention 기반 dilated CNN으로 인과 관계와 지연을 찾는다. 딥러닝을 인과 발견에 적용한 사례로 많이 인용된다.
- **CausalFormer**: 학습된 모델의 attention 가중치나 convolution 가중치만 보고 인과를 읽는 것은 불완전하다는 문제의식에서 출발한다. 예측 과제로 인과 표현을 학습하는 causality-aware transformer와, 그 모델의 전체 구조를 해석해 인과 관계를 찾는 decomposition-based detector로 구성된다.
- **CD-NOTS**: 조건부 독립성 검정 기반 방법으로 CD-NOD를 개선했다. 비모수적이며 비정상성과 비선형성을 다루고, 동시점 인과 효과도 잡는다. PCMCI는 비선형성은 다룰 수 있지만 비정상성은 다루지 못한다.
- **F-PCMCI**: 전이 엔트로피로 변수를 먼저 걸러 낸 뒤, 남은 변수에 PCMCI를 적용한다.

## 3. Instantaneous effect

### 그래프로 정의하는 방식

가장 흔한 정의는 그래프에서 같은 시점의 두 변수를 잇는 간선이다. contemporaneous effect라고도 부른다.

$$
x_i^{t} \to x_j^{t} \quad \text{(instantaneous)}, \qquad x_i^{t-k} \to x_j^{t} \quad \text{(lagged)}
$$

시간 지연이 0이면 동시점 관계, 0보다 크면 지연 관계라고 설명하는 논문도 같은 정의다. 반대로 이 관계가 없다고 가정하는 논문도 많다. "같은 시점의 변수 사이에는 간선이 없다", "모든 간선에서 $t < t'$이다"처럼 한 문장으로 적는다.

### 왜 생기는가

원인은 결과보다 앞선다(temporal priority). 그렇다면 같은 시점의 인과 관계는 왜 생기는가. 조사한 문헌의 설명은 대체로 일치한다. 실제로는 서로 다른 시각에 일어난 원인과 결과가, 데이터의 시간 해상도가 충분하지 않아 같은 시점에 일어난 것처럼 보이는 것이다. 예를 들어 실제 지연은 1주인데 데이터가 월 단위로 집계되어 있으면 그 관계는 같은 달 안에서 나타난다. TCDF 논문도 instantaneous effect를 원인과 결과 사이의 지연이 0인 경우로 정의하면서, 시간 단위가 너무 거칠어 원인과 결과의 순서를 미리 정할 수 없을 때 주로 생긴다고 설명한다. 이를 무시하면 잘못된 해석으로 이어질 수 있다.

관련해서, 측정 주기가 실제 인과 주기보다 긴 subsampling 상황에서도 원래 주기의 인과 관계를 복원할 수 있다는 결과가 있다. 분기 데이터에서 월 단위의 인과 관계를 복원하는 식이다.

### 수식으로 정의하는 방식

**Granger(1969).** 예측 오차의 분산으로 정의한다. $\bar{U}$를 과거의 모든 정보, $\bar{\bar{Y}}$를 $Y$의 과거와 현재 값이라고 할 때,

$$
\sigma^2\big(X \mid \bar{U}, \bar{\bar{Y}}\big) < \sigma^2\big(X \mid \bar{U}\big)
$$

이면 instantaneous causality가 있다고 한다. $Y$의 현재 값을 넣었을 때 $X$의 현재 값을 더 잘 예측한다는 뜻이다.

**VAR의 확장.** VAR 모델을 적합하고 나면 잔차가 같은 시점에서 서로 의존하는 경우가 많다. 이를 설명하려고 동시점 효과를 나타내는 행렬 $\mathbf{B}_0$를 넣은 모델을 쓴다.

$$
\mathbf{X}_t = \mathbf{B}_0 \mathbf{X}_t + \mathbf{A} \mathbf{X}_{t-1} + \boldsymbol{\varepsilon}_t
\;\;\Longrightarrow\;\;
\mathbf{X}_t = (\mathbf{I} - \mathbf{B}_0)^{-1} \mathbf{A} \mathbf{X}_{t-1} + (\mathbf{I} - \mathbf{B}_0)^{-1} \boldsymbol{\varepsilon}_t
$$

**SCM.** 시계열 구조적 인과 모델에서는 각 변수가 자신의 인과적 부모와 잡음의 함수로 정해진다.

$$
V_t^j := f^j\big(pa(V_t^j),\, \eta_t^j\big), \qquad pa(V_t^j) \subseteq \{V_t, V_{t-1}, \ldots, V_{t-\tau_{\max}}\} \setminus \{V_t^j\}
$$

부모 집합에 현재 시점 $V_t$가 들어갈 수 있다는 것, 즉 $\tau_{\max} \ge 0$에서 지연 0을 허용한다는 것이 곧 동시점 효과를 허용한다는 뜻이다. SCM으로 설명하는 논문도 결국 그래프로 설명하는 논문과 같은 내용을 말하지만, 수식으로 가장 정리되어 있는 것은 이 방식이었다.

### 어떤 방법이 다루는가

Granger causality는 원인이 항상 과거에 있어야 한다는 전제 때문에 동시점 관계를 다루지 못한다. PCMCI도 처음에는 다루지 못했고 PCMCI+에서 가능해졌다. Granger 계열을 쓰는 딥러닝 방법(ACD, CUTS 등)은 잠재 교란 변수와 동시점 효과가 없다고 가정하는 경우가 많다. 이 두 가정이 성립해야 Granger 인과 관계가 SCM의 인과 관계와 일치하기 때문이다.

## 4. Feedback loop

### 정의를 찾기 어려웠다

세 개념 중 정의를 가장 찾기 어려웠다. 조사한 논문 대부분은 feedback을 언급하지 않거나, 언급하더라도 정의 없이 쓴다. "비순환 그래프로는 feedback이 많은 시스템을 다룰 수 없다"는 문제 제기나 "SCM은 feedback이 있는 시스템을 모델링하는 데 널리 쓰인다"는 서술 정도가 대부분이다.

명시적인 정의는 다시 Granger(1969)에 있었다. $\overline{U - Y}$를 $Y$를 뺀 나머지 정보의 과거라고 할 때

$$
\sigma^2\big(X \mid \bar{U}\big) < \sigma^2\big(X \mid \overline{U - Y}\big), \qquad
\sigma^2\big(Y \mid \bar{U}\big) < \sigma^2\big(Y \mid \overline{U - X}\big)
$$

가 모두 성립하면 feedback이 있다고 하고 $Y_t \Leftrightarrow X_t$로 쓴다. $X$가 $Y$의 원인이면서 $Y$도 $X$의 원인인 경우다.

### 지연된 feedback과 동시점 cycle

읽으면서 가장 헷갈린 것은 "DAG는 cycle을 허용하지 않는데 시계열에서 feedback은 어떻게 표현되는가"였다. Runge 등의 리뷰 "Causal inference for time series"를 읽고 다음과 같이 정리했다. 시계열 그래프의 노드는 변수가 아니라 (변수, 시점) 쌍이다.

| 구조 | DAG에서 |
|---|---|
| $X_t \to Y_t$ 와 $Y_t \to X_t$ | 불가능하다. 동시점 cycle이므로 비순환성을 위반한다 |
| $X_t \leftrightarrow Y_t$ | 불가능하다. 양방향 간선은 DAG에 없다(혼합 그래프에서는 교란을 나타내는 데 쓴다) |
| $X_{t-1} \to Y_t$ 와 $Y_{t-1} \to X_t$ | 가능하다 |
| $X_{t-1} \to Y_t$ 와 $Y_t \to X_t$ | 가능하다. 둘 중 하나가 지연 관계이므로 cycle이 아니다 |

즉 feedback은 두 종류로 나뉜다. 지연을 사이에 둔 feedback은 시간 축으로 펼친 그래프에서 cycle이 아니므로 DAG로 표현된다. $X_{t-3} \to Y_{t-2} \to Z_{t-1} \to X_t$처럼 여러 변수를 거쳐 돌아오는 구조도 마찬가지로 지연된 feedback이다. 금지되는 것은 같은 시점 안에서 닫히는 동시점 cycle뿐이다.

"cycle은 summary graph에서만 가능하다"는 문장도 같은 맥락에서 이해했다. 시점을 접어 시계열 하나를 노드 하나로 나타내는 summary graph에서는 $X_{t-1} \to Y_t$와 $Y_{t-1} \to X_t$가 $X \to Y$와 $Y \to X$가 되어 cycle로 보인다. 시간 축으로 펼친 그래프에서는 cycle이 아니다.

동시점 cycle에 대해서는, PC 알고리즘이 cycle이 있어도 일관성을 가진다는 결과를 근거로 비순환 SCM에 대한 방법이 동시점 cycle이 있는 경우로 확장될 것이라고 기대하는 논문이 있었다. 조건부 독립성 검정 결과와 배경지식을 SAT solver로 결합해 cycle과 잠재 변수를 함께 찾는 접근, LiNGAM의 비순환 가정을 약화한 방법도 서베이에 소개되어 있다.

### Self-loop

자기 자신의 과거가 현재에 영향을 주는 관계($X_{t-k} \to X_t$)를 self-loop 또는 self-causation이라고 부르는 논문들이 있다. TCDF 논문은 self-causation을 feedback loop와 구분해서 쓴다. 두 개념을 같은 것으로 읽으면 안 된다는 점을 여기서 알았다.

## 5. Confounder

### 정의

confounder는 두 변수 이상의 공통 원인이다. 공통 원인이 관측되지 않으면, 직접적인 인과 관계가 없는 두 변수 사이에 가짜 의존성(spurious dependency)이 생긴다. 세 개념 중 언급은 가장 많았지만, 거의 모든 논문이 수식이 아니라 그림으로 정의한다. 공통 원인 $Z$에서 두 변수로 화살표가 나가는 그림이다.

가정으로는 인과 충분성(causal sufficiency)이라는 이름으로 등장한다. 관측된 변수들의 모든 공통 원인이 관측되어 있다는 가정이다. 대부분의 인과 발견 방법이 이 가정을 두므로 관측되지 않은 confounder를 다루지 못한다. 여러 서베이가 실제 데이터에는 숨은 confounder가 흔하다는 점을 한계로 지적한다.

### 수식으로 표현하는 방식

**Granger.** confounder라는 단어는 쓰지 않지만 같은 현상을 설명한다. 분석에 쓰는 정보 집합에 관련된 시계열이 빠지면 spurious causality가 생길 수 있다. $X_t$와 $Y_t$만 보았는데 실제로는 둘 모두의 원인인 $Z_t$가 있었다면, 원래 집합에서는 $X_t$와 $Y_t$ 사이에 인과가 있는 것처럼 나타난다.

**VAR.** 관측되는 과정 $\mathbf{X}_t$와 관측되지 않는 과정 $\mathbf{Z}_t$가 함께 하나의 VAR 과정을 이룬다고 쓴다.

$$
\begin{bmatrix} \mathbf{X}_t \\ \mathbf{Z}_t \end{bmatrix} =
\begin{bmatrix} \mathbf{B} & \mathbf{C} \\ \mathbf{D} & \mathbf{E} \end{bmatrix}
\begin{bmatrix} \mathbf{X}_{t-1} \\ \mathbf{Z}_{t-1} \end{bmatrix} + \boldsymbol{\varepsilon}_t
$$

$\mathbf{B}$는 관측된 과정의 인과 전이 행렬, $\mathbf{C}$는 $\mathbf{Z}_t$가 $\mathbf{X}_t$에 주는 영향이다. 이론적인 문제는 $\mathbf{X}_t$만 관측하고도 $\mathbf{B}$와 $\mathbf{C}$를 식별할 수 있느냐다.

**SCM의 잡음.** 관측되지 않은 confounder가 없는 SCM은 잡음 항이 서로 독립이며 Markovian model이라고 부른다. confounder가 있으면 관측 변수의 잡음 항이 서로 의존할 수 있고, 이를 semi-Markovian model이라고 부른다. 처음에는 "잡음의 의존성"이 무슨 뜻인지 이해하지 못했는데, confounding이 있는지 없는지를 수식으로 표현한 것이었다. $\mathrm{Cov}(\eta_t^i, \eta_t^j) \ne 0$이면 $V^i$와 $V^j$ 사이에서 관측된 상관이 confounding 때문일 수 있고, 이를 그래프에서 $V^i \leftrightarrow V^j$로 표시한다.

### 양방향 간선은 왜 confounder로 읽는가

잠재 변수를 허용하는 그래프(MAG)에서 양방향 간선 $\leftrightarrow$는 관측되지 않은 confounder를 뜻한다. 여기서 의문이 생겼다. 정말로 두 변수가 서로 영향을 주는 것일 수도 있는데 왜 confounder라고 단정하는가.

정리한 답은 다음과 같다.

1. $V^i_{t-\tau} \leftrightarrow V^j_t$처럼 시점이 다른 경우에는 미래가 과거에 영향을 줄 수 없으므로 양방향 인과가 불가능하다. 남는 해석은 공통의 미관측 원인이다.
2. 같은 시점에서는 두 변수가 실제로 동시에 상호작용할 가능성이 있다. 그러나 동시에 일어난 양방향 인과는 데이터만으로 순서를 식별할 수 없고, 비순환성 가정과도 충돌한다. 그래서 대부분의 틀은 이를 잠재 confounder에 의한 상관으로 처리한다.
3. PCMCI+나 동시점 항이 있는 VAR 모델은 동시점 효과를 명시적으로 모델에 넣어 두 가능성을 구분하려고 한다.

### 지연이 다른 confounder

TCDF 논문이 지적한 경우가 인상적이었다. confounder가 두 변수에 서로 다른 지연으로 영향을 주면, 먼저 영향을 받은 변수가 나중에 영향을 받은 변수의 원인인 것처럼 보인다. 관측되지 않은 confounder가 몇 개인지조차 모르는 상황이 많으므로, 방법이 숨은 confounder의 존재를 가설로 세울 수 있어야 그 결과들 사이에 잘못된 인과 관계를 학습하지 않는다.

subsampling을 다룬 논문("Causal Discovery from Subsampled Time Series with Proxy Variables")의 그림도 같은 현상을 잘 보여 준다. 두 변수 모두에 영향을 준 중간 시점의 값이 관측되지 않으면, 그 둘 사이에 양방향 간선이 생긴다.

## 6. 조사 결과

문헌별로 세 개념을 다루는 방식을 요약하면 다음과 같다. 빈칸은 해당 개념을 다루지 않거나 정의 없이 언급만 한 경우다.

| 문헌 | Instantaneous | Feedback | Confounder |
|---|---|---|---|
| Granger (1969) | 예측 오차 분산으로 정의 | 예측 오차 분산으로 정의 | spurious causality로 설명 |
| Assaad 등 서베이 | 시간 해상도 때문에 동시로 보이는 관계 | | 그림, 인과 충분성, MAG의 $\leftrightarrow$ |
| Gong 등 서베이 | $x_i^t \to x_j^t$ | | |
| Zanga 등 서베이 | 같은 시점의 두 변수 사이의 연결 | cyclic 모델(LiNG) 소개 | hidden confounder 언급 |
| Spirtes, Zhang 리뷰 | VAR에 $\mathbf{B}_0$ 추가 | SAT 기반 방법 소개 | 관측되지 않는 $\mathbf{Z}_t$를 포함한 VAR |
| Runge 등 리뷰 (2023) | SCM의 지연 0 부모 | 동시점 cycle만 금지 | 잡음의 의존성, $\leftrightarrow$ |
| PCMCI+ 관련 논문 | 동시점 간선 허용 | 비순환 SCM 가정 | 인과 충분성 가정 |
| LPCMCI | 동시점 간선 허용 | | 잠재 confounder 허용 |
| ACD, CUTS | 없다고 가정 | | 없다고 가정 |
| TCDF | 지연 0인 인과 | self-causation과 구분 | 공통 원인, 그림 |
| Subsampled + proxy | MAG의 같은 시점 간선 | | MAG의 $\leftrightarrow$ |
| 비정상 시계열 (CD-NOTS, semi-stationary 등) | 지연 0 간선의 허용 여부를 한 문장으로 명시 | | 인과 충분성 가정 |
| Cyclic 모델 논문들 | | 비순환 가정의 한계로 제기 | 둘 이상의 관측 변수의 숨은 공통 원인 |

조사에서 얻은 결론은 세 가지다.

1. **instantaneous effect**는 대체로 "그래프에서 같은 시점의 두 노드를 잇는 간선"으로 정의된다. 용어는 contemporaneous와 섞여 쓰인다.
2. **feedback loop**를 제대로 정의한 문헌은 거의 없었다. 지연된 feedback, 동시점 cycle, self-loop가 구분 없이 쓰이는 경우가 많아 읽는 쪽에서 구분해야 한다.
3. **confounder**는 언급은 많지만 대부분 그림으로 정의한다. 수식으로 표현한 것은 SCM의 잡음 의존성이나 잠재 과정을 포함한 VAR 정도다.

완전히 그래프로만 설명하지 않는 한, 방법마다 이 개념들을 설명하는 방식에 조금씩 차이가 생긴다. 세 개념을 일관되게 적으려면 SCM으로 수식을 쓰고 그래프를 함께 보여 주는 것이 가장 낫다고 판단했다. 그런데 그래프로 표현하려면 d-separation, Markov condition, faithfulness, 인과 충분성 같은 가정을 먼저 알아야 했다. 이 내용은 [인과 그래프와 가정](03-causal-graphs-and-assumptions.md)과 [구조적 인과 모델](04-structural-causal-model.md)에 따로 정리했다.

## 7. 최근 학회 논문에서 본 흐름

배경 조사를 겸해 ICML(2022 ~ 2024)과 KDD(2022 ~ 2024)에서 "causal discovery"로 검색되는 논문을 초록 중심으로 훑었다. 시계열 논문은 일부였지만, 분야가 어떤 문제를 풀고 있는지 파악하는 데 도움이 되었다.

| 문제 | 논문 | 내용 |
|---|---|---|
| 조건부 독립성 검정의 비용 | Causal Discovery with Fewer Conditional Independence Tests (ICML 2024) | PC는 검정 횟수가 지수적으로 늘 수 있다. 다항 횟수의 검정으로 배울 수 있는 더 거친 표현(CCPG)을 정의한다 |
| | Scalable and Flexible Causal Discovery with an Efficient Test for Adjacency (ICML 2024) | 수많은 이산적인 검정을 하나의 연속 최적화 문제로 바꾼다(DAT). 변수 1,000개 규모의 그래프를 다룬다 |
| | On the sample complexity of conditional independence testing with Von Mises estimator (ICML 2024) | KDE 기반 엔트로피 추정기로 검정을 만들고, 비선형·비가우시안 연속 변수에서 제약 기반 방법의 표본 복잡도를 유도한다 |
| 잠재 confounder | Causal Discovery via Conditional Independence Testing with Proxy Variables (ICML 2024) | 잠재 confounder가 검정을 왜곡하는 문제를 proxy 변수와 이산화로 다룬다 |
| | Nonlinear Causal Discovery with Latent Confounders (ICML 2023) | 인과 충분성 없이, VAE로 관측 변수와 잠재 confounder의 구조를 함께 복원한다 |
| | From Temporal to Contemporaneous Iterative Causal Discovery in the Presence of Latent Confounders (ICML 2023) | 시계열용. 긴 지연, 짧은 지연, 동시점 순서로 학습해 검정 횟수를 줄인다(TS-ICD) |
| | Causal Discovery with Latent Confounders Based on Higher-Order Cumulants (ICML 2023) | 고차 누적량으로 잠재 변수가 있는 LiNGAM 구조를 학습한다 |
| | Score-Based Causal Discovery of Latent Variable Causal Models (ICML 2024) | 제약 기반 방법의 오차 전파와 검정 순서 의존성을 피하려고 잠재 변수 구조에 대한 점수 함수를 만든다 |
| | Scalable Differentiable Causal Discovery in the Presence of Latent Confounders with Skeleton Posterior (KDD 2024) | 뼈대의 사후 분포를 추정해 MAG 학습의 확장성을 높인다 |
| 연속 최적화의 안정성 | Stable Differentiable Causal Discovery (ICML 2024) | 기존 비순환 제약의 수치적 불안정을 고치고 수천 개 변수까지 학습한다 |
| | Score Matching Enables Causal Discovery of Nonlinear Additive Noise Models (ICML 2022) | score 함수의 Jacobian으로 위상 순서를 추정한다 |
| 개입 데이터의 효율 | Adaptive Online Experimental Design for Causal Discovery (ICML 2024) | 개입 표본이 유한하다고 보고, 어떤 개입을 할지 온라인으로 고른다 |
| | Foundations of Testing for Finite-Sample Causal Discovery (ICML 2024) | 간선 방향 추론을 다중 검정 문제로 보고 언제 멈춰도 유효한 검정을 만든다 |
| 강한 가정과 평가 | Bivariate Causal Discovery using Bayesian Model Selection (ICML 2024) | 식별 가능성을 위해 강한 가정을 두는 대신 베이지안 모델 비교로 방향을 고른다 |
| | Challenges and Considerations in the Evaluation of Bayesian Causal Discovery (ICML 2024) | 표본이 적을 때 기존 평가 지표가 사후 분포의 품질을 잘 반영하지 못함을 보인다 |
| 데이터의 형태 | Causal Discovery on Non-Euclidean Data (KDD 2022) | 함수 데이터 같은 비유클리드 데이터를 위한 인과 모델과 학습법을 제안한다 |

훑어본 뒤의 인상은 이렇다. 많은 논문이 새 원리를 내놓기보다 기존 방법의 실용적 한계, 곧 검정 횟수, 유한 표본, 잠재 변수, 수치 안정성을 하나씩 겨냥한다. 그리고 인과 충분성을 완화하려는 논문이 눈에 띄게 많았다.

이때 논문마다 모르는 개념을 적어 두었는데, 목록이 곧 이후의 공부 순서가 되었다. intervention, SHD, identifiability, causal sufficiency, latent confounder, 조건부 독립성 검정, constraint-based와 score-based의 차이, Meek rule, additive noise model 등이다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. self-loop(자기 자신에 대한 지연 효과)를 self-reinforcing 구조라고 불러도 되는가.**

같은 말로 쓰기는 어렵다. self-loop $X_{t-k} \to X_t$는 과거의 자신이 현재에 영향을 준다는 사실만 나타내고, 그 영향의 부호는 말하지 않는다. $X_t = a X_{t-1} + \varepsilon_t$에서 $a = 0.8$이면 값이 한동안 유지되지만, $a = -0.8$이면 매 시점 부호가 뒤집히며 진동하므로 스스로를 강화한다고 할 수 없다. reinforcing은 변화가 같은 방향의 변화를 다시 키우는 양의 feedback을 가리키는 말이다. 게다가 정상 과정이려면 $\vert a \vert < 1$이어야 하므로, 계수가 양수인 경우에도 한 번의 충격은 커지지 않고 줄어든다. 따라서 self-loop는 self-causation이나 autodependency처럼 부호와 무관한 이름으로 부르는 편이 정확하다.

**Q. PCMCI+는 동시점 cycle까지 허용하는가. cycle을 직접 다루는 방법은 무엇인가.**

허용하지 않는다는 이해가 맞다. PCMCI+ 논문은 시계열 그래프가 비순환이라고 가정하며, 인과 충분성, causal Markov condition, faithfulness, 정상성을 함께 둔다. 따라서 $X_t \to Y_t$와 $Y_t \to X_t$가 함께 있는 구조는 대상이 아니고, feedback은 $X_{t-1} \to Y_t$, $Y_{t-1} \to X_t$처럼 지연을 둔 형태로만 표현된다. 동시점 간선의 방향이 Markov equivalence class 안에서 정해지지 않으면 $\circ\!\!-\!\!\circ$로, 방향 규칙이 서로 충돌하면 $\times\!\!-\!\!\times$로 표시한다. cycle을 직접 다루는 방법은 i.i.d. 데이터를 대상으로 한 것이 중심이다. 조건부 독립성으로 cyclic 그래프의 equivalence class를 찾는 CCD(Richardson, 1996), 선형 비가우시안 모델에서 cycle을 허용한 LiNG(Lacerda 외, 2008), 독립성 제약을 SAT 문제로 풀어 cycle과 잠재 변수를 함께 허용하는 방법(Hyttinen 외, 2013)이 대표적이다.

**Q. 서로 다른 지연으로 영향을 주는 confounder가 관측되지 않을 때, 그로 인한 지연된 가짜 관계를 어떤 방법이 걸러 낼 수 있는가.**

인과 충분성을 가정하지 않는 방법이어야 한다. 잠재 변수 $L$이 $L_{t-2} \to X_{t-1}$, $L_{t-2} \to Y_t$로 작용하면 $X_{t-1}$과 $Y_t$는 어떤 관측 변수를 조건에 넣어도 독립이 되지 않는다. 인과 충분성을 가정하는 방법(Granger causality, PCMCI)은 이 의존성을 설명할 수단이 간선밖에 없으므로 $X_{t-1} \to Y_t$로 보고한다. tsFCI, SVAR-FCI, LPCMCI처럼 PAG를 출력하는 방법은 같은 의존성을 지연된 양방향 간선 $X_{t-1} \leftrightarrow Y_t$로 나타낼 수 있다. 다만 항상 걸러 내는 것은 아니다. 시간 순서로 정해지는 것은 $Y_t$ 쪽의 화살촉뿐이어서 출력은 $X_{t-1} \circ\!\!\to Y_t$에서 출발하고, 다른 변수와의 독립성 패턴이 $X_{t-1}$ 쪽도 화살촉임을 보여 줄 때만 $\leftrightarrow$로 확정된다. 그렇지 않으면 "원인일 수도 있고 교란일 수도 있다"는 표시로 남는다. 자세한 내용은 [잠재 교란이 있을 때의 인과 발견](08-causal-discovery-with-latent-confounders.md)에 정리했다.

**Q. 정상성 가정이 깨질 때 위의 정의들이 그대로 유지되는가.**

정의 자체는 유지되지만, 그것을 유한한 그래프 하나로 요약하는 근거가 사라진다. 동시점 간선, 지연된 feedback, 공통 원인은 모든 시점을 펼친 full time graph에서 정의되므로 정상성과 무관하다. 깨지는 것은 "같은 지연의 관계는 모든 시점에서 같다"는 consistency throughout time이다. 이 가정이 없으면 full time graph를 window graph로 접을 수 없고, 서로 다른 시점의 관측을 같은 관계에서 나온 반복 표본으로 쓸 수도 없다. 그래서 비정상 시계열을 다루는 방법은 간선이나 메커니즘이 시간에 따라 달라진다고 놓는다. 구간(regime)마다 다른 그래프를 추정하거나(Regime-PCMCI), 시간 인덱스를 변화를 일으키는 잠재 요인의 대리 변수로 그래프에 넣거나(CD-NOD 계열), 인과 메커니즘이 주기적으로 반복된다고 가정한다(semi-stationary). 주의할 점은, 모델에 넣지 않은 분포 변화가 여러 변수에 함께 작용하면 그것이 관측되지 않은 공통 원인처럼 작용해 가짜 의존성을 만든다는 것이다.

## 참고 자료

서베이와 리뷰

- C. K. Assaad, E. Devijver, E. Gaussier, "Survey and Evaluation of Causal Discovery Methods for Time Series" (IJCAI 2023 Journal Track에 extended abstract 수록).
- U. Hasan, E. Hossain, M. O. Gani, "A Survey on Causal Discovery Methods for I.I.D. and Time Series Data".
- A. Zanga, E. Ozkirimli, F. Stella, "A Survey on Causal Discovery: Theory and Practice".
- C. Gong 외, "Causal Discovery from Temporal Data: An Overview and New Perspectives".
- P. Spirtes, K. Zhang, "Causal Discovery and Inference: Concepts and Recent Methodological Advances".
- "Causal Inference for Time Series Analysis: Problems, Methods and Evaluation".
- J. Runge 외, "Causal inference for time series".
- "Inferring causation from time series in Earth system sciences".
- "Causal network reconstruction from time series: From theoretical assumptions to practical estimation".
- "Methods and tools for causal discovery and causal inference".

개별 논문

- C. W. J. Granger, "Investigating Causal Relations by Econometric Models and Cross-spectral Methods", 1969.
- "Detecting and quantifying causal associations in large nonlinear time series datasets" (PCMCI), 2019. <https://www.science.org/doi/abs/10.1126/sciadv.aau4996>
- "Discovering contemporaneous and lagged causal relations in autocorrelated nonlinear time series datasets" (PCMCI+).
- "High-recall causal discovery for autocorrelated time series with latent confounders" (LPCMCI), NeurIPS 2020.
- "Causal Discovery for time series from multiple datasets with latent contexts".
- "Causal Discovery with Attention-Based Convolutional Neural Networks" (TCDF), 2019.
- "Amortized Causal Discovery: Learning to Infer Causal Graphs from Time-Series Data" (ACD).
- "CUTS: Neural Causal Discovery from Irregular Time-Series Data".
- "CausalFormer: An Interpretable Transformer for Temporal Causal Discovery", 2024.
- "Causal Discovery from Subsampled Time Series with Proxy Variables".
- "Causal Discovery from Subsampled Time Series Data by Constraint Optimization".
- "Causal Discovery from Temporally Aggregated Time Series".
- "Causal Discovery from Nonstationary Time Series" (CD-NOTS).
- "Causal Discovery in Semi-Stationary Time Series".
- "Causal Discovery-Driven Change Point Detection in Time Series".
- "Causal Discovery from Ecological Time-Series with One Timestamp and Multiple Observations".
- "SPACETIME: Causal Discovery from Non-Stationary Time Series", AAAI 2025.
- "Entropy-based Discovery of Summary Causal Graphs in Time Series".
- "CAnDOIT: Causal Discovery with Observational and Interventional Data from Time Series".
- "Cyclic Causal Discovery from Continuous Equilibrium Data".
- "Feedback System Neural Networks for Inferring Causality in Directed Cyclic Graphs".
- "Causal Feature Selection via Transfer Entropy", 2024.
- T. Richardson, "A Discovery Algorithm for Directed Cyclic Graphs" (CCD), UAI 1996.
- G. Lacerda, P. Spirtes, J. Ramsey, P. O. Hoyer, "Discovering Cyclic Causal Models by Independent Components Analysis" (LiNG), UAI 2008.
- A. Hyttinen, P. O. Hoyer, F. Eberhardt, M. Järvisalo, "Discovering Cyclic Causal Models with Latent Variables: A General SAT-Based Procedure", UAI 2013.

학회 논문 목록

- ICML: [2024](https://icml.cc/virtual/2024/papers.html?search=causal+discovery), [2023](https://icml.cc/virtual/2023/papers.html?search=causal+discovery), [2022](https://icml.cc/virtual/2022/papers.html?search=causal+discovery)
- KDD: [2024](https://kdd2024.kdd.org/research-track-papers/), [2022](https://kdd.org/kdd2022/paperRT.html)
