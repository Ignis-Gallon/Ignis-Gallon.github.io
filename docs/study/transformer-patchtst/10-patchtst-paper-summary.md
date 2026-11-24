---
title: "PatchTST 논문 요약"
date: 2023-11-26
last_modified_at: 2023-11-26
category: study
tags: [PatchTST, 시계열 예측, Transformer, Patching, Channel Independence]
---

# PatchTST 논문 요약

[PatchTST 성능 개선 프로젝트](../../projects/ai-course/03-patchtst.md)를 진행하면서 "A Time Series is Worth 64 Words: Long-term Forecasting with Transformers"(ICLR 2023)를 처음 읽고 절 순서대로 요약한 노트이다. 논문의 흐름을 옮기는 사이사이에 용어 풀이와, 행렬의 shape을 직접 따져 본 내용을 함께 적었다. 노트는 지도 학습 결과 표에서 끝난다.

같은 논문을 1년 뒤에 선행 연구와 함께 다시 정리한 글은 [PatchTST 리뷰](../../papers/2024-11-25-patchtst.md)에 있다.

## Abstract

다변량 시계열 예측과 self-supervised representation learning을 위한 Transformer 기반 모델의 효율적인 설계를 제안한다. 이 설계는 두 가지에 집중한다.

1. 시계열을 subseries 수준의 patch로 나누어 Transformer의 입력 token으로 넣는다.
2. channel-independence. 하나의 Transformer에 단변량 시계열을 channel별로 따로 넣는다.

patching에는 세 가지 이점이 있다.

1. embedding에 local semantic information이 유지된다.
2. 같은 look-back window에서 attention을 계산하는 token 수가 줄어들기 때문에, attention map에 드는 계산량과 메모리가 줄어든다.
3. 모델이 더 긴 과거(history)를 참고할 수 있다.

여기서 look-back window는 모델에 넣는 시계열의 길이를 말한다.

## Introduction

예측은 시계열 분석에서 가장 중요한 task 가운데 하나이고, 딥러닝과 representation learning은 예측에서 뛰어난 성능을 보여 왔다.

Transformer는 sequence의 원소들 사이의 관계를 자동으로 학습하는 attention mechanism 덕분에 자연어 처리, 컴퓨터 비전, 음성, 시계열 등 여러 분야에서 좋은 성능을 보였다. Informer, Autoformer, FEDformer는 Transformer를 시계열에 성공적으로 적용한 모델들이다. 그런데 이 모델들의 복잡한 설계에도 불구하고, 아주 단순한 linear 모델이 예측에서 더 좋은 성능을 보였다는 결과가 발표되었다. 그래서 Transformer가 시계열 예측에 정말 효과적인지에 대한 의문이 제기되었다.

논문은 두 가지 핵심 개념을 제안하며 이 물음에 답한다.

### Patching

시계열 예측의 목표는 서로 다른 time step의 데이터 사이의 상관관계를 파악하는 것이다. 그런데 time step 하나는 문장 속의 단어와 달리 그 자체로는 의미(semantic meaning)를 갖지 않는다. 그래서 데이터에서 local한 의미 정보를 추출하는 것이 꼭 필요하다.

이전 연구들은 대부분 point-wise input token만 쓰거나, 시계열에서 손으로 설계한 정보만 활용했다. 이 논문은 time step들을 subseries 수준의 patch로 묶어, point 단위에서는 알아내기 어려웠던 locality를 높이고 포괄적인 의미 정보를 잡아내려 한다.

여기서 locality는 시계열의 패턴, 추세, 특성이 한 시점이 아니라 일정한 구간에 걸쳐 나타나는 성질을 말한다.

### Channel-independence

다변량 시계열은 여러 channel로 이루어진 신호이다. Transformer에 들어가는 input token은 channel 하나의 정보로 만들 수도 있고 여러 channel의 정보로 만들 수도 있으며, token을 어떻게 설계하느냐에 따라 여러 변형이 나온다.

- **Channel-mixing**: 한 시점의 모든 channel 값을 하나의 벡터로 묶어 input token으로 삼고, 이것을 embedding 공간으로 projection해 정보를 섞는다.
- **Channel-independence**: input token 하나가 channel 하나의 정보만 갖는다.

channel-independence는 CNN과 linear 모델에서는 잘 동작한다는 것이 알려져 있었지만, Transformer 기반 모델에는 적용된 적이 없었다.

### 이 설계의 장점

논문은 Table 1의 사례 연구를 들어 세 가지 장점을 설명한다.

**1. 복잡도 감소.** 기본 Transformer는 attention 때문에 input token 수 $N$에 대해 $O(N^2)$의 복잡도를 갖는다. 전처리를 하지 않으면 $N$은 입력 sequence의 길이 $L$과 같아서 계산 자원이 많이 든다. patching을 적용하면 $N$을 대략 $L/S$로 줄일 수 있다. $S$는 patch 사이의 간격인 stride이다. Table 1의 예에서는 큰 데이터셋에서 학습 시간이 최대 22배 줄었다.

**2. 더 긴 look-back window에서 학습할 수 있다.** Table 1에서 look-back window $L$을 96에서 336으로 늘리면 MSE가 0.518에서 0.397로 줄어든다. 그러나 단순히 $L$을 늘리면 메모리와 계산량이 그만큼 더 든다.

시계열에는 시간적으로 중복된 정보가 많아서, 이전 연구들은 downsampling이나 sparse한 attention 연결로 일부 값을 버렸고, 그렇게 해도 모델은 예측에 충분한 정보를 얻을 수 있었다. 논문은 $L = 380$인 시계열을 4 step마다 sampling하고 마지막 한 점을 추가해 input token 수를 $N = 96$으로 맞춘 경우를 실험했다($380 / 4 = 95$에 1개를 더한 것이다). 이때의 MSE는 0.447로, 가장 최근 96 step을 그대로 넣었을 때의 0.518보다 낮았다. input token의 수가 같더라도 look-back window가 길수록 더 중요한 정보를 담고 있다는 뜻이다.

여기서 질문이 나온다. look-back window는 길게 유지하면서 값을 버리지 않을 방법은 없을까? patching이 그 대안이다. patching은 비슷한 값을 갖는 이웃 time step들을 묶어 input token 수를 줄이므로 계산량이 줄어든다. Table 1에서 $L = 336$일 때 patching을 쓰면 MSE가 0.397에서 0.367로 더 낮아진다.

**3. representation learning이 가능하다.** 강력한 self-supervised learning 기법이 등장하면서, 데이터의 추상적인 representation을 잡아내려면 비선형 layer를 여러 겹 가진 정교한 모델이 필요해졌다. linear 모델 같은 단순한 모델은 표현력에 한계가 있어 이런 작업에 적합하지 않다. PatchTST는 Transformer가 시계열 예측에 효과적이라는 것을 확인하는 데 그치지 않고, 예측 성능을 더 높일 수 있는 representation 능력이 있음을 보인다. Table 1에서 PatchTST는 self-supervised 방식으로 가장 낮은 MSE인 0.349를 얻었다.

## Related Work

### Transformer 기반 모델의 patch

Transformer는 여러 종류의 데이터에서 잠재력을 보여 왔다. 그 모든 경우에서, local semantic information이 중요할 때는 patching이 필수적으로 쓰였다. 자연어 처리의 BERT는 문자 단위가 아니라 subword 단위로 tokenization을 한다. 컴퓨터 비전의 Vision Transformer는 이미지를 16×16 patch로 나누어 모델에 넣는다. 이후의 영향력 있는 연구인 BEiT와 masked autoencoder도 모두 patch를 입력으로 쓴다.

정리하면, 다른 분야에서는 Transformer에 patch를 적용하는 것이 이미 일반적이었다.

### Transformer 기반 장기 시계열 예측

최근 Transformer를 장기 시계열 예측(LTSF)에 쓰려는 연구가 많았다.

- **LogTrans**: LogSparse 설계를 가진 convolutional self-attention layer를 써서 local 정보를 잡고 공간 복잡도를 줄인다.
- **Informer**: ProbSparse self-attention을 제안한다.
- **Autoformer**: 전통적인 시계열 분석 방법에서 decomposition과 auto-correlation을 가져온다.
- **FEDformer**: Fourier 변환을 이용해 선형 복잡도를 얻는다.
- **Pyraformer**: pyramidal attention module로 선형 복잡도를 얻는다.

이 모델들은 대부분 기존 attention mechanism의 복잡도를 줄이는 새 방법을 설계하는 데 초점을 두었고, 그 결과 예측 길이가 길 때 특히 더 좋은 성능을 얻었다. 그러나 대부분 point-wise attention을 쓰며, patch의 중요성은 다루지 않는다.

- LogTrans는 Query와 Key 사이의 point-wise dot product를 피하지만, Value는 여전히 time step 하나를 기준으로 한다.
- Autoformer는 auto-correlation으로 patch 수준의 연결을 얻지만, 손으로 설계한 방식이어서 patch 안의 의미 정보를 모두 담지는 못한다.
- Triformer는 patch attention을 제안하지만, 목적이 복잡도를 줄이는 데 있다. patch를 입력의 단위로 다루지 않고, 그 뒤에 있는 의미적 중요성도 드러내지 않는다.

### 시계열 representation learning

지도 학습 외에 self-supervised learning도 주목받는 주제이다. downstream task에 쓸 수 있는 유용한 representation을 학습할 가능성을 보여 주었기 때문이다. 최근 Transformer를 쓰지 않는 시계열 representation 학습 방법이 많이 제안되었다. 한편 Transformer는 foundation 모델과 범용 representation 학습에 적합한 구조로 여겨졌고 시계열에서도 여러 시도가 있었지만, 그 잠재력이 아직 충분히 실현되지는 못했다.

이 절을 읽으며 찾아본 용어는 다음과 같다.

- **전이 학습(transfer learning)**: 모델을 처음부터 새로 학습시키지 않고, 미리 학습된 모델에서 출발해 학습하는 것
- **upstream**: 사전학습(pre-training) 단계. 미리 학습해 두는 모델
- **downstream**: 최종적으로 풀고자 하는 task. 사전학습된 모델을 가져와 만드는 모델

## Proposed Method

### 모델 구조

다변량 시계열 sample의 look-back window 길이가 $L$이고($x_1, \dots, x_L$), 각 time step의 $x_t$는 $M$차원 벡터이다. 여기서 미래의 $T$개 값($x_{L+1}, \dots, x_{L+T}$)을 예측하는 것이 목표이다. PatchTST는 vanilla Transformer encoder를 핵심 구조로 쓴다.

### Forward 과정

time index 1에서 시작하는 길이 $L$의 $i$번째 단변량 시계열을 다음과 같이 쓴다. 변수가 $M$개이므로 $i = 1, \dots, M$이다.

$$
x^{(i)}_{1:L} = \left(x^{(i)}_1, \dots, x^{(i)}_L\right)
$$

입력 $(x_1, \dots, x_L)$은 $M$개의 단변량 시계열 $x^{(i)} \in \mathbb{R}^{1 \times L}$로 나뉘고, 각각이 Transformer backbone에 독립적으로 들어간다. 구조도에서 데이터가 거치는 단계와 그때의 shape은 다음과 같다.

| 단계 | 기호 | shape |
|---|---|---|
| 입력 단변량 시계열 | $x^{(i)}$ | $1 \times L$ |
| instance normalization과 patching | $x^{(i)}_p$ | $P \times N$ |
| projection과 position embedding | $x^{(i)}_d$ | $D \times N$ |
| Transformer encoder | $z^{(i)}$ | $D \times N$ |
| flatten과 linear head | $\hat{x}^{(i)}$ | $1 \times T$ |

Transformer는 결과적으로 $L$ 이후의 $T$ step에 대한 해당 변수의 예측을 내놓는다.

$$
\hat{x}^{(i)} = \left(\hat{x}^{(i)}_{L+1}, \dots, \hat{x}^{(i)}_{L+T}\right) \in \mathbb{R}^{1 \times T}
$$

### Patching

각 단변량 시계열 $x^{(i)}$는 여러 개의 patch로 나뉜다. patch끼리는 겹칠 수도 있고 겹치지 않을 수도 있다. patch의 길이를 $P$, stride를 $S$라고 하면 patching의 결과는 patch의 sequence이다.

$$
x^{(i)}_p \in \mathbb{R}^{P \times N}
$$

$N$은 patch의 개수이다.

$$
N = \left\lfloor \frac{L - P}{S} \right\rfloor + 2
$$

patch를 나누기 전에 마지막 값 $x_L$을 $S$번 반복한 padding을 원래 sequence의 끝에 붙인다. 당시에는 sequence의 길이가 stride로 항상 나누어떨어지는 것은 아니어서, 마지막 값을 복사해 patch 하나를 더 만드는 것이라고 이해했다.

patch를 쓰면 input token의 수가 $L$에서 대략 $L/S$로 줄어든다. attention map을 계산하는 데 드는 메모리와 계산 복잡도가 줄어들어 GPU 메모리를 적게 쓰고, 그만큼 모델이 더 긴 과거 sequence를 볼 수 있다. patching 없이 모든 time step끼리 attention을 계산하는 것은 길이가 길어지면 계산량 때문에 현실적으로 어렵다. patching을 쓰면 attention을 계산할 대상이 훨씬 적어져서, 더 오래전의 정보까지 attention을 적용할 수 있다.

### Transformer encoder

vanilla Transformer encoder로 관측된 신호를 latent representation으로 사상한다.

Transformer는 입력과 출력의 크기가 항상 같고, 모든 layer에서 같은 크기 $D$의 latent 벡터를 쓴다. 그래서 patch를 학습 가능한 linear projection $\mathbf{W}_p \in \mathbb{R}^{D \times P}$로 $D$차원에 사상한다. 코드에서는 이 $D$를 `d_model`이라고 부른다. 여기에 patch의 시간 순서를 알려 주는 학습 가능한 위치 encoding $\mathbf{W}_{pos} \in \mathbb{R}^{D \times N}$을 더한다.

$$
x^{(i)}_d = \mathbf{W}_p\, x^{(i)}_p + \mathbf{W}_{pos}
$$

### batch를 빼고 shape으로 정리하기

논문의 식을 이해하려고, batch를 고려하지 않고 단변량 시계열 하나가 거치는 과정을 shape으로 적어 보았다.

1. 처음 입력은 $(1, L)$이다.
2. patching을 적용하면 $(P, N)$이 된다. 이때 마지막 값 $x_L$을 복사한 padding이 붙는다. patch 하나가 열벡터 하나이다.
3. Transformer에 넣기 위해 $D$차원으로 사상한다. $D$는 hyperparameter이고(예를 들어 128), patch에서 뽑아낼 feature의 수를 정하는 값이라고 보면 된다. $(D, P) \times (P, N)$을 계산하면 $(D, N)$이 된다. 길이 $P$인 열벡터로 표현되던 patch $N$개가 각각 $D$개의 feature로 표현되는 것이다.
4. $\mathbf{W}_{pos}$를 더해 모델이 patch의 순서를 알 수 있게 한다. 이것이 $x^{(i)}_d$이다.
5. head의 수만큼 Query, Key, Value를 만들어 multi-head attention을 수행한다. $D$를 head의 수로 나눈 값을 $d_k$라고 하자.

multi-head attention에서 head $h = 1, \dots, H$는 다음과 같이 Query, Key, Value를 만든다.

$$
Q^{(i)}_h = \left(x^{(i)}_d\right)^T \mathbf{W}^Q_h, \quad
K^{(i)}_h = \left(x^{(i)}_d\right)^T \mathbf{W}^K_h, \quad
V^{(i)}_h = \left(x^{(i)}_d\right)^T \mathbf{W}^V_h
$$

$$
\mathbf{W}^Q_h, \mathbf{W}^K_h \in \mathbb{R}^{D \times d_k}
$$

$x^{(i)}_d$는 $(D, N)$이고 patch가 열벡터로 놓여 있다. 이것을 transpose하면 $(N, D)$가 되어 patch가 행벡터로 놓이고, [Attention Is All You Need](../../papers/2025-01-06-attention-is-all-you-need.md)에서 단어를 행으로 쌓은 것과 같은 모양이 된다. $(N, D) \times (D, d_k)$이므로 $Q^{(i)}_h$는 $(N, d_k)$이다. 이것을 head의 수만큼 구해 이어 붙이면 $(N, D)$가 되고, 다시 transpose하면 원래의 shape과 같아진다.

encoder를 거친 출력 $z^{(i)}$는 $(D, N)$으로 입력과 크기가 같다. 마지막으로 flatten layer와 linear head를 거쳐 $T$ step의 예측을 얻는다.

### PatchTST는 변수 사이의 관계를 보는가

다변량 예측은 변수 사이의 상관관계를 반영해 다루는 것이라고 생각했는데, PatchTST의 방식은 그것과 다르다. PatchTST가 각 변수를 다루는 방식은 단변량이다. 학습할 때 여러 변수를 하나의 batch에 넣어 한꺼번에 계산할 뿐이고, 그렇게 묶어서 계산한다고 해서 변수 사이의 관계를 모델링하는 것은 아니다. 코드에서 이 부분이 어떻게 구현되어 있는지는 [forward 과정](09-patchtst-forward-pass.md)의 shape 표에 정리했다.

## 실험 결과

노트의 마지막은 지도 학습 방식의 다변량 장기 예측 결과 표이다. ETTh1에서는 예측 길이 $T \in \{96, 192, 336, 720\}$에 대해 예측한 결과가 실려 있다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. $\mathbf{W}^Q_h$와 $\mathbf{W}^K_h$는 $(D, d_k)$인데, 왜 $\mathbf{W}^V_h$만 $(D, D)$인가.**

논문의 식에는 $\mathbf{W}^V_h \in \mathbb{R}^{D \times D}$로 적혀 있고, 그렇게 둔 이유는 본문에 설명되어 있지 않다. 공식 구현은 논문의 표기와 다르게 되어 있다. Value도 Query, Key와 마찬가지로 head마다 `d_v = d_model // n_heads` 차원으로 만들고, head들의 결과를 이어 붙여 `d_model` 차원으로 되돌린 뒤 linear layer를 한 번 거친다. 원래의 Transformer와 같은 방식이다. 당시에는 "크게 중요한 부분은 아니니 넘어간다"고 적었는데, 실제 계산을 이해할 때는 구현을 기준으로 보면 된다.

**Q. 논문이 실험한 데이터셋은 무엇인가.**

장기 예측 실험에는 8개 데이터셋이 쓰였다. Weather, Traffic, Electricity, ILI와 ETT 네 가지(ETTh1, ETTh2, ETTm1, ETTm2)이다. 예측 길이는 ILI만 $\{24, 36, 48, 60\}$이고 나머지는 $\{96, 192, 336, 720\}$이다. 이 가운데 프로젝트에서는 ETTh1과 ILI를 썼다.

## 참고 자료

- Y. Nie, N. H. Nguyen, P. Sinthong, J. Kalagnanam, "A Time Series is Worth 64 Words: Long-term Forecasting with Transformers", ICLR 2023. <https://arxiv.org/abs/2211.14730>
- A. Zeng, M. Chen, L. Zhang, Q. Xu, "Are Transformers Effective for Time Series Forecasting?", AAAI 2023. <https://arxiv.org/abs/2205.13504>
- PatchTST 공식 구현. <https://github.com/yuqinie98/PatchTST>
