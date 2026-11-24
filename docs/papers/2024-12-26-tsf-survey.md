---
title: "A Survey of Deep Learning and Foundation Models for Time Series Forecasting 리뷰"
date: 2024-12-26
last_modified_at: 2024-12-30
category: paper
tags: [시계열 예측, 서베이, Transformer, GNN, Foundation Model, 평가 지표]
---

# A Survey of Deep Learning and Foundation Models for Time Series Forecasting 리뷰

작성일 2024-12-26 · 최종 수정일 2024-12-30
{ .post-date }

시계열 예측(Time Series Forecasting, TSF) 분야를 처음 공부하면서 전체 지형을 파악하려고 읽은 서베이 논문이다. 통계 모델에서 딥러닝, Transformer, GNN, foundation model, 지식 활용, 평가 방법까지를 한 편에서 다룬다. 논문 전체가 COVID-19 같은 팬데믹 예측을 예시로 삼아 서술된다는 점이 특징이다.

두 번에 나누어 읽었다. 처음에는 1 ~ 3장을 문단 단위로 따라가며 모르는 개념을 찾아 정리했고, 며칠 뒤 3장 뒷부분부터 8장까지를 요점 위주로 읽었다. 나에게 실제로 필요한 내용은 3장부터였기 때문에, 의학 도메인에 치우친 부분은 가볍게 넘겼다. 아래는 논문의 순서를 따르되 내가 정리한 방식으로 다시 쓴 것이고, 읽으며 든 생각과 질문을 함께 적었다.

## 논문의 구성

| 장 | 내용 |
|---|---|
| 1 | Introduction |
| 2 | 다변량 시계열(MTS) 예측의 발전: M Competitions, 통계 모델과 딥러닝 모델(first wave, second wave) |
| 3 | 시계열 Transformer의 최근 발전: sparse attention, masking과 pre-training, representation learning |
| 4 | 시계열 GNN의 최근 발전 |
| 5 | Foundation model |
| 6 | 지식(knowledge)의 활용 |
| 7 | Meta evaluation: 평가 지표, 검증 방법, 문헌의 결과 정리 |
| 8 | Summary |

## 1. 문제의식

딥러닝은 여러 분야에서 두각을 드러냈지만 시계열 예측에서는 그 장점이 천천히 나타났다. 예측 대회인 M Competition에서도 통계 모델과 머신러닝을 결합한 방법이 상위권에 오른 것은 비교적 최근의 일이다. Attention을 쓰는 encoder-decoder, Transformer, GNN 같은 구조가 시계열에 적용되면서 딥러닝의 이점이 드러나기 시작했지만, 팬데믹 예측에는 여전히 세 가지 어려움이 남아 있다.

1. 효과적으로 학습하기에는 시계열이 충분히 길지 않다.
2. 모델이 그동안 축적된 과학 지식을 알지 못한다.
3. 모델을 해석하기 어렵다. 논문의 표현은 interpretability인데, 내용상으로는 설명가능성에 더 가깝다고 느꼈다.

이에 대한 대응으로 논문이 주목하는 방향이 둘이다. 하나는 방대한 데이터에서 미리 패턴을 익힌 뒤 새로운 문제에 적용하는 foundation model이고, 다른 하나는 knowledge graph나 LLM처럼 이미 존재하는 지식을 딥러닝 모델에 주입하는 연구다.

### 팬데믹 예측이라는 예시

논문은 날씨 예측이나 팬데믹 예측처럼 복잡하고 확률적인 시스템을 예측하려면 세 가지가 필요하다고 말한다. 여러 모델의 집합, 정확한 데이터 수집, 그리고 data assimilation이다. Data assimilation은 수집한 관측 자료를 모델에 넣어 모델의 예측 오차를 보정하는 과정을 말한다. 셋 중 하나라도 부족하면 정확도가 떨어지고, 그러면 통제 수단을 제대로 쓸 수 없다.

COVID-19 기간의 일간 사망자 수에는 매우 강한 주 단위의 톱니 모양 패턴이 나타나는데, 논문은 이를 보고 과정에서 생긴 인위적인 산물이라고 설명한다. 이 때문에 일부 연구는 주간 데이터를 쓰는 편이 낫다고 판단했고, 팬데믹 후반에는 일간 보고가 끝나 주간 보고만 남았다. 딥러닝 모델의 입장에서는 학습할 데이터가 줄어든 것이다. 입원자 수와 집중 치료 환자 수는 사망자 수의 좋은 선행 지표로 확인되었다.

처음 읽을 때는 이 대목이 왜 들어갔는지 알 수 없었다. 주말에만 보고가 몰려서 그런 것인지, 일간 데이터의 처리가 제대로 되지 않았다는 말을 하려는 것인지 분명하지 않았다. 다시 정리해 보니 요점은 다음과 같았다.

- 일간 보고를 믿기 어려운 것은 일간 데이터 자체의 가치가 낮아서가 아니라, 수집과 보고 관행이 일정하지 않았기 때문이다. 그래서 집계나 smoothing 없이는 추세를 분석하거나 모델링하기 어렵다.
- 주간 보고는 더 깨끗하지만 데이터의 양과 적시성이 줄어든다. 장기 추세 분석에는 주간 데이터가 더 믿을 만하고, 실시간 감시나 발병 탐지에는 일 단위 정보를 잃는 것이 문제가 된다.

팬데믹 데이터에 적용된 모델링 기법은 통계 모델, 머신러닝 모델, 그리고 이론에 기반한 compartmental model로 나뉜다. Compartmental model은 SIR, SEIR 모델처럼 인구를 몇 개의 상태로 나누고, 상태 사이의 전환을 데이터에서 추정한 비율 상수를 쓰는 미분방정식으로 기술한다. 다만 노출(exposed) 상태에 있는 사람의 수처럼 직접 관측하기 어려운 값을 추정해야 한다는 어려움이 있다. 이 부분은 의학 도메인에 치우친 내용이어서 무엇인지만 확인하고 넘어갔다.

통계 모델과 머신러닝 모델은 데이터에 적용하기 쉽고 반복되는 패턴과 과거에 대한 의존성을 찾아낸다. 둘 다 다변량 시계열(Multivariate Time Series, MTS) 예측에 쓸 수 있고, 관련 문제로는 MTS 분류와 이상 탐지가 있다. 이론과의 연결도 여전히 중요해서 Theory-Guided Data Science(TGDS)와 Physics-Informed Neural Networks(PINN) 같은 연구가 이어지고 있다.

### 통계 모델은 여전히 기준선이다

논문은 통계 모델과 머신러닝 모델이 상호보완적이라고 본다. 모델 연구라면 Random Walk(RW), Auto-Regressive(AR), SARIMAX 같은 믿을 만한 기준선을 포함해야 한다. SARIMAX는 학습 데이터가 적을 때 딥러닝과 경쟁할 만한 성능을 낸다. 팬데믹 초기에 주간 데이터를 쓰면 학습 데이터가 매우 적은데, 초기는 통제를 위해 정확한 예측이 가장 필요한 시점이기도 하다. 데이터가 충분해 딥러닝 모델이 좋은 성능을 내는 경우에도, SARIMAX의 결과는 그 성능을 검증하는 기준이 된다.

## 2. 다변량 시계열 예측의 발전

논문은 시계열 예측 모델의 역사를 표로 정리한다. 1950 ~ 70년대에 ARIMA, 지수 평활(Exponential Smoothing), VAR, SARIMAX 같은 통계 모델이 자리를 잡았고, Box와 Jenkins의 저서가 큰 영향을 주었다. 그 뒤의 큰 진전은 2010년대 딥러닝의 발전과 시기가 겹친다. GRU와 attention을 쓰는 encoder-decoder가 2014년, TCN과 GNN이 2016년, Transformer가 2017년이다.

### M Competitions

M Competition은 1982년에 시작해 2022 ~ 2023년의 M6까지 이어진 예측 대회다.

- 2018년에 끝난 M4에서는 머신러닝 기법만 쓴 팀의 성적이 좋지 않았고, LSTM과 통계 기법(ES)을 결합한 hybrid 모델이 우승했다. 상위권의 다른 팀들도 통계 기법을 섞어 썼다.
- 2020년 6월에 끝난 M5에 이르러서야 머신러닝 기법이 고전적인 통계 기법을 앞섰다. 상위권 팀 가운데 여럿이 gradient boosting tree를 효율적으로 구현한 LightGBM을 썼고, 신경망을 조합에 포함한 팀도 많았다. LSTM 유닛을 여러 개 쓰는 DeepAR, fully connected block을 여러 개 쌓은 N-BEATS가 그 예다.
- M6는 예측과 투자 전략을 함께 다루며, 논문을 읽던 시점에는 결과 요약이 2024년에 나올 예정이라고 되어 있었다.

머신러닝이 대회에서 상위권에 오르기까지 오래 걸린 이유로 논문은 두 가지를 든다. 시계열의 패턴은 알아내기 어렵고 자주 바뀐다. 그리고 빅데이터 이전에는 데이터가 너무 작아서 parameter가 많은 신경망을 학습시키기 어려웠다.

많은 모델이 recursive forecasting과 direct forecasting을 섞어 썼다는 대목에서 두 방식을 따로 정리했다.

| | Recursive forecasting | Direct (non-recursive) forecasting |
|---|---|---|
| 방식 | 이전 시점의 예측을 다음 시점의 입력으로 쓴다 | 각 시점을 한 번에, 또는 시점별 모델로 따로 예측한다 |
| 장점 | 단기 예측에 적합하다 | 오류가 누적되지 않아 예측 기간이 길어도 안정적이다 |
| 단점 | 초기 예측의 오류가 누적되어 기간이 길수록 오류가 커진다 | 시점별 모델을 따로 두는 경우에는 계산이 더 많이 필요하고, 데이터가 부족하면 성능이 떨어진다 |

### First wave: 통계 모델

SARIMAX가 두루 좋은 성능을 내는 이유는 세 가지 정보를 쓸 수 있기 때문이다.

1. 내생(endogenous) 시계열의 과거 값과 예측 값
2. 내생 시계열의 과거 오차(shock). MA 항이 과거의 예측 오차로 현재 값을 보정하고, AR 항이 과거의 패턴이 이어질 가능성을 반영한다.
3. 외생(exogenous) 시계열의 과거 값

여기에 더해 내생 변수를 차분해 정상성을 개선할 수 있고, 계절·주기 패턴도 활용할 수 있다.

읽으면서 찾아본 개념은 다음과 같다.

- **내생 변수**: 예측하려는 대상 변수 자체에서 나오는 입력이다. 간단히 말해 과거의 종속 변수($y$) 값이다. 판매량을 예측할 때 $t-1$, $t-2$ 시점의 판매량이 여기에 해당하고, 자기회귀(AR) 모델의 주된 입력이다.
- **외생 변수**: 모델 밖에서 관측되어 대상 변수에 영향을 주는 요인이다. 과거의 $y$가 아닌 독립 변수($x$)다. 판매량 예측에서의 할인율, 광고비, 날씨가 그 예다.
- **정상성(stationarity)**: 시계열의 통계적 특성이 시간이 지나도 그대로 유지된다는 성질이다.

많은 머신러닝 논문이 ARIMA를 비교 대상으로 삼지만 SARIMAX는 종종 ARIMA보다 좋은 성능을 낸다. M4와 M5에서는 지수 평활도 단순하면서 정확한 모델이 될 수 있음이 확인되었다.

가장 단순한 MTS 모델은 Vector Auto-Regressive(VAR) 모델이다. 시차(lag) $p$개와 변수 $n$개를 갖는 모델을 $VAR(p, n)$으로 쓴다. 예를 들어 신규 입원 환자 수 $y_{t0}$와 신규 사망자 수 $y_{t1}$을 묶어 시차 3으로 모델링하면 다음과 같다.

$$
\mathbf{y}_t = \boldsymbol{\delta} + \Phi^{(0)} \mathbf{y}_{t-1} + \Phi^{(1)} \mathbf{y}_{t-2} + \Phi^{(2)} \mathbf{y}_{t-3} + \boldsymbol{\epsilon}_t
$$

VARMA 모델도 있지만 VAR에 비해 성능 개선은 작고 훨씬 복잡하다.

SARIMAX와 VAR은 둘 다 MTS 모델로 볼 수 있다. 차이는 SARIMAX가 주된 변수 하나에 초점을 두고 나머지 변수를 그 변수의 지표로 쓴다는 점이다. 신규 사망자 수를 예측하는 데 신규 확진자 수와 입원 환자 수를 쓰는 식이다. SARIMAX는 VAR보다 오류 누적에 덜 취약하다고 한다. VAR은 내생 변수의 자기회귀에만 의존하므로 오류가 쌓일 수밖에 없지만, SARIMAX는 외생 변수도 쓰기 때문에 누적이 덜한 것이라고 이해했다.

SARIMAX의 기본 구조는 ARX(Auto-Regressive, eXogenous) 모델로 줄여서 볼 수 있다. 내생 변수의 시차 $p$개와 외생 변수의 시차 범위 $[a, b]$를 갖는다. $ARX(3, [2, 3])$은 다음과 같다.

$$
y_t = \delta + \phi_0 y_{t-1} + \phi_1 y_{t-2} + \phi_2 y_{t-3} + \beta_0 x_{t-2} + \beta_1 x_{t-3} + \epsilon_t
$$

### Second wave: 딥러닝

M5는 LightGBM의 가치를 보여 주었고, LSTM과 GRU가 일반적인 feed-forward network(FNN)보다 낫다는 것도 연구로 확인되었다.

RNN은 입력 벡터와 이전 hidden state의 가중 결합에 activation을 적용해 새 hidden state를 만들고, 마지막 층에서 이를 예측 값으로 바꾼다.

$$
\mathbf{x}_t = [y_{t-p}, \dots, y_{t-1}], \qquad
\mathbf{h}_t = f\!\left(U \mathbf{x}_t + W \mathbf{h}_{t-1} + \boldsymbol{\beta}^{(h)}\right), \qquad
\hat{y}_t = g\!\left(V \mathbf{h}_t + \beta^{(y)}\right)
$$

여기서 $W \mathbf{h}_{t-1}$ 항을 빼면 FNN과 같아진다. Sigmoid로 여닫는 gate를 추가한 GRU와 LSTM은 과거 정보를 기억하는 능력과 gradient의 안정성을 높였다.

그다음 진전은 Seq2Seq의 encoder-decoder 구조다. Encoder는 과거의 패턴을 학습하고 decoder는 미래를 예측하는 데 집중하며, 처음에는 LSTM이나 GRU로 구현했다. 긴 시계열에서는 모든 hidden state를 그대로 넘기는 것이 도움이 되지 않고, 예측에 필요한 것만 골라내는 것이 중요하다. 이 생각에서 attention mechanism이 나왔고 시계열 예측도 크게 개선되었다. Positional encoding과 self-attention, multi-head attention, cross attention이 LSTM·GRU 기반 encoder-decoder를 대체한 것이 Transformer다. Self-attention은 데이터의 순서를 고려하지 않으므로 순서 정보를 따로 넣어야 하는데, 논문은 두 가지를 구분한다.

- Positional encoding: 순차 데이터의 각 입력이 몇 번째인지를 모델에 알려 준다.
- Temporal encoding: 시계열의 시간 정보 자체를 모델에 명시적으로 알려 준다.

NLP에서는 Transformer가 가장 강력한 모델이고 일부 시계열 데이터셋에서도 상위권을 차지했다. 다만 시계열의 확률적인(stochastic) 특성 때문에 앞으로도 그럴지는 확실하지 않다. 그럼에도 NLP의 진전이 시계열 예측에 적용될 여지는 있다.

아직 연구가 덜 된 방향으로는 Temporal Convolutional Network(TCN)가 있다. Causal convolution과 dilated convolution으로 과거를 더 넓게 보고, skip connection으로 이전 층의 정보를 활용하면서 gradient를 안정시킨다. TCN이 LSTM을 넘어선다는 결과도 있지만 시계열 예측에서는 근거가 아직 분명하지 않다.

## 3. 시계열 Transformer의 최근 발전

### Sparse attention

모든 시점 쌍에 대해 attention을 계산하지 않고 일부만 골라 계산하는 sparse attention이 지난 몇 년간 꾸준히 연구되었다. 크게 보면 query의 수를 줄이거나, query가 정해져 있을 때 key의 수를 줄이는 방식이다. 많은 query를 대표하도록 고른 query를 query prototype이라고 한다. 두 시점이 멀리 떨어져 있으면 attention weight를 0으로 두는 방법도 있다. Sparse attention은 attention의 초점을 좁히는 동시에 계산 시간을 줄인다.

Transformer는 head와 layer가 여러 개여서 설명력을 확보하기 어렵다. Attention weight로 어느 정도는 설명할 수 있고, Transformer block을 단순하게 만드는 연구는 학습 시간뿐 아니라 설명력에도 도움이 된다.

### Masking과 pre-training

컴퓨터 비전과 NLP에서 크게 성공한 pre-training이 MTS 예측에도 통할지는 아직 열린 질문이다. 팬데믹으로 예를 들면, 인플루엔자 같은 과거 유행병 데이터로 미리 학습한 모델은 새 팬데믹의 데이터가 충분히 쌓이기 전에도 쓸 만한 예측을 낼 수 있을 것이다. 정점이 지난 뒤에야 모델이 쓸 만해지는 문제를 피할 수 있다.

- **PatchTST**: MTS를 단변량 시계열 여러 개로 나누는 channel-independence 전략을 쓰고, 각 시계열을 patch(예: 길이 16, stride 8)로 잘라 토큰처럼 Transformer에 넣는다. Patch마다 local semantic information을 담으면서 계산 비용과 메모리를 줄인다. Channel마다 다른 attention map을 만들어 다양한 temporal pattern을 잡고, look-back window가 길수록 성능이 좋아진다. 이 모델은 [PatchTST 리뷰](2024-11-25-patchtst.md)에서 따로 정리했다.
- **AR-Transformer**: vanilla Transformer 구조에 segment 기반 attention, teacher forcing, temporal·positional encoding, auto-regressive multi-horizon forecasting을 결합해 좋은 성능을 냈다.
- **Masked AutoEncoder(MAE)**: MTSMAE, ExtraMAE, Ti-MAE 등이 MTS 예측에 MAE를 쓴다. 입력 patch 일부를 가리고 복원하게 하는 방식으로, 입력을 latent representation으로 보냈다가 그대로 복원하는 일반적인 autoencoder보다 어려운 과제다. 가려진 patch를 복원하려면 temporal dependency를 더 온전히 잡아내야 하기 때문이다.
- **MTSMAE**: Vision Transformer의 개념을 가져와 patching으로 feature extraction을 개선하고 중복(redundancy)을 줄였다. Pre-training 단계에서는 무작위로 가린 patch를 복원하고, fine-tuning 단계에서는 앞에서 학습한 encoder를 그대로 쓰되 decoder의 입력을 다시 설계한다.

시계열에서 pre-training과 fine-tuning이 성공하기 어려운 이유는 둘이다. 대부분의 시계열이 짧고, 도메인 간 또는 분포의 이동(distribution shift)이 흔하다. Distribution shift는 모델이 학습한 데이터의 분포와 테스트 또는 실제 환경의 분포가 다른 상황을 말하며 성능 저하로 이어진다. 대응책으로 attention을 공유하는 domain adaptation 기법이 제안되었고, 시계열 모음을 유지하면서 시계열 전체나 구간을 원본 수준 또는 representation 수준에서 비교해 유사한 것을 찾는 접근도 있다.

### Representation learning

Representation learning은 원본 MTS를 직접 다루지 않고 latent space의 $[\mathbf{z}_t]$로 변환해, 시계열의 본질적인 정보를 분류나 예측에 쓰기 쉬운 형태로 담는 방법이다. 예측에서는 시점 $\tau$를 기준으로 과거와 미래를 나누고 같은 encoder $f$로 변환한다.

$$
Y = [\mathbf{y}_1, \dots, \mathbf{y}_\tau], \quad
Y' = [\mathbf{y}_{\tau+1}, \dots, \mathbf{y}_m], \quad
Z = f(Y, W), \quad
Z' = f(Y', W)
$$

목표는 $Y'$과 $g(Z', U)$의 차이를 줄이는 것이다. $f$는 encoder, parameter $U$를 갖는 $g$는 예측 함수(decoder) 역할을 하고 둘 다 여러 층으로 이루어질 수 있다. 일부 연구는 $g$로 정규화된 회귀(regularized regression)를 쓰는데, $[\mathbf{z}_t]$가 예측을 단순하게 만들 만큼 충분한 정보를 담았다고 가정하기 때문이다.

Representation을 좋게 만드는 방법들은 다음과 같다.

- **시계열 분해**: 시계열을 trend, seasonal, local variability 성분으로 나눈다. 성분들이 복잡하게 섞여 있어 이를 풀어내는(disentanglement) 기법도 발전했다. 논문은 선형 모델의 관점에서 분해를 다중공선성 축소의 일반화로 볼 수 있다고 한다. 다중공선성은 변수들의 설명력이 겹치는 것이므로, 성분을 나누면 그 겹침이 줄어든다는 뜻으로 이해했다.
- **Smoothing**: Legendre, Laguerre, Chebyshev 같은 직교 다항식에 시계열을 사영해 noise를 줄인다. 원래 데이터를 직교 다항식의 선형 결합으로 근사해 저주파의 주요 패턴만 남기고 고주파 noise를 줄이는 작업이라고 정리했다.
- **Augmentation과 masking**: BERT처럼 가려진 patch를 맞히게 하는 pretext training에 AE나 MAE를 쓸 수 있다.
- **Contrastive learning**: 유사한 구간을 positive 쌍으로, 다른 구간을 negative 쌍으로 묶어, 비슷한 샘플은 latent space에서 가깝게, 다른 샘플은 멀게 학습시키는 자기 지도 학습이다. TS2Vec, CoST, FEAT, SimTS가 여기에 속한다. 이상 탐지와 비슷한 개념이라는 인상을 받았는데, 정상 데이터만 학습하는 것이 아니라 positive와 negative를 모두 학습하면서 둘이 멀어지도록 한다는 점이 다르다. 한편 SimTS는 contrastive learning이 분류에는 효과적이지만 시계열 예측에는 그만큼 효과적이지 않다고 주장한다.

최근에는 $[\mathbf{z}_t]$ 자체를 해석 가능하게 만들려는 연구도 있다. 사후(post-hoc) 해석 기법을 쓸 수 있지만, 주 모델이 스스로 해석 가능한 것이 이상적이다.

### 더 단순한 구조

단순한 모델이 좋은 성능을 보인 사례도 있다. DLinear는 시계열을 trend와 나머지로 분해해 각각 linear 모델로 예측한 뒤 더하는 것이 전부인데도 좋은 결과를 냈다. TSMixer(논문이 인용한 것은 Ekambaram et al.의 모델이다)는 컴퓨터 비전의 MLP-Mixer에서 착안한 모델로, convolution도 self-attention도 쓰지 않는다. 시간 방향의 의존성과 변수 간 의존성을 번갈아(interleaved) 살피는데, 둘을 동시에 고려하는 것보다 복잡도가 낮다. 대신 강한 선행 지표가 있는 경우 그 정보를 놓칠 수 있다.

## 4. 시계열 GNN의 최근 발전

Transformer가 시간 방향의 분석에 잘 맞는다면 GNN은 개념적으로 시공간(spatial-temporal) 분석에 잘 맞는다. 시간 의존성은 encoder-decoder나 Transformer가 맡고, 시계열 사이의 의존성이나 공간 의존성은 GNN이 더 잘 잡는다.

**국가 단위 데이터.** GNN의 강점은 여러 시계열 사이의 의존성을 더 가깝게 모델링할 수 있다는 것이다. 변수 하나의 시계열을 그래프의 node로 두고, node 사이의 관계를 cross-correlation이나 mutual information 같은 edge 속성으로 둔다. Cross-correlation의 세기는 시차에 따라 달라진다. 입원이 사망보다 며칠 앞서는 것이 그 예다. 즉 GNN은 변수 사이의 지연된 상호작용을 명시적으로 고려할 수 있다. 데이터가 정상적이지 않으면 cross-correlation의 패턴도 시간에 따라 바뀔 수 있다.

**주(state) 단위 데이터.** 질병 확산을 예측하는 GNN 모델 다수는 이동 데이터나 사회적 연결로 지역을 이어 공간적 관계를 잡는다. 뉴욕에서 급격한 증가세가 나타나면 뉴저지의 예측은 그 정보에서 도움을 받을 수 있다. 지역 사이에 선형 또는 비선형 의존성이 있으면 node를 연결하는 연구도 있다. 주별 feature 사이의 correlation과 mutual information을 계산한 것으로, 오하이오와 일리노이의 사망자 수·확진자 수가 높은 상관을 보였다고 한다. 다만 데이터가 $Y = [y_{tjk}]$ (시간 $t$, 변수 $j$, 주 $k$)의 3차원이 되어 문제가 더 복잡해진다.

**GNN의 종류.** 초기 연구는 Fourier 변환을 쓰는 spectral domain에서 이루어졌다. ChebNet이 graph Laplacian을 쓰는 더 효율적인 계산을 제시했고, Graph Convolution Network(GCN)는 Laplacian을 직접 적용해 계산을 더 단순하게 만들었다. Message Passing Neural Network(MPNN)는 더 일반적인 형태로, node의 hidden state를 이전 값과 이웃이 보낸 message의 조합으로 갱신한다. Message는 두 node와 그 사이 edge의 feature에 대한 함수여서 edge feature를 계산에 포함할 수 있다. Graph Attention Network(GAT)는 attention weight로 node 사이의 의존성을 잡는다.

MTS 예측에서 모델이 다뤄야 하는 의존성은 두 가지다. (1) 공간 또는 변수 간 의존성, (2) 시간 의존성. GNN은 (1)에 적합하고, (2)를 위해서는 recurrent, convolutional, attention 기반 모델과 결합하는 경우가 많다. Block을 여러 개 이으면 더 먼 거리의 공간적 관계와 더 긴 시간 의존성을 잡을 수 있다. Transformer와 GNN을 결합하면 양쪽의 장점을 얻을 수 있는데, SageFormer가 그래프 표현과 GNN으로 여러 시계열 사이의 연결을 만들어 attention이 집중할 곳을 잡아 주는 예다.

## 5. Foundation Model

### Transfer learning과 무엇이 다른가

Transfer learning은 foundation model의 전신으로 볼 수 있다. 차이는 다음과 같이 정리했다. Transfer learning은 특정 작업이나 도메인의 큰 데이터로 학습한 모델을 비슷한 다른 작업에 fine-tuning하는 것이다. Foundation model도 방대한 데이터로 학습하지만 그 데이터가 더 일반적이어서 더 넓게 적용할 수 있다. 딥러닝과 transfer learning에 기반하면서도, 규모가 커서 더 넓은 응용과 새로운 능력(emergent capability)이 가능하다는 것이 논문이 인용하는 주장이다.

예측에서는 통계 모델이든 딥러닝 모델이든 특정 데이터셋으로 학습해 그 데이터의 패턴을 익히는 것이 기본이다. 그런데 parameter가 많은 모델을 학습시킬 만큼 데이터가 충분하지 않은 경우가 많다. Foundation model의 가치가 드러나는 것이 이 지점이다. 충분히 학습된 foundation model이라면 새로운 데이터셋의 패턴을 더 쉽게 잡을 것이다.

시계열도 자연어처럼 순차 데이터이므로 LLM만큼 잘 작동하리라 기대할 수 있지만, 시계열에서는 문제가 더 어렵다.

- **다양성**: 수많은 도메인에서 시계열이 수집되고, 도메인마다 고유한 특성이 있다.
- **작은 데이터셋이 많다**: 시계열 데이터는 대체로 짧은 시퀀스여서 딥러닝 모델이 힘을 쓰기 어렵다.
- **어휘, 문법, 의미가 없다**: 어떤 숫자의 나열이든 시계열이 될 수 있다. 구조적 패턴이 비교적 제한된 자연어와 달리 시계열의 구조와 패턴은 훨씬 복잡하고 동적이다.

### Backbone

Foundation model은 딥러닝 모델 하나 또는 여러 개의 조합을 키워서 만든다.

- MTS에서는 sparse attention에 초점을 둔 모델이 full attention을 쓰는 Transformer보다 오차가 작았다.
- 지금의 논쟁은 encoder-decoder 구조와 decoder-only 구조 가운데 무엇을 쓸지에 있다. GPT는 decoder-only, BART와 T5는 encoder-decoder다.
- 그 밖의 후보로 self-attention에 convolution 기반 head를 더한 Transformer++, Mamba 같은 state-space model이 있다.
- 시계열 고유의 쟁점으로 channel-independent 방식과 cross-channel 방식 가운데 무엇을 쓸지, 그리고 어떤 변수를 포함할지가 있다. 변수를 많이 넣는 것이 오히려 역효과일 수 있다.
- 시공간 도메인에서는 Graph Foundation Model(GFM)이 관련된다. 가장 많이 쓰이는 backbone은 message passing 기반 GNN과 Transformer 기반 모델이고, 둘을 결합한 hybrid는 표현력이 높고 long-range dependency를 잘 모델링한다.

### 만드는 방법 네 가지

| 유형 | 방법 | 논문이 든 예 |
|---|---|---|
| 1. 기존 LLM의 재활용 | 시계열 구간이나 patch를 단어처럼 바꿔 LLM에 넣고, 출력을 다시 시계열로 바꾼다 | PromptCast(적응 없이 사용), TEMPO(LLM을 적응) |
| 2. 범용 시계열 foundation model | 방대한 시계열 데이터셋으로 처음부터 사전 학습한다 | TimeGPT, TimeCLR, PreDcT, Lag-Llama |
| 3. 특정 도메인용 foundation model | 도메인과 관련된 시계열로 처음부터 사전 학습한다 | AutoMixer |
| 4. Multi-modal foundation model | 텍스트와 시계열을 함께 쓴다 | Time-LLM |

**유형 1.** 단어의 나열과 시계열 구간의 나열 사이에 보편적인 패턴이 있다는 것이 전제다. 그러나 주의하지 않으면 시계열을 바꿔 만든 단어열은 의미 없는 문장이 되기 쉽고, 출력 단어를 시계열로 되돌릴 때도 마찬가지다. 그래서 LLM을 시계열 데이터로 fine-tuning하면 예측 능력이 나아질 수 있다. LLM은 토큰을 입력으로 받는데 시점 하나로는 의미 정보를 담을 수 없으므로 데이터를 patch로 나눠야 하고, distribution shift를 줄이려고 RevIN을 쓰기도 한다. GPT를 backbone으로 쓰는 모델 가운데에는 auto-regressive하지 않고 decoder의 마지막 hidden state를 펼쳐 linear head로 예측하는 것이 있는데, 이 방식은 예측 길이를 바꿀 수 없다.

**유형 2.** 방대한 시계열을 모으고 전처리하는 것부터가 큰 일이고, 학습에는 고성능 컴퓨팅이 필요하다.

- TimeGPT는 residual connection과 layer normalization을 갖춘 여러 층의 encoder-decoder 구조를 쓴 초기 foundation model이다.
- TimeCLR은 시계열 분류를 위해 사전 학습된 모델이다. SimCLR을 바탕으로 한 contrastive learning을 쓰고, jittering, smoothing, magnitude warping, time warping, circular shifting, slope·spike·step 추가, masking, cropping 같은 augmentation을 사용한다. 증강한 데이터를 씀으로써 예측에 필요한 핵심 패턴을 파악하는 능력, 즉 더 많은 불변성을 학습하게 한다는 뜻으로 이해했다.
- PreDcT는 decoder-only 구조를 auto-regressive하게 써서 과거 길이, 예측 길이, 시간 단위가 달라져도 대응한다. 미래 토큰을 보지 못하게 mask를 씌우는 causal self-attention을 쓴다.
- TimesNet은 FFT로 최적의 주기를 찾고 주기 단위로 시계열을 쌓아 2D로 표현한다. 2D 형태에서는 주기 사이와 주기 내부의 특징을 2D kernel로 추정할 수 있다.
- Lag-Llama는 LLaMA의 구성 요소(RMSNorm, RoPE, SwiGLU, AdamW)를 따른다. RMSNorm은 RMS로 나누는 방식으로 LayerNorm보다 빠르고, RoPE는 절대 위치를 회전 행렬로 인코딩하면서 상대 위치 의존성을 self-attention 식에 반영하는 방식이다. 입력을 patch가 아니라 계절성 등을 고려해 고른 시차(lag)로 구성한다는 점이 다르고, 단변량 시계열에만 쓸 수 있다는 한계가 있다.

**유형 3.** 필요한 데이터 양과 학습 자원 면에서 더 감당할 만하다. 도메인마다 계절성과 추세의 특성이 다르므로 특정 도메인으로 사전 학습하면 성능이 나아질 수 있다. AutoMixer는 비즈니스와 IT 관측 데이터를 대상으로 하며, channel 압축을 pre-training 과제로 삼는다. 원래 channel들을 압축된 channel 공간으로 보내 중요하지 않은 channel은 버리고 중요한 상관관계만 남긴다.

**유형 4.** 텍스트와 시계열을 timestamp 기준으로 맞추어야 하고, 시계열 정렬에는 Dynamic Time Warping 같은 기법이 필요하다. 예측 정확도를 높이면서 설명 가능성의 여지도 커진다. Time-LLM은 사전 학습된 LLM을 쓰는 multi-modal framework다. 입력 시계열을 patching과 전용 embedding 층으로 토큰화하고, patch embedding을 압축된 text prototype으로 다시 표현(reprogramming)해 두 modality를 맞춘다. 입력의 통계 정보를 텍스트로 쓴 prompt prefix도 함께 넣는다.

### Pre-training과 fine-tuning

시계열 foundation model의 pre-training은 데이터가 다양해서 어렵지만, 데이터의 양과 차원이 LLM보다 작다는 점에서는 쉽다. 데이터셋을 충분히 찾는 것부터가 과제다. 레이블이 부족한 문제는 self-supervised learning으로 대응한다. 시계열 일부를 가리고 다시 만들어 내게 하거나, 복잡한 temporal pattern을 해치지 않는 범위에서 augmentation을 쓴다. Noise를 제거하는 것뿐 아니라 더하는 것도 모델이 실제 패턴을 보는 데 도움이 될 수 있다는 점이 새로웠다.

LLM에서는 self-supervised pruning metric으로 의미 없는 학습 샘플을 줄이는 방법, 곧 잘 고른 소수의 샘플만 더해도 성능이 오른다는 결과가 있다. 이것이 시계열 예측에 어떻게 옮겨질지는 아직 열린 문제다.

Fine-tuning은 특정 하위 도메인에서의 성능을 위해 parameter를 조금 조정하는 것이다. Transformer 기반 모델이라면 마지막 몇 개 block의 마지막 층만 다시 학습하고 나머지를 고정하는 방법, attention weight를 다시 학습하는 방법이 있다. 가장 효율적인 조합이 무엇인지는 연구가 진행 중이다. Parameter Efficient Fine-Tuning(PEFT)은 사전 학습된 모델의 정확도를 가능한 한 효율적으로 높이려는 기법들이다.

| 기법 | 내용 |
|---|---|
| Sparse fine-tuning | parameter 값이나 gradient 값을 기준으로 일부 parameter만 골라 조정하고 나머지 대부분은 고정한다 |
| Adapter fine-tuning | 학습 가능한 새 가중치 행렬을 추가하고 그것만 학습한다 |
| LoRA (Low-Rank Adaptation) | Adapter와 비슷하지만 기존 층에 통합된다. 미리 계산해 둘 수 있어 추론 속도가 full fine-tuning과 같다 |

특정 도메인에서 foundation model의 정확도를 높이는 방법으로는 fine-tuning 외에 두 가지가 더 있다. Retrieval Augmented Generation(RAG)은 계속 갱신되는 정보원에서 관련 정보를 검색해 출력이 그것을 참조하게 하는 방법으로, hallucination을 줄이고 답의 최신성을 높인다. Prompt Engineering은 질의에 관련 정보를 덧붙여 모델이 그 정보를 알게 하는 방법이다.

## 6. 지식의 활용

지식을 예측에 쓰려는 시도는 오래되었다. 규칙으로 예측에 가중치를 주는 방법, 상황 인식(situational awareness)에 지식을 쓰는 방법, 예측 성능이나 해석 가능성을 위해 feature selection에 지식을 쓰는 방법이 있다.

모델에 지식을 넣어 정확도를 높인 예로 KERN(Knowledge Enhanced Recurrent Network)이 있다. 패션 트렌드를 예측하는 LSTM encoder-decoder에 내부 지식과 외부 지식을 더한 모델이다. 내부 지식으로는 트렌드 패턴 사이의 가깝고 먼 유사 관계를 정규화 항으로 손실 함수에 더하고, 외부(도메인) 지식으로는 패션 요소의 ontology를 encoder 입력의 embedding을 통해 넣는다. Ontology는 사물과 사물 사이의 관계, 개념을 컴퓨터가 처리할 수 있게 표현한 것이다.

논문은 지식을 모델에 넣는 방법을 여섯 가지로 정리한다. $y$는 실제 시계열, $\hat{y}$는 예측이다.

1. **Composite loss**: 이론 또는 시뮬레이션 모델의 예측 $z$와의 차이를 손실에 섞는다.

    $$
    (1 - \lambda) \lVert y - \hat{y} \rVert_p + \lambda \lVert z - \hat{y} \rVert_p
    $$

2. **제약 조건**: 제약 위반에 대한 penalty 함수 $f_c$를 더한다. 제약의 형태에 따라 정규화로 볼 수도 있다.

    $$
    \lVert y - \hat{y} \rVert_p + \lambda f_c(\hat{y})
    $$

3. **Self-attention에 반영**: 변수 $j$가 시차 $l$의 변수 $k$와 관련 있다는 정보($y_{tj}$와 $y_{t-l,k}$의 관련성)를 temporal knowledge graph에 두고, attention 계산의 초점을 맞추거나 수정하는 데 쓴다.
4. **입력과 결합**: 도메인 knowledge graph의 sub-graph에서 얻은 embedding을 입력 MTS(원본 또는 patch 수준)와 이어 붙인다.
5. **뒤쪽 층에 주입**: 지식을 입력이나 latent representation과 결합할 가장 좋은 위치를 정하는 것은 어렵다. $x_t$를 $z_t$로 보내는 representation learning 기반 모델에서는 최종 representation이 만들어지기 전 어디에서든 가능하다.
6. **구조에 반영**: 도메인 knowledge graph의 sub-graph를 GNN 구조의 초안으로 쓴다.

Temporal Knowledge Graph Embedding(TKGE)은 그래프의 요소를 latent vector로 나타내고 시간 관계를 포함한 관계들이 벡터의 상대적 위치를 정하게 하는 방법이다. 링크 예측에 쓰이는데, 대상 시점이 미래이면 그것이 곧 예측이 된다. 이 벡터들이 다른 딥러닝 예측 모델을 얼마나 개선할 수 있는지는 열린 질문이다.

LLM을 지식으로 강화하는 연구도 진행 중이다. BERT, GPT, BART, T5 같은 사전 학습 언어 모델(PLM)의 구조에 가장 잘 맞는 지식 주입 방법을 설계하는 것이 핵심 질문이다. 아직은 LLM만으로 정확한 예측 모델을 만들기 어렵고, 도메인 지식을 쓴 fine-tuning과 prompt 설계가 도움이 된다. LLM을 MTS 모델을 개선하는 데 쓰거나 함께 쓸 수도 있다. 최근 문헌에는 temporal dependency를 잡는 데 기존의 attention이 꼭 필요하지 않을 수 있다는 결과도 있다(MTS-Mixers). 그래서 논문은 팬데믹 예측을 위해 (1) 언어, (2) temporal pattern, (3) 지식의 이해와 처리를 각각 맡는 전문 모델들이 협력하는 구조를 전망한다.

COVID-19 knowledge graph를 다룬 절과 지식 강화 GNN을 다룬 절은 각각 도메인에 치우치거나 앞의 내용과 겹쳐서 건너뛰었다.

## 7. Meta Evaluation

여러 시점 앞을 예측하는 multi-horizon forecasting은 불확실성이 큰 과제다. 예측 기간이 길수록 예측은 나빠지고, 어디까지가 현실적인 예측 기간인지는 도메인과 예측 대상에 따라 다르다.

### 예측 품질 지표

| 지표 | 의미 | 식 |
|---|---|---|
| MSE | Mean Squared Error | $\mathrm{mean}\big((\mathbf{y} - \hat{\mathbf{y}})^2\big)$ |
| $R^2$ | Coefficient of Determination | $1 - \mathrm{MSE} / \mathrm{var}(\mathbf{y})$ |
| RMSE | Root Mean Squared Error | $\sqrt{\mathrm{MSE}}$ |
| NRMSE | Normalized RMSE | $\mathrm{RMSE} / \mathrm{mean}(\mathbf{y})$ |
| MAE | Mean Absolute Error | $\mathrm{mean}\big(\lvert \mathbf{y} - \hat{\mathbf{y}} \rvert\big)$ |
| MAPE | Mean Absolute Percentage Error | $100\,\mathrm{mean}\!\left(\dfrac{\lvert \mathbf{y} - \hat{\mathbf{y}} \rvert}{\lvert \mathbf{y} \rvert}\right)$ |
| sMAPE | symmetric MAPE | $200\,\mathrm{mean}\!\left(\dfrac{\lvert \mathbf{y} - \hat{\mathbf{y}} \rvert}{\lvert \mathbf{y} \rvert + \lvert \hat{\mathbf{y}} \rvert}\right)$ |
| MASE | Mean Absolute Scaled Error | $\dfrac{\mathrm{MAE}(\text{model})}{\mathrm{MAE}(\text{RW@h1})}$ |

- MSE, RMSE, MAE는 도메인과 단위를 알아야 해석할 수 있다.
- NRMSE는 평균이 0이면 무한대로 발산한다. 이 경우 범위(range)로 나누는 정의를 쓸 수 있다.
- $R^2$는 시간 의존성과 구간마다 달라지는 변동성 때문에 회귀 문제에서만큼 유용하지 않다.
- MAPE는 관측 값이 0이면 발산한다. MAPE와 sMAPE는 단위에 따라 달라져서, 섭씨를 켈빈으로 바꾸기만 해도 오차가 작아 보인다.
- MASE는 단위와 척도에 영향을 받지 않는다. 한 시점 앞을 예측하는 random walk(RW@h1)의 MAE로 나눈 값이어서, 1이면 random walk와 같은 수준, 1보다 작으면 더 좋고 크면 더 나쁘다. 다만 10 시점 앞 예측에서의 MASE 2는 꽤 좋은 값일 수 있다.

Random walk는 현재 값이 이전 값에 무작위 변동만 더해져 정해지는 가장 단순한 시계열 모델이다. $y_t = y_{t-1} + \epsilon_t$이고 $\epsilon_t$는 평균이 0인 무작위 오차다. 예측에서는 이전 시점의 값을 그대로 다음 시점의 예측으로 쓴다.

### Rolling validation

시계열에는 시간 의존성이 있어 일반적인 k-fold cross-validation을 쓸 수 없다. 그렇다고 데이터를 순서대로 학습용과 테스트용으로 한 번 나누고, 학습한 parameter로 테스트 구간 전체를 예측하면 시간이 갈수록 정확도가 떨어진다. 원인은 두 가지 노후화(staleness)다.

1. **데이터의 노후화**: 테스트 도중 새로 관측된 데이터가 예측에 반영되지 않는다.
2. **Parameter의 노후화**: 시간이 지날수록 parameter가 최신 정보를 반영하지 못한다.

해결 방법은 다음과 같다.

- 데이터: 예측 기간 $h$를 테스트 구간보다 짧게 둔다. 1부터 $h$까지의 예측을 마치면 window를 한 시점 앞으로 옮겨 다시 예측한다. 옮길 때마다 테스트 구간의 값 하나가 예측에 쓸 수 있는 정보로 추가되므로 정보가 계속 새로워진다. 모든 예측은 행이 시간, 열이 예측 기간인 행렬에 모아 둔다.
- Parameter: 주기를 정해 다시 학습한다. 보통 학습 구간의 첫 값을 빼고 테스트 구간의 첫 값을 넣는다. 단순한 모델은 window가 움직일 때마다 다시 학습해도 되지만, 복잡한 모델은 계산 비용 때문에 주기를 길게 잡는다.

이처럼 데이터와 parameter의 노후화를 줄이는 기법을 rolling validation이라고 한다. Foundation model이라면 같은 절차에서 재학습을 fine-tuning으로 바꾸면 된다. Window를 다루는 방식에 따라 rolling window와 expanding window가 있다.

### 문헌에서 반복되는 결과

벤치마크로 자주 쓰이는 데이터셋은 다음과 같다.

| 데이터셋 | 내용 | 주기 |
|---|---|---|
| ETTh1, ETTh2 | 중국 두 지역 변압기의 전력 부하 6종과 오일 온도, 2년 분량 | 1시간 |
| ETTm1, ETTm2 | 같은 변압기 데이터 | 15분 |
| ILI | 미국 CDC가 관리하는 인플루엔자 유사 질환 환자 보고 | 1주 |
| Electricity | 고객 321명의 전력 사용량. 열 하나가 고객 한 명이다 | 1시간 |
| Weather | 독일 예나의 기상 관측소에서 수집한 습도, CO2, 강수량 등 | - |
| Traffic | 샌프란시스코 고속도로 센서 862개의 도로 점유율. 열 하나가 센서 하나다 | 1시간 |

논문이 여러 문헌의 결과를 모아 정리한 관찰은 다음과 같다.

- **Look-back window**: 초기 시계열 Transformer들은 look-back window를 늘려도 성능이 좋아지지 않고 오히려 떨어져, 길어진 시간 정보를 활용하지 못했다. PatchTST는 look-back window가 길수록 좋아진다.
- **Channel mixing과 channel independence**: Channel mixing은 모든 feature를 공유 embedding 공간에 사영한 뒤 attention을 적용하고, channel independence는 channel마다 시점들에 따로 attention을 적용한다. Channel마다 움직임이 달라서 모두 섞으면 전체 성능이 나빠질 수 있다. 시간 방향 attention과 channel 방향 attention을 두 단계로 나누어 쓰는 연구도 있다.
- **Embedding**: feature를 embedding 공간으로 보내는 feature embedding, positional embedding, temporal embedding 등 여러 종류가 쓰인다.
- **Instance normalization**: Reversible Instance Normalization(RevIN)은 look-back window와 예측 구간 사이의 distribution shift를 줄인다. Look-back window를 정규화했다가 최종 예측을 내기 전에 되돌리며, 되돌릴 때 쓰는 평균과 표준편차는 학습할 수도 있다.
- **Layer normalization과 batch normalization**: 대부분의 Transformer 기반 모델은 layer normalization을 쓰지만 PatchTST는 batch normalization을 쓴다.
- **Residual attention**: PatchTST는 한 층의 attention score를 보관했다가 다음 층의 attention score에 더하는 residual attention을 쓴다. 다른 모델들은 중간 attention score를 쓰지 않는다.

## 8. 논문의 결론

- 발전된 딥러닝 구조가 쓰이면서 MTS 예측 연구가 활발해지고 있다. Knowledge graph와 LLM으로 성능을 더 끌어올리려는 연구, 과학 문헌으로 모델을 학습시키고 기존 지식을 주입하려는 시도가 시작되었다. 이것이 시계열 예측과 분류, 이상 탐지에 얼마나 도움이 되는지 온전히 밝히는 데는 시간이 걸릴 것이다.
- Foundation model은 더 정확하고 강건한 예측과 더 나은 설명 가능성을 기대하게 한다. Backbone과 구조 요소의 선택지가 많고, LLM에서 성공한 요소가 좋은 출발점이지만 시계열에 맞게 최적화하는 연구가 더 필요하다.
- 시계열 foundation model을 LLM 수준으로 만드는 것은 어렵다. 시계열에는 자연어와 같은 풍부한 의미가 없고, 있는 의미도 도메인에 크게 의존한다. 심전도 신호를 모델링한 것이 주가 예측에는 거의 도움이 되지 않는다.
- 그럼에도 multi-modal 모델에는 가능성이 있다. 텍스트는 원본 시계열에 없는 맥락을 주고, 도메인 지식으로 해석 가능성을 높이며, 추가적인 feature와 변동성의 차원을 제공해 더 강건하고 일반화된 모델을 학습하게 한다.

## 읽으며 생각한 것

**부분적인 channel mixing.** Channel을 모두 섞으면 성능이 나빠지고 모두 독립으로 두면 변수 사이의 정보를 버리게 된다. 그렇다면 attention으로 channel 사이의 상관을 따져 볼 수는 없을까. 상관을 적절히 계산할 수 있다면 관련 있는 channel끼리만 섞는 부분적인 channel-mixing attention을 만들 수 있을 것이고, 여기에 sparse attention을 함께 쓰는 것도 생각해 볼 만하다. 이 과정에 도메인 지식이 도움이 될 수 있다. Channel마다 성질이 이렇게 다르다는 점이 시계열 foundation model을 만들기 어려운 이유이기도 하다고 생각했다.

**학습 샘플 고르기.** LLM에서 통한 "의미 없는 학습 샘플을 줄이고 잘 고른 샘플을 쓰는" 방법이 시계열에서는 왜 어려운지, 그렇다면 어떤 방식을 적용할 수 있을지는 따로 고민해 보고 싶은 주제로 남겼다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. Direct forecasting이 계산을 더 많이 필요로 한다는 설명은 납득되지 않았다. 예측을 다시 입력으로 넣으며 반복하는 recursive 쪽이 오히려 계산이 많지 않은가.**

어느 단계의 계산을 말하느냐에 따라 답이 달라진다. 전통적인 direct 전략은 예측 시점마다 모델을 따로 둔다. 예측 기간이 $h$이면 $\hat{y}_{t+1} = f_1(\cdot), \dots, \hat{y}_{t+h} = f_h(\cdot)$로 모델 $h$개를 학습하고 보관해야 하므로, 학습 비용이 $h$에 비례해 늘어난다. Recursive 전략은 한 시점 앞을 예측하는 모델 하나만 학습하면 된다. "Direct가 계산이 더 많다"는 설명은 이 학습과 유지 비용을 가리킨다. 추론만 놓고 보면 질문의 직관이 맞다. Recursive는 모델을 $h$번 차례로 실행해야 하고, 앞의 결과가 있어야 다음을 계산할 수 있어 병렬화도 되지 않는다. 그리고 DLinear나 PatchTST처럼 출력층이 $h$개의 값을 한 번에 내는 모델은 모델도 하나이고 forward도 한 번이어서, direct 방식이면서 recursive보다 계산이 적다. 따라서 표의 설명은 시점별 모델을 두는 direct 전략에 한정해서 읽어야 한다.

**Q. SARIMAX가 가진 세 가지 정보, 차분으로 정상성을 개선한다는 것, "SARIMAX의 specification이 ARX의 specification을 포함한다"는 것은 각각 무슨 뜻인가.**

논문은 SARIMAX의 명세를 $\mathrm{SARIMAX}(p, d, q) \times (P, D, Q)_s\,[a, b]$로 적는다. $p$는 AR 항(내생 변수의 과거 값)의 수, $d$는 간격 1로 차분하는 횟수, $q$는 MA 항(과거의 shock, 곧 과거 예측 오차)의 수다. $P$, $D$, $Q$는 같은 것을 계절 주기 $s$ 간격으로 적용한 것이고, $[a, b]$는 포함할 외생 변수의 시차 범위다. 본문에서 말한 세 가지 정보가 각각 $p$, $q$, $[a, b]$에 대응한다.

"포함한다"는 것은 ARX가 SARIMAX의 특수한 경우라는 뜻이다. $d = q = 0$, $P = D = Q = 0$으로 두면 남는 것은 내생 변수의 시차 $p$개와 외생 변수의 시차 $[a, b]$뿐이고, 이것이 $ARX(p, [a, b])$다. 논문은 이어서 차이도 짚는다. ARX의 parameter는 연립 일차방정식을 풀어 구할 수 있지만, MA 항이 들어가면 식이 비선형이 되어 반복적인 최적화로 손실을 줄여야 한다.

차분은 추세를 없애 평균이 일정한 시계열로 바꾸는 연산이다. $y_t = y_{t-1} + c + \epsilon_t$처럼 값이 계속 쌓이는 시계열은 평균이 시간에 따라 변하므로 정상적이지 않다. 한 번 차분한 $y_t - y_{t-1} = c + \epsilon_t$는 평균이 $c$로 일정하다. $d$는 이 차분을 몇 번 할지를 정하고, 계절 차분 $y_t - y_{t-s}$는 주기 $s$로 반복되는 패턴을 같은 방식으로 없앤다.

**Q. AR-Transformer가 temporal encoding과 positional encoding을 함께 쓴다고 하는데, 둘은 구체적으로 어떻게 다른가.**

논문의 구분으로는 positional encoding은 $x_t$의 절대적 또는 상대적인 순서에 기반하고, temporal encoding은 $x_t$의 날짜와 시각에 기반한다. 시간 단위 데이터에서 길이 96의 window를 입력으로 넣는다고 하자. Positional encoding은 window 안에서의 번호 $0, 1, \dots, 95$를 인코딩하므로 어느 window에서나 같다. Temporal encoding은 각 시점의 실제 시각, 예를 들어 "3월, 화요일, 14시"를 인코딩하므로 window마다 다르고, 하루나 일주일 간격으로 같은 값이 돌아온다. 앞의 것은 입력 안에서의 앞뒤 관계를, 뒤의 것은 달력에 묶인 주기를 모델에 알려 준다. [Informer](2025-01-15-informer.md)가 입력에 더하는 local time stamp와 global time stamp가 각각 이 둘에 해당한다.

**Q. MTSMAE가 줄였다는 redundancy는 무엇을 가리키는가.**

시계열의 정보 중복을 가리킨다. MTSMAE 논문은 다변량 시계열의 국소 정보에 중복이 심해서, 가려진 값을 높은 수준의 이해 없이 인접한 시점의 정보만으로 쉽게 복원할 수 있다고 설명한다. 그러면 복원 과제가 너무 쉬워져 유용한 feature를 배우지 못한다. 그래서 MTSMAE는 patch 단위로 입력을 만들고 원래의 MAE보다 높은 비율(약 85%)의 patch를 무작위로 가려, 보이는 이웃만으로는 복원하기 어려운 과제를 만든다. 이것이 논문이 말하는 "redundancy를 줄인다"의 뜻이다. [PatchTST 리뷰](2024-11-25-patchtst.md)의 3절에서 본, 바로 앞 시점의 값을 복사하기만 해도 잘 맞는다는 문제와 같은 이야기다.

**Q. SimTS가 contrastive learning이 예측에 효과적이지 않다고 주장하는 근거는 무엇인가. 학습에 negative 쌍을 쓰지 않는다는 뜻인가.**

Negative 쌍을 쓰지 않는 것이 맞고, SimTS 논문이 드는 근거는 세 가지다. 첫째, contrastive learning은 대부분 서로 다른 instance를 구별하도록 학습하는데(instance discrimination), 이렇게 얻은 feature는 분류에는 유용하지만 과거로부터 미래를 예측하는 데에는 충분하지 않을 수 있다. 둘째, negative 쌍을 만드는 기존 방법은 "같은 시계열의 구간들은 시차가 멀수록 덜 비슷하다", "서로 다른 시계열의 구간은 비슷하지 않다"는 가정에 기대는데, 주기성이 강한 시계열에서는 멀리 떨어진 구간이나 다른 시계열에도 비슷한 패턴이 있다. 그런 구간을 negative로 고르면 실제로는 비슷한 표본의 representation을 서로 밀어내게 되고, 논문은 이것을 false repulsion이라고 부른다. 셋째, 분류와 달리 예측에서는 의미를 보존하는 augmentation을 찾기가 어렵다. 그래서 SimTS는 negative 쌍을 쓰지 않고, augmentation으로 positive 쌍을 만들지도 않는다. 대신 시계열을 과거 구간과 미래 구간으로 나누어, 과거의 latent representation으로 예측한 미래의 representation과 실제 미래를 인코딩한 representation을 positive 쌍으로 삼아 둘이 가까워지도록 학습한다.

**Q. Representation learning에서 말하는 augmentation은 구체적으로 무엇인가.**

원래 시계열의 의미는 유지하면서 겉모습만 바꾼 표본을 만드는 변환이다. 논문이 TimeCLR을 소개하며 나열한 것들이 대표적이다. 작은 noise를 더하는 jittering, 값을 매끄럽게 만드는 smoothing, 진폭을 구간마다 다르게 늘이고 줄이는 magnitude warping, 시간 축을 늘이고 줄이는 time warping, 시작점을 옮기는 circular shifting, 일부 구간을 가리는 masking, 일부 구간만 잘라 쓰는 cropping 등이다. Contrastive learning에서는 같은 시계열에 서로 다른 augmentation을 적용한 두 표본을 positive 쌍으로 삼으므로, 모델은 그 변환에 영향받지 않는 representation을 배우게 된다. 어떤 변환이 의미를 유지하는지는 과제에 따라 다르다. Time warping은 분류에서는 class를 바꾸지 않을 수 있지만, 주기와 시점이 곧 정보인 예측에서는 정답을 바꿔 버릴 수 있다.

**Q. TSMixer가 시간 의존성과 변수 간 의존성을 번갈아 본다는 것은 구체적으로 어떻게 동작하는가.**

같은 이름의 모델이 둘 있어서 나누어 적는다. 논문이 인용한 것은 Ekambaram et al.의 TSMixer다. 이 논문에 따르면 입력을 patch로 나눈 뒤 mixer layer를 쌓는데, layer 하나 안에 MLP 기반 모듈이 차례로 놓인다. Patch들 사이의 상관을 배우는 inter-patch mixer, patch 안의 hidden feature를 섞는 intra-patch mixer, 그리고 channel들 사이의 상관을 배우는 inter-channel mixer다. 모듈마다 자기가 맡은 축에 맞게 tensor의 모양을 바꾼 뒤 MLP를 적용하고 모양을 되돌린다. Inter-channel mixer는 backbone의 한 변형에만 들어가고, channel-independent backbone에 변수 간 관계를 보정하는 예측 head를 덧붙이는 구성도 있다.

Chen et al.의 TSMixer는 "번갈아"라는 표현에 더 직접 들어맞는다. 입력을 (시점 × 변수) 행렬로 두고, 시간 축 방향의 MLP(time-mixing, 모든 변수가 공유)와 변수 축 방향의 MLP(feature-mixing, 모든 시점이 공유)를 교대로 적용하는 block을 쌓는다. 두 모델 모두 한 번의 연산은 한 축만 섞는다는 점이 같다. 시점과 변수의 모든 조합을 한꺼번에 섞는 것보다 parameter가 훨씬 적은 대신, "변수 A의 3시점 전 값이 변수 B의 현재 값에 영향을 준다"와 같이 시차가 있는 변수 간 관계는 한 번의 연산으로 표현하지 못한다. 시간 방향으로 섞은 결과를 다시 변수 방향으로 섞는 두 단계를 거쳐 간접적으로만 표현된다. 강한 선행 지표를 놓칠 수 있다는 논문의 지적은 이 간접성을 두고 한 말로 읽었다.

**Q. 하나의 block이 시간과 공간 의존성을 함께 처리한다는 GNN 쪽 설명은 무슨 뜻인가.**

논문의 해당 문장은 주어가 빠진 채 실려 있어서 어느 모델에 대한 설명인지 알 수 없다. 일반적인 구조로 설명하면 다음과 같다. 시공간 GNN의 입력은 (시점 $T$) × (node $N$) × (feature) 모양의 tensor다. 대표적인 예인 STGCN의 block은 시간 방향 convolution, graph convolution, 시간 방향 convolution을 차례로 쌓은 것이다. 시간 방향 convolution은 node마다 시간 축을 따라 인접한 시점들을 섞고, graph convolution은 시점마다 그래프에서 이웃한 node들을 섞는다. 가장 단순한 graph convolution은 정규화한 인접 행렬 $\hat{A}$를 써서 $H' = \sigma(\hat{A} H W)$로 쓴다. 두 연산을 한 block에 이어 놓았으므로, block 하나를 지난 뒤 어떤 node의 어떤 시점 값에는 이웃 node들의 인접한 시점 정보가 함께 들어 있다. Block을 여러 개 이으면 그래프에서 더 먼 node와 더 먼 과거까지 닿는다. 논문이 "block을 이어 붙여 다양한 거리의 공간적 관계와 더 긴 시간 의존성을 잡는다"고 한 것이 이 뜻이다.

**Q. Knowledge graph와 temporal knowledge graph embedding은 정확히 무엇인가.**

Knowledge graph는 사실을 (주어, 관계, 목적어)의 삼중항으로 나타내고, 개체를 node로, 관계를 방향과 종류가 있는 edge로 삼은 그래프다. (COVID-19, 증상, 발열)이 삼중항의 예다. Temporal knowledge graph는 각 사실에 그것이 성립하는 시점을 붙인 (주어, 관계, 목적어, 시점)의 사중항으로 이루어진다.

Embedding은 개체와 관계를 벡터로 나타내되, 참인 사실일수록 점수가 높아지도록 벡터를 학습하는 방법이다. 가장 단순한 TransE는 참인 삼중항에서 $\mathbf{h} + \mathbf{r} \approx \mathbf{t}$가 되도록, 곧 주어 벡터에 관계 벡터를 더하면 목적어 벡터 근처에 오도록 학습한다. 논문이 나열한 TKGE 기법 가운데 TTransE는 여기에 시점의 벡터 $\boldsymbol{\tau}$를 더해 $\mathbf{h} + \mathbf{r} + \boldsymbol{\tau} \approx \mathbf{t}$로 확장한 것이다. 학습된 벡터로 (주어, 관계, ?, 시점)의 빈칸에 가장 점수가 높은 개체를 찾는 것이 링크 예측이고, 그 시점이 미래이면 예측 문제가 된다. 본문에 적은 "관계들이 벡터의 상대적 위치를 정한다"는 말은 이 점수 함수를 가리킨다.

**Q. 예측 품질 지표들은 각각 어떤 성질을 갖는가.**

본문의 표에 적은 것 외에 알아 둘 성질을 정리한다.

- MSE와 RMSE는 오차를 제곱하므로 큰 오차 하나가 값을 크게 좌우한다. MSE를 최소화하는 예측은 조건부 평균이다.
- MAE는 오차에 비례해서만 커지므로 이상치에 덜 민감하다. MAE를 최소화하는 예측은 조건부 중앙값이다. 그래서 분포가 한쪽으로 치우친 데이터에서는 MSE로 고른 모델과 MAE로 고른 모델이 다를 수 있다.
- MAPE는 비율이어서 단위가 다른 시계열을 비교할 수 있지만 대칭적이지 않다. 음이 아닌 값을 예측할 때 과소 예측의 오차는 100%를 넘지 못하고 과대 예측의 오차는 상한이 없다. MAPE만으로 모델을 고르면 낮게 예측하는 모델이 유리해진다.
- sMAPE는 값이 0에서 200 사이로 제한되지만, 실제 값과 예측 값이 모두 0에 가까우면 여전히 불안정하다. 이름과 달리 완전히 대칭도 아니다. 실제 값이 100일 때 110으로 예측하면 $200 \times 10 / 210 \approx 9.5$, 90으로 예측하면 $200 \times 10 / 190 \approx 10.5$로, 같은 크기의 오차라도 과소 예측에 더 큰 값을 준다.
- MASE는 기준 예측의 MAE로 나누므로 단위와 척도에 영향을 받지 않고, 실제 값이 0이어도 발산하지 않는다. 척도가 다른 여러 시계열의 결과를 평균할 때 쓰기 좋다.

**Q. Layer normalization과 batch normalization은 무엇이 다른가.**

평균과 분산을 어느 축에 대해 구하느냐가 다르다. 둘 다 $\hat{x} = \gamma\, \dfrac{x - \mu}{\sigma} + \beta$의 형태이고 $\gamma$, $\beta$는 학습된다. 입력이 (batch, 토큰, feature) 모양일 때, batch normalization은 feature 차원 하나하나에 대해 batch 안의 모든 표본과 토큰에 걸쳐 $\mu$, $\sigma$를 구한다. 통계가 batch의 구성에 따라 달라지므로 학습 중에는 batch의 통계를, 추론 때에는 학습 중에 모아 둔 이동 평균을 쓴다. Layer normalization은 토큰 하나하나에 대해 그 토큰의 feature들에 걸쳐 $\mu$, $\sigma$를 구한다. 다른 표본과 무관하므로 batch 크기의 영향을 받지 않고 학습과 추론의 동작이 같다. 문장 길이가 제각각이고 padding이 섞이는 NLP에서는 batch 통계가 불안정해지기 쉬워서, Transformer는 보통 layer normalization을 쓴다. PatchTST 논문은 시계열 Transformer에서는 batch normalization이 layer normalization보다 낫다는 선행 연구(Zerveas et al., 2021)를 근거로 batch normalization을 쓴다고 밝힌다.

### 아직 남은 질문

- AR-Transformer가 temporal encoding과 positional encoding을 구체적으로 어떤 식으로 계산해 결합하는지는 원 논문을 확인하지 못했다. 위의 답은 서베이의 구분과 Informer의 구현을 근거로 한 것이다.
- 하나의 block이 시간과 공간 의존성을 함께 처리한다는 문장이 어느 모델을 가리키는지는, 서베이의 문장이 온전하지 않아 확정하지 못했다. 문장 앞뒤에 인용된 논문들을 직접 확인해야 한다.

## 참고 자료

- John A. Miller, Mohammed Aldosari, Farah Saeed, Nasid Habib Barna, Subas Rana, I. Budak Arpinar, Ninghao Liu. "A Survey of Deep Learning and Foundation Models for Time Series Forecasting." 2024. [arXiv:2401.13912](https://arxiv.org/abs/2401.13912)
- Xiaoyong Jin, Youngsuk Park, Danielle Maddix, Hao Wang, Yuyang Wang. "Domain Adaptation for Time Series Forecasting via Attention Sharing." ICML 2022. (본문의 domain adaptation 기법)
- Zhe Li, Zhongwen Rao, Lujia Pan, Zenglin Xu. "MTS-Mixers: Multivariate Time Series Forecasting via Factorized Temporal and Channel Mixing." arXiv:2302.04501, 2023.
- Vijay Ekambaram, Arindam Jati, Nam Nguyen, Phanwadee Sinthong, Jayant Kalagnanam. "TSMixer: Lightweight MLP-Mixer Model for Multivariate Time Series Forecasting." arXiv:2306.09364, 2023. (서베이가 인용한 TSMixer)
- Si-An Chen, Chun-Liang Li, Nate Yoder, Sercan O. Arik, Tomas Pfister. "TSMixer: An All-MLP Architecture for Time Series Forecasting." 2023. [arXiv:2303.06053](https://arxiv.org/abs/2303.06053) (이름이 같은 다른 모델)
- Peiwang Tang, Xianchao Zhang. "MTSMAE: Masked Autoencoders for Multivariate Time-Series Forecasting." ICTAI 2022.
- Xiaochen Zheng, Xingyu Chen, Manuel Schürch, Amina Mollaysa, Ahmed Allam, Michael Krauthammer. "SimTS: Rethinking Contrastive Representation Learning for Time Series Forecasting." arXiv:2303.18205, 2023.
- Yuqi Nie, Nam H. Nguyen, Phanwadee Sinthong, Jayant Kalagnanam. "A Time Series is Worth 64 Words: Long-term Forecasting with Transformers." ICLR 2023. [arXiv:2211.14730](https://arxiv.org/abs/2211.14730) (PatchTST)
- George Zerveas 외. "A Transformer-based Framework for Multivariate Time Series Representation Learning." KDD 2021. (PatchTST가 batch normalization의 근거로 인용한 연구)
- Haoyi Zhou 외. "Informer: Beyond Efficient Transformer for Long Sequence Time-Series Forecasting." AAAI 2021. [arXiv:2012.07436](https://arxiv.org/abs/2012.07436)
