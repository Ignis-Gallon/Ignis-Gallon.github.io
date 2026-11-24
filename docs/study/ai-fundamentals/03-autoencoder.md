---
title: "Autoencoder: manifold learning, 규제, 응용"
date: 2024-11-24
last_modified_at: 2024-11-24
category: study
tags: [Autoencoder, Manifold Learning, Bias-Variance, Regularization, 차원 축소]
---

# Autoencoder: manifold learning, 규제, 응용

Autoencoder를 소개하는 공개 세미나 영상을 보고 정리한 글이다. Autoencoder가 무엇이고 왜 쓰는지, bias-variance tradeoff의 관점에서 어떤 변형들이 나왔는지, 그리고 어디에 쓰이는지를 차례로 본다. 세미나가 manifold learning 쪽을 중심으로 설명하므로 이 글도 그 범위를 따른다.

## Autoencoder란

Autoencoder는 label이 없는 데이터의 효과적인 코딩(표현)을 배우는 데 쓰는 신경망이다. Label이 필요 없으므로 비지도 학습에 속한다.

구조는 encoder와 decoder가 가운데가 좁은 bottleneck 모양을 이룬다.

- **Encoder**: 입력 데이터를 의미 있고 압축된 representation으로 바꾼다.
- **Decoder**: 압축된 representation을 다시 입력 데이터로 복원한다.

학습은 입력과 복원된 데이터의 차이를 줄이는 방향으로 한다.

Autoencoder를 쓰는 목적은 크게 두 가지다.

1. **입력을 압축된 representation으로 mapping하는 encoder를 얻기 위해.** Manifold learning을 위한 autoencoder이고, encoder를 학습시키려고 decoder를 붙인 구조다.
2. **Latent variable을 실제 데이터의 분포로 mapping하는 decoder를 얻기 위해.** 생성 모델을 위한 autoencoder이고, decoder를 학습시키려고 encoder를 붙인 구조다.

## Manifold learning

Manifold는 가까운 범위에서는 유클리드 공간처럼 보이지만 전체로는 그렇지 않은 공간이다. 두 점 사이의 거리(유사도)가 근거리에서는 Euclidean distance를 따르지만 원거리에서는 따르지 않는다. 쉽게 말하면 고차원 공간에 들어 있는 저차원 공간이다. 돌돌 말린 면 위의 두 점은 3차원 공간에서 직선으로 재면 가깝지만, 면을 펼친 2차원에서 재면 멀리 떨어져 있을 수 있다.

데이터의 관점에서는 이렇게 가정한다. 고차원 공간에 주어진 실제 데이터는 공간 전체에 퍼져 있지 않고, 그 안에 있는 훨씬 저차원의 manifold 근처에 몰려 있다. Manifold learning은 이 가정 아래 저차원 구조를 찾아내는 비선형 차원 축소이자 비지도 학습이다.

Manifold learning을 쓰는 이유는 두 가지로 정리했다.

1. **차원의 저주.** 고차원 공간에서는 데이터가 희박하게 분포해서 비슷한 데이터끼리의 특성을 파악하기 어렵다. 차원을 줄여 이 문제를 풀고 학습 속도와 성능을 높인다.
2. **고차원에서의 거리는 믿기 어렵다.** 고차원 공간에서 가까워 보이는 두 점이 manifold 위에서는 멀고 의미도 전혀 다를 수 있다. 고차원에서 그대로 해석하려 해도 그 거리가 의미 있는 것이 아닐 수 있다는 뜻이다.

정리하면 데이터를 저차원으로 줄이면서도 본질적인 구조와 특성을 보존하려는 것이고, PCA 같은 선형 방법으로는 놓칠 수 있는 비선형 관계를 잡아내려는 것이다.

Manifold learning은 고차원 데이터를 저차원 manifold로 mapping하는 함수를 찾는 과정이다. 학습이 끝난 autoencoder의 encoder를 이 mapping 함수로 쓰면 dimensionality reduction, feature extraction, representation learning을 할 수 있다.

생성 모델은 반대로 실제 데이터의 분포를 학습해 데이터를 만들어 낸다. GAN(Generative Adversarial Network)과 VAE(Variational Autoencoder)가 대표적이다. Latent variable을 실제 데이터 분포로 mapping하는 함수를 찾는 것이 목적이고, 학습이 끝난 autoencoder의 decoder를 그 함수로 쓴다.

## Bias-variance tradeoff와 규제

Manifold learning을 위한 autoencoder에는 여러 변형이 있는데, 과적합 문제를 풀기 위해 다양한 규제(regularization) 기법을 적용하는 과정에서 파생된 것들이다. 그래서 세미나는 먼저 bias와 variance를 설명한다.

어떤 데이터에 대해 모델을 만들 때 모델의 error는 세 항으로 나뉜다.

$$
\text{Total Error} = \text{Bias}^2 + \text{Variance} + \text{Irreducible Error}
$$

- **Bias**: 실제 데이터를 만들어 낸 모델과 우리가 가정한 모델의 차이에서 생기는 error다. 실제값과 평균 예측값의 차이라고 할 수 있다. 같은 데이터에 1차 함수, 2차 함수, 4차 함수를 차례로 맞춰 보면 차수가 높아질수록 이 error가 줄어든다.
- **Variance**: 모델의 예측값이 얼마나 흩어지는지를 나타낸다. 표본 집합을 바꿔 가며 모델을 추정할 때 생기는 error다. 표본 집합 A로 맞춘 모델과 표본 집합 B로 맞춘 모델을 비교하면, 차수가 높을수록 두 모델의 모양이 크게 달라진다.
- **Irreducible error**: 줄일 수 없는 자연적인 오류다.

Variance에 대해서는 따로 풀어서 적어 두었다. Variance는 모델이 학습 데이터의 작은 변화(예를 들어 표본을 어떻게 뽑았는지에 따른 차이)에 얼마나 민감하게 반응하는지를 나타낸다. Variance가 크다는 것은 데이터가 조금만 달라져도 모델의 출력이 크게 달라진다는 뜻이다. 4차 함수로 맞춘 모델은 bias는 작지만, 표본이 바뀌면 곡선이 크게 출렁인다.

모델이 복잡해지면(layer와 node가 늘어 파라미터가 많아지면) 데이터의 더 세부적인 패턴을 학습할 수 있다. 그 결과 데이터에 섞인 잡음이나 우연한 특성까지 학습하게 되고, 새로운 데이터에서는 불안정한 성능을 보인다. 복잡한 모델은 학습 데이터를 완벽히 맞추려고 데이터를 통째로 암기하는 경향이 있고, 이것이 과적합이다. 그래서 모델이 데이터의 사소한 특성까지 암기하지 않도록 적절한 규제가 필요하다.

Bias와 variance는 tradeoff 관계다. 모델의 복잡도가 높아질수록 bias는 줄고 variance는 늘어난다. 학습 집합의 error가 지나치게 작아지면 다른 표본 집합에서의 variance가 커져 일반화 성능이 떨어진다. 따라서 total error를 가장 작게 하는 복잡도를 찾아야 한다.

그런데 데이터에 딱 맞는 복잡도를 찾기는 매우 어렵다. 그래서 딥러닝은 모델의 복잡도를 충분히 높여 놓고, 규제 기법으로 bias를 조금 높이는 대신 variance를 크게 낮추는 방향으로 발전했다. 규제를 걸면 bias가 커지는 양보다 variance가 줄어드는 양이 더 크다. 언어 모델의 크기를 등장 시기별로 보면 최근 모델일수록 복잡도가 아주 크고, 이런 모델들은 여러 규제를 적용해 일반화 성능을 확보한다. 규제는 모델에 어떤 제약을 걸어 모델이 가진 용량을 다 쓰지 못하게 하는 것이고, 이를 통해 학습 데이터를 그냥 외워 버리는 일을 막는다.

Autoencoder에도 같은 tradeoff가 있다. 우리는 autoencoder가 입력을 잘 복원하기를 바라면서, 동시에 처음 보는 데이터까지 아우르는 representation을 학습하기를 바란다. 앞의 것은 입력에 민감하게 반응해야 한다는 요구이고, 뒤의 것은 둔감하게 반응해야 한다는 요구다. Autoencoder 역시 복잡도를 높인 뒤 규제를 적용하는 길을 갔고, 그 과정에서 아래의 변형들이 나왔다.

## 규제를 넣은 autoencoder

### Sparse autoencoder

다른 autoencoder와 달리 hidden layer의 node 수가 입력 layer보다 많은 overcomplete 구조를 가진다. 대신 hidden layer에서 실제로 활성화되는 node가 적어지도록(sparse하도록) 제약을 건다.

Sparsity parameter $\rho$를 정하고, hidden node $j$의 활성화 값을 전체 관측치 $m$개에 대해 평균 낸 값 $\hat{\rho}_j$가 $\rho$와 같아지도록 한다.

$$
\hat{\rho}_j = \frac{1}{m} \sum_{i=1}^{m} a_j\big(x^{(i)}\big)
$$

이 제약은 loss에 KL divergence 항이나 L1 regularization 항을 추가해서 구현한다.

$$
\min_{\mathrm{enc},\, \mathrm{dec}} \ \mathbb{E}\big[\mathrm{loss}\big(x, \mathrm{dec}(\mathrm{enc}(x))\big)\big] + \sum_{j} \mathrm{KL}\big(\rho \,\|\, \hat{\rho}_j\big)
$$

### Denoising autoencoder

이름 그대로 잡음을 제거하는 autoencoder다. 입력 데이터에 무작위 잡음을 더하거나 dropout을 적용해 일부러 손상시킨 뒤, 모델이 손상되지 않은 원래 데이터를 복원하도록 학습시킨다.

바탕이 되는 가정은 입력에 잡음을 조금 더하더라도 manifold 위에서는 같은 곳에 놓여야 한다는 것이다. 이렇게 학습하면 입력의 작은 변화에 강건한 모델을 얻는다.

### Contractive autoencoder

Denoising autoencoder와 목적은 같다. 작은 변화에 강건한 모델을 얻으려는 것이다. 차이는 초점에 있다. Denoising autoencoder는 입력의 작은 변화를 견디고 복원하는 데 초점을 두고, contractive autoencoder는 encoder가 특징을 추출하는 단계에서 중요하지 않은 입력의 변화를 무시하도록 하는 데 초점을 둔다.

구현은 encoder의 Jacobian matrix의 크기를 loss에 더하는 것이다. Jacobian은 입력의 변화에 대한 encoder 출력(representation)의 변화를 1차 도함수로 모아 놓은 행렬이다.

$$
\min_{\mathrm{enc},\, \mathrm{dec}} \ \mathbb{E}\big[\mathrm{loss}\big(x, \mathrm{dec}(\mathrm{enc}(x))\big)\big] + \lambda \big\| \nabla_x \mathrm{enc}(x) \big\|_F^2,
\qquad
\big\| \nabla_x \mathrm{enc}(x) \big\|_F^2 = \sum_j \sum_k \left( \frac{\partial\, \mathrm{enc}(x)_j}{\partial x_k} \right)^2
$$

직관은 숫자 이미지로 설명할 수 있다. 손글씨 3과 8을 나란히 놓으면, 왼쪽의 획이 닫혀 있는지 열려 있는지는 두 숫자를 가르는 중요한 변화다. 반면 아래쪽 획의 모양이 조금 다른 것은 3과 8을 구분하는 데 중요하지 않다. 숫자들이 1차원 곡선 모양의 manifold 위에 놓여 있다고 하면, 앞의 변화는 manifold를 따라 움직이는 방향이고 뒤의 변화는 manifold에서 벗어나는 방향이다. Contractive autoencoder는 중요하지 않은 방향의 변화에 대해 representation이 변하지 않도록 학습한다.

## 응용

### Classification

Autoencoder는 비지도 학습이므로 label 없이 사전 학습할 수 있다. 사전 학습한 뒤 encoder를 feature extractor로 삼아 downstream task에 쓰거나 few-shot learning에 쓴다. 같은 class에 속한 관측치들에는 겉으로 드러나지 않더라도 autoencoder로 근사할 수 있는 latent structure가 있을 것이라고 가정하는 것이다.

Classification에서 규제 기법으로도 쓸 수 있다. 일반적인 classification loss에 reconstruction loss를 더하는 방식이다.

세미나에서는 Vision Transformer를 autoencoder처럼 사용하는 자기지도 학습 연구도 소개했다. 전통적인 autoencoder와는 다르지만 Transformer를 같은 방식으로 썼다는 점에서 흥미로운 사례다.

### Clustering

Label이 없는 비슷한 데이터들 사이에 저차원의 latent representation, 곧 manifold가 있다고 가정한다. Encoder로 고차원 데이터를 저차원으로 embedding한 뒤, 그 결과에 K-Means 같은 기존 군집화 알고리즘을 적용한다. Autoencoder는 군집화가 아니라 복원을 목적으로 학습되므로, 군집화에 맞추기 위한 추가 기법이 함께 쓰이기도 한다.

### Anomaly detection

정상 관측치와 이상 관측치의 latent structure가 다르다고 가정한다. 정상 데이터만으로 autoencoder를 학습시키면 이상 데이터는 잘 복원하지 못할 것이라고 기대할 수 있다. 검증 단계에서 이상 관측치가 들어오면 reconstruction loss가 크게 나오고, 이를 이용해 이상을 탐지한다. 시계열에 적용하는 방법은 [다음 글](04-lstm-autoencoder-anomaly-detection.md)에서 자세히 본다.

### Dimensionality reduction

실제 데이터는 매우 높은 차원의 sparse한 벡터로 표현되는 경우가 많고, 이런 데이터로 학습하면 차원의 저주에 빠지기 쉽다. 목표는 고차원의 sparse한 데이터를 훨씬 낮은 차원의 intrinsic dimensionality로 mapping하는 것이다. 고전적인 방법으로 관측치를 낮은 차원으로 linear projection하는 PCA가 있고, 여기에 비선형 변환을 더한 것이 autoencoder라고 해석할 수 있다. PCA는 [선형대수 복습](08-linear-algebra-and-pca.md)에서 다시 정리했다.

## 정리

- Autoencoder는 입력을 압축했다가 복원하도록 학습하는 비지도 학습 모델이다. Encoder를 얻으려는 목적(manifold learning)과 decoder를 얻으려는 목적(생성 모델)으로 나뉜다.
- 모델의 복잡도를 높이고 규제로 variance를 낮추는 딥러닝의 일반적인 방향이 autoencoder에도 적용되어, sparse, denoising, contractive autoencoder 같은 변형이 나왔다.
- 학습된 encoder와 reconstruction error는 classification, clustering, anomaly detection, 차원 축소에 쓰인다.

## 참고 자료

- 고려대학교 산업경영공학부 DMQA 연구실, "[DMQA Open Seminar] Autoencoder" (YouTube). <https://www.youtube.com/watch?v=X8SBsVqmVdY>
