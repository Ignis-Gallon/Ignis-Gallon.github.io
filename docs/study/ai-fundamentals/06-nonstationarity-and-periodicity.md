---
title: "시계열의 비정상성과 주기성 다루기: 분해, Non-stationary Transformer, TimesNet"
date: 2024-11-28
last_modified_at: 2024-11-29
category: study
tags: [시계열 예측, 정상성, RevIN, Non-stationary Transformer, TimesNet, FFT, 시계열 분해]
---

# 시계열의 비정상성과 주기성 다루기: 분해, Non-stationary Transformer, TimesNet

학부 수업 프로젝트에서 PatchTST의 입력에 세 가지 처리(이동평균 분해, non-stationary 정보의 반영, Top-K FFT 분해)를 적용해 본 적이 있다. 실험의 구성과 결과는 [PatchTST 성능 개선 실험](../../projects/ai-course/03-patchtst.md)에 정리했다. 이 글은 그때 쓴 방법들을 다시 들여다보며, 각 방법의 바탕이 된 개념과 논문(Non-stationary Transformers, TimesNet)을 리뷰 영상과 함께 공부해 정리한 것이다. 왜 그 방법이 효과가 있었는지에 대한 내 생각과, 공부하며 생긴 질문도 함께 적었다.

## 1. 이동평균 분해

이동평균 분해는 시계열을 추세, 계절성, 잔차 같은 성분으로 나누는 기법이다. 프로젝트에서는 이동평균으로 구한 trend와 그 나머지(remainder) 둘로만 나누어, 서로 독립적인 두 모델에 넣어 학습시키고 두 출력을 더해 최종 예측을 만들었다. DLinear에서 제시된 아이디어다.

이동평균에는 몇 가지 종류가 있다.

1. Simple Moving Average: window 안의 값을 같은 비중으로 평균한다. 프로젝트에서 쓴 방식이다.
2. Weighted Moving Average: 시점마다 다른 가중치를 준다.
3. Exponential Moving Average: 최근 값일수록 큰 가중치를 주고 과거로 갈수록 지수적으로 줄인다.

주의할 점도 있다.

- Window의 크기에 따라 결과가 크게 달라질 수 있다.
- 비선형 추세가 있거나 변동성이 큰 데이터에는 잘 맞지 않을 수 있다. 주식 가격처럼 급격한 상승과 하락이 반복되거나, 계절적 패턴과 주기적 패턴이 섞여 있는 경우다.

**왜 이동평균 분해를 적용하면 성능이 좋아지는가.** 추세와 잔차를 나누어 학습시키면 모델이 데이터를 받아들이기 쉬워진다고 판단했다. 한 모델은 추세만 파악하고 다른 모델은 그 밖의 것을 학습하므로, 각 모델이 풀어야 할 문제의 난이도가 낮아진다. 어떤 이동평균을 쓰느냐(단순, 가중, 지수 가중)에 따라 성능이 달라지는 것도 볼 수 있었다.

## 2. 비정상성과 정규화

### 정상성

정상성(stationarity)을 가진 시계열은 평균이나 분산 같은 통계적 특성이 시점에 따라 변하지 않고, 추세나 계절성이 없다. 과거, 현재, 미래의 분포가 시점에 무관하게 같다는 뜻이다. 반대로 비정상(non-stationary) 시계열은 통계적 특성이 시간에 따라 변하고 추세와 계절성을 품고 있다. 추세와 계절성은 서로 다른 시점의 값에 영향을 주므로, 시간에 따른 값의 등락을 만들어 내는 주된 요인이다. 정상성의 정의와 검정 방법은 [정상성과 단위근 검정](../time-series-statistics/02-stationarity-and-unit-root-test.md)에서 따로 다루었다.

데이터의 평균과 분산이 계속 변하면 모델은 데이터의 장기적인 경향을 학습하기 어렵다. 그래서 입력 구간마다 정규화(instance normalization)를 해서 구간 사이의 분포 차이를 줄인다. PatchTST도 입력 시퀀스를 RevIN과 비슷한 방식으로 정규화하고, 출력 layer에서 같은 평균과 표준편차로 되돌린다(de-normalization).

### RevIN

RevIN(Reversible Instance Normalization)은 정규화와 역정규화를 짝으로 써서 데이터의 비정상성에 대응한다.

1. 원래 분포를 가진 입력 시퀀스가 들어오면 변수별로 instance normalization을 한다. 이때 쓴 평균 $\mu$와 표준편차 $\sigma$를 저장해 둔다. 학습 가능한 affine 파라미터 $\gamma$, $\beta$도 둔다.
2. 정규화된 입력, 곧 비정상성 정보가 제거된 입력을 모델에 넣어 예측을 얻는다. 이 예측도 비정상성이 제거된 분포 위에 있다.
3. 저장해 둔 $\mu$와 $\sigma$로 예측을 역정규화해 원래 분포의 예측값을 얻는다.

### Over-stationarization

문제는 실제 시계열이 거의 대부분 비정상이라는 점이다. 정규화로 시계열을 정상에 가깝게 만들면, 데이터가 원래 갖고 있던 비정상성 정보가 훼손되어 모델이 temporal dependency를 모델링하는 능력이 떨어질 수 있다. 출력에서 통계량을 되돌려 준다고 해도 원래 시계열의 비정상성이 de-normalization만으로 완전히 회복되지는 않는다.

이 영향은 attention에서도 나타난다. 비정상 시계열을 잘라 정규화하면 조각들이 모두 같은 평균과 분산을 갖게 되고, 원래 데이터에 비해 서로 비슷한 분포가 된다. 그러면 모델은 사건이 거의 없는 밋밋한 출력을 내고, 원래 데이터의 자연스러운 비정상성과 맞지 않게 된다. 내 식으로 풀어 쓰면 이렇다. 정규화를 거치며 비정상성이 이미 어느 정도 사라졌으므로, 모델은 그 데이터를 비정상성이 없는 데이터로 보고 attention을 계산한다. 그 결과 비정상성은 더 사라진다.

이 현상을 over-stationarization이라고 부른다. 따라서 모델이 비정상성을 반영할 수 있게 해 주는 것이 중요하다.

### Non-stationary Transformer

이 문제를 정면으로 다룬 논문이 "Non-stationary Transformers: Exploring the Stationarity in Time Series Forecasting"(NeurIPS 2022)이다.

**기존 연구의 한계.**

1. Transformer 기반 예측 모델들은 구조를 고도화하는 데 집중했다. 시계열에 맞는 attention을 제안했지만 비정상성에 대한 고려는 부족했다.
2. 비정상성 정보를 모델링에 활용하지 않았다. RevIN처럼 입력 쪽에서 비정상성을 다루는 연구는 많았지만, 정규화로 정보를 일괄 제거하면 attention에서 over-stationarization이 생긴다. Attention 구조 자체를 바꿔 비정상성을 고려하려는 시도는 당시(2022년)에는 많지 않았다.

논문의 첫 그림이 이 문제를 보여 준다. 원래의 비정상 시계열로 학습한 vanilla Transformer의 attention은 시계열마다 다른 temporal dependency를 반영한다. 같은 모델에 정규화한 시계열을 넣으면 서로 다른 시계열에 대한 attention이 비슷비슷해져 temporal dependency가 제대로 반영되지 않는다. Non-stationary Transformer의 attention은 원래 시계열에서 얻은 것에 다시 가까워진다.

**방법.** 길이 $S$, 변수 $C$개의 look-back window $x$가 입력이다.

1. **Normalization.** Window의 통계량으로 입력을 정규화한다. 평균과 표준편차를 구해 적용하는 일반적인 정규화다.

    $$
    x' = \frac{x - \mu_x}{\sigma_x}
    $$

2. **De-stationary Attention.** Attention은 이미 비정상성이 제거된 $x'$의 embedding으로 계산되므로, 출력에서 de-normalization을 하는 것만으로는 비정상성을 정확히 반영할 수 없다. 그래서 query와 key의 곱을 scaling하는 자리에 두 값 $\tau$와 $\Delta$를 넣는다.

    $$
    \mathrm{Attn}(Q', K', V', \tau, \Delta) = \mathrm{Softmax}\left( \frac{\tau\, Q' K'^{\top} + \mathbf{1}\Delta^{\top}}{\sqrt{d_k}} \right) V'
    $$

    $$
    \log \tau = \mathrm{MLP}(\sigma_x, x), \qquad \Delta = \mathrm{MLP}(\mu_x, x)
    $$

    $Q'$, $K'$, $V'$은 정규화된 입력에서 만든 query, key, value다. $\tau$와 $\Delta$는 정규화 전의 통계량($\sigma_x$, $\mu_x$)과 원래 입력 $x$를 받는 MLP의 출력이다. 값 자체가 고정된 파라미터인 것은 아니고, 이 값을 만들어 내는 MLP가 학습된다. 여기서 MLP는 attention과 별개로 둔 작은 feed-forward network라고 보면 된다.

3. **De-normalization과 예측.** Encoder의 출력을 decoder에 넣어 예측 길이만큼 forecasting하고, 저장해 둔 통계량으로 출력을 되돌린다.

정규화와 역정규화를 묶어 series stationarization이라고 부르고, 이 구조는 Transformer encoder를 쓰는 기존 모델들에 model-agnostic하게 붙일 수 있다는 것이 논문의 주장이다. 의도를 한 줄로 쓰면, 정규화된 입력으로 계산하는 attention이 원래의 비정상 시계열에서 학습했을 attention에 가까워지도록 근사하는 것이다.

**실험에서 눈에 띈 점.** 논문은 데이터셋의 정상성 정도를 ADF(Augmented Dickey-Fuller) 검정 통계량으로 제시한다. 값이 작을수록 정상에 가깝다. ILI 데이터셋은 비정상성이 높게 나왔는데, 이것이 Non-stationary Transformer가 ILI에서 높은 성능을 보인 이유라고 생각했다.

## 3. 주파수 영역에서 보기

### Top-K FFT 분해

프로젝트에서 적용한 세 번째 방법은 입력을 주요 주파수 성분과 잔차로 나누는 것이다.

1. 입력 시퀀스에 FFT를 적용한다.
2. Frequency domain에서 amplitude가 가장 큰 주파수 $K$개를 그 시퀀스의 주요 성분으로 보고, 나머지 주파수는 0으로 만든다.
3. 역변환으로 시간 영역의 신호를 얻는다.
4. 원래 시퀀스에서 이 신호를 빼서 remainder를 구한다.
5. 두 신호를 각각 별도의 PatchTST에 넣어 장기 예측을 하고 결과를 더한다.

아이디어는 TimesNet(ICLR 2023)에서 얻었다. TimesNet은 시계열의 multi-periodicity를 반영하기 위해 입력에 FFT를 적용해 주기를 찾는다. 시계열을 frequency domain에서 보면 데이터의 주요한 정보를 얻을 수 있다는 것을 보여 주는 사례다.

### TimesNet의 개요

"TimesNet: Temporal 2D-Variation Modeling for General Time Series Analysis"의 요점은 두 가지다.

- Multi-periodicity를 반영하기 위해 시간에 따른 변화(temporal variation)를 intraperiod-variation과 interperiod-variation으로 나누어 본다.
- 두 variation을 2D tensor의 형태로 표현해 함께 모델링한다.

### 배경: 시계열의 성분과 Fourier transform

시계열의 성분은 흔히 넷으로 나눈다.

| 성분 | 내용 |
|---|---|
| 추세(trend) | 데이터가 장기적으로 증가하거나 감소하는 방향성 |
| 계절성(seasonality) | 일정한 빈도로 반복되는 패턴 |
| 주기성(cycle) | 고정된 빈도가 아닌 형태로 나타나는 증가와 감소의 패턴 |
| 불규칙 요소(residual) | 설명할 수 없는 임의의 변동. 돌발적인 요인으로 생긴다 |

용어 하나를 짚어 둔다. 위 표의 "주기성(cycle)"은 간격이 일정하지 않은 등락을 가리킨다. 반면 TimesNet이 말하는 period는 FFT로 찾은 주파수에 대응하는, 길이가 정해진 반복이다. 노트에는 두 가지를 섞어 적었는데, TimesNet의 periodicity는 표에서는 계절성 쪽에 더 가깝다.

Fourier transform은 원래 신호 처리의 도구였지만 지금은 이미지 처리, 자연어 처리 등 여러 분야에서 쓰인다. Time domain의 신호를 여러 개의 주기 함수(sin과 cos)로 분해해, 각 주파수 성분이 얼마나 강하게 들어 있는지(amplitude)와 어느 위치에서 시작하는지(phase)를 구한다. 출발점은 주기 함수를 sin과 cos의 합으로 쓰는 Fourier series다.

$$
f(t) = a_0 + \sum_{n=1}^{\infty} \big( a_n \cos n\omega t + b_n \sin n\omega t \big)
$$

$a_0$는 0을 중심으로 진동하지 않는 신호를 표현하기 위한 상수항이고, $a_n$, $b_n$은 각 주파수 성분의 세기다. 이 식을 한 주기에 대해 적분하면 sin과 cos의 직교성으로 계수를 구하는 식을 얻고, Euler 공식으로 cos과 sin을 복소 지수로 바꾸면 이산 신호에 대한 변환 식이 나온다. FFT는 이 이산 Fourier 변환을 빠르게 계산하는 알고리즘이다.

$$
Y(k) = \sum_{n=0}^{N-1} y(n)\, e^{-i \frac{2\pi}{N} kn}, \qquad
y(n) = \frac{1}{N} \sum_{k=0}^{N-1} Y(k)\, e^{\,i \frac{2\pi}{N} kn}
$$

Fourier transform을 조금 더 자세히 본 내용은 [푸리에 변환 메모](../time-series-statistics/09-fourier-transform.md)에 있다.

### 왜 1D로는 부족한가

- 시계열은 시간 순서대로 이어지고, 각 시점은 scalar 값 하나만 가진다.
- 시점 하나의 scalar 값만으로는 분석에 필요한 의미 정보를 충분히 얻을 수 없다. 연속성, 주기성, 추세 같은 시계열 고유의 속성을 반영해 모델링해야 한다.
- 실제 데이터에서는 상승, 하락, 변동 같은 여러 variation이 겹쳐서 복잡한 패턴을 만든다. 이 variation들을 잘 잡아 representation을 뽑는 것이 중요하다.

TimesNet은 지금까지 대부분의 모델이 입력으로 써 온 1D 시계열을, multi-periodicity를 기준으로 다른 차원에서 다시 보는 것으로 이 문제를 풀려고 한다.

### Multi-periodicity

Multi-periodicity는 시계열 하나에도 여러 주기가 함께 있다는 뜻이다. 기온은 하루를 주기로 변하지만 크게 보면 일 년을 주기로도 변한다. 한 시점은 인접한 구간의 패턴에도 영향을 받지만 이웃한 주기의 변화에도 크게 영향을 받는다. 여러 주기가 겹치고 상호작용하기 때문에 variation을 모델링하기가 복잡해진다. 실제 시계열의 상당수가 이런 성질을 가지므로 이를 고려하는 것이 중요하다.

!!! question "Q. 장기 예측(LTSF)이어서 FFT가 잘 듣는 것은 아닐까?"
    긴 기간의 측정 데이터일수록 주기가 더 많이, 더 분명하게 나타날 것이다. 그 주기를 집중적으로 본다는 것은 데이터의 비정상성을 주기 단위로 파악하는 일이라고 해석할 수도 있다. 그렇다면 locality를 잘 학습하는 것만으로는 부족하고, 데이터 자체에 내재된 주기를 잘 파악하는 것이 함께 필요하다.

TimesNet은 변화를 두 종류로 나눈다.

- **Intraperiod-variation**: 한 주기 안에서 인접한 시점들 사이의 변화다. 짧은 범위의 패턴이고, 1D 시계열을 입력으로 모델링할 때 보통 고려하는 것이 이것이다.
- **Interperiod-variation**: 서로 다른 주기에 있지만 위상(phase)이 같은 시점들 사이의 변화다. 긴 범위의 패턴을 반영한다.

1D 시계열을 주기 길이마다 잘라 나란히 세우면 2D tensor가 된다. 한 열에는 한 주기 안의 시점들이 들어가고(intraperiod), 한 행에는 주기는 다르지만 위상이 같은 시점들이 들어간다(interperiod). 두 방향을 함께 보면 인접한 시점의 변화와, 떨어져 있지만 같은 위상에 있는 시점의 변화를 동시에 잡을 수 있다.

### FFT로 주기 찾기

눈에 띄는 주기가 없으면 주기를 직접 찾기 어렵다. 그래서 입력 시퀀스에 FFT를 적용한다.

1. FFT로 주파수별 amplitude를 구한다. 모든 채널($d_{model}$개)에 대해 계산한 뒤 평균해 주파수마다 값 하나를 얻는다.
2. Amplitude가 가장 큰 주파수 $k$개 $\{f_1, \dots, f_k\}$를 고른다. 주파수 영역은 sparse하므로, 의미 없는 고주파가 만드는 잡음을 피하려고 상위 $k$개만 쓴다.
3. 각 주파수에 대응하는 주기를 구한다.

    $$
    p_i = \left\lceil \frac{T}{f_i} \right\rceil, \qquad i \in \{1, \dots, k\}
    $$

상위 $k$개를 고른다는 것은 원래 신호를 만드는 데 가장 크게 기여한 성분만 고른다는 뜻이다. 그 성분의 주파수를 알면 원래 신호에서 어떤 주기로 나타나는지를 알 수 있다.

!!! question "Q. 주기는 왜 전체 길이를 주파수로 나눈 값인가?"
    여기서 주파수 $f$는 길이 $T$의 구간 안에서 패턴이 반복되는 횟수다. 주파수가 1이면 구간 동안 한 번, 2이면 두 번 반복된다. 따라서 주기의 길이를 $p$라고 하면 $p \cdot f = T$다. $T = 10$이고 $f = 2$라면 $p = 5$다. 나누어떨어지지 않는 경우를 위해 올림을 한다.

!!! question "Q. Amplitude의 평균은 어느 축으로 구하는가?"
    길이 $T$, 채널 $d_{model}$개의 입력이 있을 때, 채널마다 시간 축으로 FFT를 적용해 주파수별 amplitude를 얻고, 그 값을 채널 축으로 평균한다고 이해했다. 논문의 식 $A = \mathrm{Avg}(\mathrm{Amp}(\mathrm{FFT}(X_{1D})))$와 맞는 해석이다.

### 2D로 바꾸기

주기 $p_i$가 정해지면 1D 표현을 2D로 바꾼다.

$$
X_{2D}^{l,i} = \mathrm{Reshape}_{p_i, f_i}\big(\mathrm{Padding}(X_{1D}^{l-1})\big), \qquad i \in \{1, \dots, k\}
$$

- 길이 $T$가 $p_i$로 나누어떨어지지 않으면 padding을 붙여 shape을 맞춘다.
- $T \times d_{model}$의 데이터가 $p_i \times f_i \times d_{model}$이 된다. 행이 $p_i$개, 열이 $f_i$개다.
- 주파수마다 주기가 다르므로 서로 다른 모양의 2D tensor가 $k$개 만들어진다.

이렇게 만든 2D tensor에 2D convolution을 적용해 두 방향의 variation을 함께 뽑는다. 노트는 여기까지 정리했다. 영상의 구조도를 보면 그 뒤에 결과를 다시 1D로 되돌리고 $k$개의 결과를 합치는 단계가 이어지며, 이 묶음이 TimesBlock 하나다. TimesBlock은 residual connection으로 여러 층 쌓인다.

### Embedding

TimesBlock에 들어가기 전의 embedding은 세 가지를 더해서 만든다.

1. Positional embedding: 시퀀스 안에서의 위치 정보를 반영한다. 일반적인 Transformer의 positional encoding을 쓴다.
2. Token embedding: 입력을 $d_{model}$ 차원으로 projection한다. 다변량 입력 $T \times C$가 $T \times d_{model}$이 된다.
3. Temporal embedding: 데이터에 시간 정보(월, 요일, 시각 등)가 들어 있을 때 그것을 함께 학습에 쓴다. 시간 정보가 있는 데이터에만 적용한다.

노트에는 "element-wise로 더하는가?"라고 적어 두었는데, 영상의 그림에서 세 embedding은 같은 위치끼리 더해진다.

### 왜 FFT 기반 분해가 효과가 있었을까

프로젝트를 돌아보며, TimesNet에서 가져온 FFT 방식의 개선이 가장 컸던 이유를 다음과 같이 생각했다. 실제 결과는 데이터셋에 따라 달랐고, 표는 [프로젝트 글](../../projects/ai-course/03-patchtst.md)에 있다.

TimesNet은 FFT로 주기를 파악하고 그것을 중심으로 학습한다. 이런 주기야말로 데이터에 비정상성을 만들어 내는 가장 큰 요인일 수 있다. FFT로 주요한 성분만 뽑아 주면 모델이 그 부분에 집중하기 쉬워지고, 나머지를 따로 분해해 학습시키면 trend, seasonality, residual까지 나누어 학습하는 효과가 난다. 이것이 앞에서 본 non-stationary 정보의 반영과 비슷한 효과를 냈다고 생각한다. 그리고 무엇보다 분해를 통해 학습 난이도를 낮춰 준 것이 성능 향상의 가장 큰 이유라고 본다.

## 4. Batch를 섞어도 되는가

시계열 예측 모델을 학습시킬 때 학습 데이터를 shuffle하는 것이 괜찮은지도 따져 보았다. 예전에 기업 데이터로 시계열 예측을 해 본 과제에서 학습 데이터를 shuffle했던 것이 계절성 학습에 문제가 되지 않았는지 돌아본 것이다.

걱정되는 점은 이렇다. Shuffle을 하면 한 batch 안에 서로 다른 시기의 데이터가 섞인다. 여름의 데이터와 겨울의 데이터는 분포가 완전히 다를 수 있는데, 이것을 한 batch에 넣어 한꺼번에 학습시키면 모델이 시기의 차이에서 오는 특성, 곧 계절성을 놓칠 가능성이 있다. 다만 섞이는 것은 window 단위이고 window 안의 시간 순서는 그대로라는 점은 구분해 두어야 한다.

반대쪽의 생각도 있다.

- 단기 예측이라면 장기적인 패턴을 학습할 필요가 크지 않을 수 있고, 그렇다면 shuffle이 큰 문제를 만들지 않을 수 있다.
- Shuffle을 하지 않으면 모델이 데이터가 들어오는 특정한 순서에 의존해 학습할 수 있다.
- Shuffle을 하면 batch마다 데이터 분포가 고르게 섞여 불균형이 해소되고, 그 덕분에 성능이 좋아진 것일 수도 있다.

그 과제는 pilot 시스템이자 prototype이어서 대략적인 성능을 확인하는 것이 목적이었고, 학습 데이터가 매우 적어 단순한 LSTM 구조를 썼다. 무엇보다 데이터의 기간이 계절성을 확인할 수 있을 만큼 길지 않았다. 그래서 위의 어느 쪽이 맞는지는 그 데이터로 가릴 수 없었다. 당시 XAI를 적용해 모델의 동작을 볼 수 있었다면 입력 feature를 더 잘 이해하고 디버깅할 수 있었을 것이라는 아쉬움도 남았다.

## 남은 질문

- 계절성이 뚜렷하고 기간이 긴 데이터에서 shuffle 여부가 실제로 예측 성능에 차이를 만드는가.
- FFT로 주요 성분을 떼어 내는 것과 정규화 전의 통계량을 attention에 넣어 주는 것이 비슷한 효과를 낸다는 생각은 추측이다. 두 방법을 같은 데이터에서 직접 비교해 확인해 볼 필요가 있다.

## 참고 자료

- Y. Liu, H. Wu, J. Wang, M. Long, "Non-stationary Transformers: Exploring the Stationarity in Time Series Forecasting", NeurIPS 2022. <https://arxiv.org/abs/2205.14415>
- H. Wu, T. Hu, Y. Liu, H. Zhou, J. Wang, M. Long, "TimesNet: Temporal 2D-Variation Modeling for General Time Series Analysis", ICLR 2023. <https://arxiv.org/abs/2210.02186>
- T. Kim, J. Kim, Y. Tae, C. Park, J.-H. Choi, J. Choo, "Reversible Instance Normalization for Accurate Time-Series Forecasting against Distribution Shift", ICLR 2022.
- A. Zeng, M. Chen, L. Zhang, Q. Xu, "Are Transformers Effective for Time Series Forecasting?" (DLinear), AAAI 2023. <https://arxiv.org/abs/2205.13504>
- 서울대학교 산업공학과 DSBA 연구실, "[Paper Review] Non-stationary Transformers: Exploring the Stationarity in Time Series Forecasting" (YouTube). <https://www.youtube.com/watch?v=HXOhp75E6cg>
- 서울대학교 산업공학과 DSBA 연구실, "[Paper Review] TimesNet: Temporal 2D-Variation Modeling for General Time Series Analysis" (YouTube). <https://www.youtube.com/watch?v=q7U1LX2BdOA>
