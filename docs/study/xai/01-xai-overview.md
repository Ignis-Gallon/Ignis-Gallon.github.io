---
title: "XAI 개요: 설명이 왜 필요하고 어떤 방법이 있는가"
date: 2024-11-22
last_modified_at: 2025-04-09
category: study
tags: [XAI, LIME, SHAP, LRP, 시계열, 설명가능 인공지능]
---

# XAI 개요: 설명이 왜 필요하고 어떤 방법이 있는가

설명가능 인공지능(Explainable AI, XAI)을 처음 공부하면서 강의와 survey 논문을 보고 정리한 내용을 한 편으로 묶었다. 2024년 11월에 들은 강의 세 편(Local Explanation Methods, 시계열 데이터에 대한 XAI 적용, 설명가능 인공지능의 원리와 응용), 12월에 정리한 XAI 개요, 2025년 1월에 읽은 시계열 XAI survey의 용어 부분, 그리고 논문을 읽다가 따로 정리해 둔 개념 세 가지가 바탕이다. 출처는 글 끝에 적었다.

글의 순서는 다음과 같다.

1. 설명이 왜 필요한가
2. 설명 방법을 나누는 기준
3. Local explanation: LIME, SHAP, LRP, Activation Maximization, Saliency Map
4. Global explanation
5. 시계열 데이터에 XAI를 적용할 때
6. survey에서 정리한 용어
7. 논문을 읽으며 따로 정리한 개념

## 1. 설명이 왜 필요한가

Black box 모델은 어떤 결과를 내더라도 왜 그런 결과가 나왔는지 알 수 없다. 우리에게 중요한 역할을 하는 도구를 그 기저를 모르는 채로 잘 쓸 수 있는가 하는 것은 결국 신뢰의 문제이고, 이 때문에 black box 모델은 산업 현장에서 쓰기 어렵다. 개인정보보호법처럼 AI가 자동화된 결정을 내렸다면 그에 대한 설명을 제시하도록 요구하는 제도도 있다.

XAI는 사람이 아니라 AI가 설명을 만들어 내고, 사람이 AI의 동작과 최종 결과를 이해하고 올바르게 해석할 수 있게 하며, 결과가 만들어지는 과정을 설명할 수 있게 해 주는 기술을 말한다.

설명성은 그 자체로도 쓸모가 있다.

- **모델 개선**: 모델이 데이터에 따라 어떤 결과를 내는지 확인할 수 있으므로 디버깅이 쉬워지고 성능 개선에도 활용할 수 있다.
- **모델 검증**: 모델이 왜 그런 결과를 냈는지 이유를 찾을 수 있다. 사람이 생각하지 못한 새로운 인사이트를 얻기도 한다.

설명은 "어디를 보고 이런 결과를 냈는가"를 보여 주는 데서 끝나지 않는다. 그 설명을 보고 모델에 문제가 있는 것은 아닌지 확인하는 데까지 가야 의미가 있다. XAI에 기대하는 효과는 투명성(transparency) 향상, 신뢰(trust) 향상, 편향(bias)에 대한 이해 세 가지로 정리된다.

## 2. 설명 방법을 나누는 기준

가장 먼저 나오는 기준은 설명의 범위다.

| 구분 | 설명 대상 | 예 |
|---|---|---|
| Local | 개별 prediction. 입력 하나에 대해 왜 그런 결과가 나왔는지 설명한다 | 이 사진을 왜 고양이로 분류했는가 |
| Global | 모델의 전반적인 동작. 데이터 전체에 대해 하나의 설명을 만든다 | 모델이 고양이에 대해 전반적으로 무엇을 학습했는가 |

Local explanation의 대표가 input attribution이다. 입력의 각 feature가 출력을 내는 데 기여한 정도를 계산한다. Input attribution은 다시 적용 범위에 따라 나뉜다.

- **Model-agnostic**: 어떤 모델에도 적용할 수 있다. LIME, SHAP.
- **Model-specific**: 특정 유형의 모델에만 적용할 수 있다. 딥러닝 모델에 특화된 LRP.

## 3. Local explanation

### LIME

LIME(Local Interpretable Model-agnostic Explanations)의 아이디어는, 설명하고 싶은 입력 하나가 있을 때 그 입력 근방에서 원래 모델을 근사하는 해석 가능한 모델(interpretable model)을 찾는 것이다. 해석 가능한 모델로는 보통 선형 모델을 쓴다.

원래 모델의 decision boundary는 매우 복잡하게 학습되어 있겠지만, 특정 점 근방만 보면 선형 모델로 근사할 수 있다는 관점이다. 그렇게 적합한 회귀식의 계수를 읽어 모델이 그 점에서 어떻게 동작하는지 분석한다.

설명이 필요한 입력 $x$에 대해 LIME은 다음 objective를 최소화하는 $g$를 찾는다.

$$
\text{explanation}(x) = \arg\min_{g \in G} \big[ L(f, g, \pi_x) + \Omega(g) \big]
$$

- $f$는 원래 모델, $g$는 찾아야 하는 해석 가능한 모델이다.
- $L$은 $f$와 $g$의 차이를 재는 loss로, 보통 MSE를 쓴다.
- $\pi_x$는 proximity measure다. Local하게 근사해야 하므로 $x$에 가까운 샘플일수록 가중치를 크게, 멀수록 작게 준다. 예를 들어 문장에서 단어를 지워 샘플을 만든다면 지운 단어 수만큼 멀어진 것으로 본다. 어떻게 정의하느냐에 따라 결과가 달라진다.
- $\Omega(g)$는 $g$의 복잡도다. 선형 모델이라도 차원이 너무 크면 해석이 어려우므로 복잡도를 낮추는 항을 둔다. Decision tree라면 깊이, 선형 모델이라면 0이 아닌 계수의 개수가 된다.

강의에서 예로 든 형태는 다음과 같다.

$$
L(f, g, \pi_x) = \sum_{z, z'} \pi_x(z)\,\big(f(z) - g(z')\big)^2, \qquad \pi_x(z) = \exp\!\left(-\frac{D(x, z)^2}{\sigma^2}\right)
$$

절차는 네 단계다.

1. 설명이 필요한 데이터를 고른다.
2. 그 데이터를 perturbation해서 새로운 데이터셋을 만든다. Perturbation은 데이터에 의도적으로 작은 변화를 주는 것이다.
3. 원본과 가까운 샘플일수록 큰 가중치를 준다.
4. Objective를 최소화하는 해석 가능한 모델을 찾아 설명으로 제시한다.

3번을 정리하다가 한 번 틀리게 이해했다. 처음에는 "예측값이 많이 변했다면 사라진 feature가 중요한 것"이라는 식으로, 가중치가 예측 결과의 변화에서 나온다고 적었다. 이것은 틀린 설명이다. 가중치는 예측 결과가 아니라 원본 샘플과 perturbation된 샘플 사이의 유사도로 정한다. 원본과 많이 다른 샘플에서는 정확히 무엇 때문에 결과가 달라졌는지 알기 어렵고, local하게 근사한다는 것이 선형회귀로 설명할 수 있는 근거이기 때문에 가까운 샘플에 높은 가중치를 주어야 한다.

**예시.** 유튜브 댓글이 스팸인지 판별하는 모델이 "For Christmas Song visit my channel! ;)"을 스팸으로 예측했다고 하자.

1. 단어 몇 개를 무작위로 지워 여러 문장을 만든다. 예: "Christmas Song visit ;)"
2. 각 문장을 모델에 넣어 스팸일 확률을 얻는다. 문장과 확률의 쌍이 새로운 데이터셋이 된다.
3. 가중치는 `1 - (지워진 토큰의 비율)`로 준다. 많이 지울수록 원본과 달라졌으므로 가중치가 작아진다.
4. 이 데이터셋에 선형 모델을 학습시키고 계수를 본다.

이 예에서는 `channel!`의 계수가 가장 크게 나온다. 즉 `channel`이라는 단어가 스팸으로 판단하는 데 가장 크게 기여했다고 볼 수 있다.

- 장점: 다양한 모델에 쓸 수 있다.
- 단점: proximity measure를 정하기 어렵다. Perturbation을 잘못 정하면 분포 밖(out of distribution)의 데이터를 만들게 되어 설명의 정확도가 떨어질 수 있다.

### Shapley value

Shapley value는 협력 게임 이론에서 온 개념이다. 여러 플레이어가 협력해 게임에 참여하고 상금을 얻었을 때, 그 상금을 각 플레이어의 기여도에 따라 공정하게 나누는 방법이다. 머신러닝에는 다음과 같이 대응시킨다.

| 게임 이론 | 머신러닝 |
|---|---|
| 플레이어 | 입력 feature |
| 협력 게임 | 예측 모델 |
| 전체 상금 | 모델의 출력 |
| 상금 분배 방법(Shapley value) | 각 feature의 기여도 |

즉 모델의 출력을 입력 feature들의 기여도에 따라 나누어 주는 방식이다.

Shapley value가 널리 쓰이는 이유는 "공정한 분배"가 가져야 할 네 가지 공리를 만족하기 때문이다. $\phi_i(v)$는 게임 $v$에서 플레이어 $i$가 받는 몫, $N$은 전체 플레이어 집합이다.

| 공리 | 내용 | 식 |
|---|---|---|
| Linearity | $\phi$는 게임 $v$에 대해 선형이다 | $\phi_i(v_1) + \phi_i(v_2) = \phi_i(v_1 + v_2)$, $\phi_i(cv) = c\,\phi_i(v)$ |
| Dummy (Null player) | 기여하지 않은 플레이어는 0을 받는다 | $\forall S \subseteq N \setminus \{i\}: v(S \cup \{i\}) = v(S) \Rightarrow \phi_i(v) = 0$ |
| Symmetry | 항상 같은 기여를 하는 두 플레이어는 같은 몫을 받는다 | $\forall S \subseteq N \setminus \{i, j\}: v(S \cup \{i\}) = v(S \cup \{j\}) \Rightarrow \phi_i(v) = \phi_j(v)$ |
| Efficiency | 모든 플레이어의 몫을 합치면 전체 상금이 된다 | $\sum_{i \in N} \phi_i(v) = v(N)$ |

네 조건을 모두 만족하는 분배 방법은 Shapley value 하나뿐이다. Feature가 $p$개일 때 식은 다음과 같다.

$$
\phi_i(v) = \sum_{S \subseteq \{1, \dots, p\} \setminus \{i\}} \frac{|S|!\,(p - |S| - 1)!}{p!}\,\big(v(S \cup \{i\}) - v(S)\big)
$$

괄호 안의 $v(S \cup \{i\}) - v(S)$가 marginal contribution이다. Feature $i$가 있을 때와 없을 때의 차이다. 어떤 것의 기여도를 "그것이 있을 때와 없을 때의 차이"로 재는 것은 자연스럽다.

문제는 marginal contribution이 함께 있는 feature에 따라 달라진다는 점이다. `공원 근처`, `평수`, `고양이를 키울 수 있는지` 세 feature로 집값을 예측한다고 하자.

| 이미 있는 feature | `고양이를 키울 수 있는지`가 만드는 차이 |
|---|---|
| 공원, 평수 | 2,000만 원 |
| 공원 | 3,000만 원 |
| 평수 | 1,000만 원 |

그래서 Shapley value는 모든 경우를 고려한다. Feature $i$를 뺀 나머지로 만들 수 있는 모든 부분집합 $S$에 대해 marginal contribution을 구하고, 가중치를 곱해 모두 더한다. 한마디로 모든 상황에서의 기여도를 평균한 분배 방식이다.

이것을 머신러닝에 그대로 적용하려면 두 가지 문제가 있다.

1. 모든 부분집합에 대해 계산해야 하므로 feature가 많으면 정확한 계산이 사실상 불가능하다.
2. $v(S)$가 정의되어 있지 않다. 모델은 모든 feature가 들어와야 출력을 낼 수 있는데, $v(S)$는 일부 feature가 빠진 입력의 출력이다. 빠진 feature를 무엇으로 채울지 정해야 한다.

### SHAP

SHAP(SHapley Additive exPlanations)은 간단히 말하면 계수가 Shapley value인 선형 모델이다.

$$
g(z') = \phi_0 + \sum_{j=1}^{M} \phi_j z'_j
$$

입력 $z'$은 원래 입력이 아니라 단순화된 입력(simplified input)이다. 각 feature가 있으면 1, 없으면 0인 이진 변수다. 입력을 9개 feature로 나누었다면, 전부 있을 때는 $z'_1, \dots, z'_9$가 모두 1이고, 4번과 5번만 있을 때는 $z'_4, z'_5$만 1이다. $\phi_0$는 모든 feature가 없을 때의 예측값이고, $\phi_j z'_j$는 $j$번째 feature가 있을 때만 그 기여도 $\phi_j$가 더해진다는 뜻이다.

선형 모델은 그 자체로 설명이 가능하다. 계수와 값을 곱한 것이 그 feature의 기여도다. 결국 SHAP은 Shapley value를 기여도로 보는 방법이다.

남은 일은 Shapley value를 실제로 구하는 것이다. 계산량이 너무 많으므로 근사해야 하고, 대표적인 방법이 **KernelSHAP**이다. 모든 부분집합을 계산하는 대신 LIME의 틀을 빌린다. Feature의 부분집합을 샘플링하고, 포함된 feature는 1, 빠진 feature는 0으로 표시한 이진 벡터를 입력으로, 그때의 모델 예측값을 출력으로 하는 선형회귀를 적합한다. 이때 LIME의 objective를 다음과 같이 설정하면 선형 모델의 계수가 Shapley value에 근사한다.

$$
\Omega(g) = 0, \qquad \pi_{x'}(z') = \frac{M - 1}{\binom{M}{|z'|}\,|z'|\,(M - |z'|)}, \qquad L(f, g, \pi_{x'}) = \sum_{z' \in Z} \big[f(h_x(z')) - g(z')\big]^2\,\pi_{x'}(z')
$$

두 번째 문제, 즉 feature가 빠진 입력의 출력은 조건부 기댓값으로 정의한다. $x_3$가 없을 때의 출력은 $x_1, x_2$가 주어졌을 때 출력의 기댓값이다.

$$
f(x_1 = 1,\, x_2 = 2,\, x_3\ \text{absent}) = \mathbb{E}\big[f(x_1, x_2, x_3) \mid x_1 = 1,\, x_2 = 2\big]
$$

실제로는 이 조건부 기댓값도 구하기 어려워서 marginal expectation으로 대체한다. 다른 feature의 값과 무관하게 $x_3$만의 분포로 기댓값을 취하는 것이다.

- 장점: Shapley value라는 이론적 기반이 견고하다.
- 단점: KernelSHAP은 매우 느리다. Marginal expectation으로 대체하면 feature 사이의 의존성을 무시하게 되어 결과가 잘못 나올 수 있다.

### LRP

LRP(Layer-wise Relevance Propagation)는 딥러닝 모델에 특화된 방법이다. 모델의 출력을 직전 layer에 분배하고, 그 layer는 다시 직전 layer에 분배한다. Backpropagation을 하듯 출력에서 입력 쪽으로 내려가면서 각 노드가 출력에 얼마나 기여했는지를 구하고, 마지막에는 입력의 각 feature에 대한 relevance score를 얻는다.

RNN처럼 gate가 있는 구조에서는 relevance가 hidden state와 memory cell로는 전달되지만 gate에는 0이 전달된다.

LRP의 전파 규칙과 Transformer로의 확장은 [AttnLRP 논문 리뷰](../../papers/2025-01-23-attnlrp.md)에서 더 자세히 다룬다.

### Activation Maximization

CNN의 각 layer가 무엇을 보는지 알기 위해, 특정 hidden unit의 activation을 최대화하는 입력 패턴을 찾는 방법이다. 데이터 $x$와 파라미터 $\theta$가 주어졌을 때 unit의 activation $z_{ij}(\theta, x)$를 최대화하는 입력을 $x^*$라고 한다.

$$
x^* = \arg\max_{x\ \text{s.t.}\ \lVert x \rVert = \rho} z_{ij}(\theta, x)
$$

### Saliency Map

출력 클래스의 점수에 대한 gradient를 픽셀별로 계산해 각 픽셀의 중요도를 구하는 방법이다. 시각화에는 클래스 전체를 대표하는 그림을 만드는 class model visualization과 이미지 한 장에 대한 image-specific class visualization이 있는데, 앞의 것은 global 방법에 속한다.

Image-specific class visualization은 이미지 $I$에 대한 클래스 점수 함수 $S_c(I)$를 1차식으로 근사한다.

$$
S_c(I) \approx w^\top I + b
$$

여기서 $w$가 픽셀별 gradient다. Gradient가 양수이고 클수록 결과에 더 큰 영향을 주는 픽셀로 보고, 이 픽셀들로 saliency map을 만든다.

## 4. Global explanation

Global 방법은 모델 전체를 하나의 단위로 보고 설명한다. 입력 $x^{(1)}, \dots, x^{(n)}$과 출력 $\hat{y}^{(1)}, \dots, \hat{y}^{(n)}$ 전체에 대해 설명 하나를 만든다. 모델 전체를 다루므로 "어떻게(How)?"에 대한 설명이 많다. 모델이 어떻게 일반화되는지, 입력의 변화에 따라 어떻게 동작하는지 같은 질문이다.

- 복잡한 딥러닝 모델을 해석하기 쉬운 선형 모델로 축소하는 방법이 많다.
- Rule-based 모델과 tree-based 모델이 대표적인 예다. 의사결정나무에서는 각 분기의 결정을 거슬러 올라갈 수 있으므로 모델 전체에 대한 설명 하나가 나온다.
- 비선형 모델을 선형 모델이나 의사결정나무로 대체해 해석하기도 한다.

참고한 survey가 꼽는 대표적인 방법은 Global Surrogate Models, Class Model Visualization, LIME for Global Explanation, CAVs, SpRAy, Global Attribution Mapping, NAMs다.

### 설명을 어떻게 평가하는가

강의에서는 설명 방법을 평가하는 기준으로 세 가지를 들었다.

- **Correctness**: 모델의 행동을 정확하게 설명하는가
- **Continuity**: 비슷한 입력에 대해 비슷한 설명을 내는가
- **Compactness**: 간결한 설명을 내는가

XAI는 LIME, SHAP, LRP 가운데 하나만 보면 되는 것이 아니라 여러 관점에서 보아야 하고, 도메인마다 잘 맞는 방법이 다르다는 점도 함께 적어 두었다.

## 5. 시계열 데이터에 XAI를 적용할 때

### 시계열이 왜 어려운가

시계열 데이터는 시간 축 위의 데이터 포인트들의 집합이다. 주식 데이터, 환자 모니터링 데이터, 공정의 센서 데이터가 대표적이고, 기업에서 재고 관리, 수요 예측, 생산성 개선을 위해 모델을 만들 때 가장 자주 쓰게 되는 데이터다.

시계열의 해석이 어려운 이유는 두 가지다.

- **다변량이다.** 한 샘플 안에 여러 변수가 있고, 변수 사이의 상관관계가 복잡한 dynamics를 만든다. 변수 A가 올랐다고 해서 그것만으로 출력이 설명되지 않는다.
- **사람에게 친숙하지 않다.** 자연어와 이미지는 사람이 보고 바로 이해할 수 있지만, 시계열은 trend나 변화를 바탕으로 해석해야 해서 한눈에 파악하기 어렵다.

그래서 시계열 모델에는 설명이 더 필요하다. 입력 시계열과 예측만 보여 주는 것보다 입력, 출력, feature의 중요도를 함께 보여 주는 쪽이 개발자에게는 디버깅에, 최종 사용자에게는 신뢰에 도움이 된다.

### 설명의 종류

시계열 XAI는 post-hoc인지 intrinsic인지 등 여러 기준으로 나눌 수 있지만, 강의에서는 설명의 단위를 기준으로 나누었다.

| 종류 | 설명 단위 | 대표 방법 |
|---|---|---|
| Time-point based | 시점 하나하나 | Integrated Gradients, LRP, SHAP, Grad-CAM, attention |
| Subsequence based | 구간(덩어리) | Shapelet, prototype |
| Instance based | 입력 전체를 하나로 | Prototype 샘플, counterfactual |
| 그 밖 | | Rule-constrained network, Granger causal graph |

**Time-point based.** 입력의 각 시점에 중요도(relevance score)를 부여한다. 센서 입력이 정상 심박인지 판별하는 모델이라면 중요한 시점은 진하게, 아닌 시점은 연하게 표시한다. 이미 개발한 모델에 설명을 나중에 붙이는 경우가 많은데, 그때 가장 쉽게 적용할 수 있는 방식이다. 여러 패키지가 지원해서 계산도 쉽다. 단점은 한눈에 보이는 것 이상의 깊은 해석이 어렵다는 것이다. 또 같은 입력이라도 방법마다 relevance가 다르게 나온다. Grad-CAM++, saliency map, SHAP이 같은 시점을 서로 다르게 평가하기도 한다. 서로 다른 방법이 준 중요도를 어떻게 해석할지가 어려운 부분이다.

**Subsequence based.** 여러 입력에서 공통으로 나타나는 구간을 뽑아 분석한다. 시계열에서는 이것을 shapelet이라고 부르고, 일반적으로는 prototype이라고도 한다. 시점 단위보다 패턴을 관찰하기 쉽다.

**Instance based.** 입력과 같은 길이의 샘플 단위로 설명한다. 정상 심박으로 예측했다면, 정상 심박이면서 입력과 비슷한 예시(prototype 샘플)를 보여 주는 식이다. 예시들과 입력을 비교하면 "처음에 급격히 떨어지고, 조금 오른 뒤 peak가 나타난다" 같은 전체적인 패턴이 보인다. Counterfactual도 여기에 속한다. 입력의 일부가 달랐다면 예측이 어떻게 바뀌었을지를 보여 준다. 전체적인 패턴을 파악하기에 좋지만, 눈에 띄는 패턴이 없으면 전문가의 지식이 필요하다.

**그 밖.** Rule-constrained network는 규칙을 하나씩 추가했을 때 모델의 학습과 결과가 어떻게 바뀌는지로 설명한다. Granger causal graph는 feature를 노드로, feature 사이의 Granger causality를 edge로 하는 그래프를 학습해 인과관계를 보여 준다. 둘 다 특수한 모델 구조가 필요해 범용적이지 않고, 코드를 새로 구성해야 할 수 있다.

같은 time-point based 설명이라도 heatmap으로 그릴 수도 있고 집계해서 feature importance로 보여 줄 수도 있다. 강의가 강조한 것은, 설명 하나만 보지 말고 여러 가지를 종합해서 보아야 한다는 점이다. 설명 방법을 적용했다고 설명이 끝나는 것이 아니라, 그 결과를 가공해 사용자가 이해할 수 있는 형태로 만드는 과정이 필요하다.

### LRP 결과를 시계열에서 읽는 법

이미지에서는 LRP 결과를 읽기 쉽다. 성 사진을 Castle로 분류했다면 강조된 영역을 보고 "이 부분을 보고 판단했다"고 말하면 된다. 시계열에서는 애매하다. 값이 치솟은 구간이 양의 relevance로 표시되었다고 해서 "값이 커서 중요하다"고 읽으면, 음의 relevance로 표시된 다른 구간의 해석과 충돌한다.

강의에서는 AudioMNIST(0부터 9까지를 발음한 음성)로 화자의 성별을 분류하는 예를 들었다. 음성은 시간 도메인 대신 mel spectrogram으로 바꾸어 시간별 주파수 강도를 입력으로 쓰고, 작은 ResNet으로 분류한다. 해석은 다음 순서로 한다.

1. **극단값부터 본다.** 색이 가장 진한 지점에 먼저 집중한다. 양의 relevance는 예측을 지지한 부분이다. 여성으로 예측했다면 그 예측에 도움이 된 부분이다. 음의 relevance는 도움이 되지 않았거나 반대 결과를 강하게 지지한 부분이다.
2. **여러 입력에서 패턴을 찾는다.** 비슷한 입력을 여러 개 가져와 relevance가 어떻게 달라지는지, 공통된 패턴이 있는지 본다. 주기적인 spike나 색이 일정한 구간이 있는지가 대표적인 확인 대상이다. 이 예에서는 200Hz 부근이 공통으로 양의 영역으로 나타났고, 여성으로 판단하는 데 중요한 역할을 했다고 볼 수 있다.
3. **보는 사람에 맞게 가공한다.** 개발자는 2번까지로 충분할 수 있다. 관리자나 최종 사용자에게는 특정 구간만 보여 주거나 맥락을 단순화해야 한다. Time-point based 설명은 모든 데이터 포인트를 다루므로, 가장 중요한 한두 개만 보여 주거나 특정 이벤트에 대한 설명만 제공하는 식이다.
4. **입력을 바꾸어 본다.** 입력을 perturbation하면서 relevance가 어떻게 변하는지 본다. 예를 들어 뒷부분의 신호가 오르지 않고 유지되도록 바꾸었는데 relevance가 거의 그대로라면, 뒷부분은 여전히 중요하지만 그 값의 상승과 하락 자체는 중요하지 않다는 두 가지를 알 수 있다.

마지막으로 도메인 전문가와 맞추어 보는 것이 좋다. 전문가가 보기에도 납득이 되는 설명인지 묻는 것이다.

### 내부 activation을 설명하는 사례

입력의 중요도 대신 CNN 내부의 feature를 설명하는 방법으로 강의에서는 PTAP을 소개했다. 채널에서 강하게 활성화된 노드를 고르고, 그 노드가 보는 입력 구간(receptive field)을 계산한 뒤, 모은 구간들 가운데 대표(prototype)를 MMD(Maximum Mean Discrepancy)로 선택한다. 미리 정의한 label 없이 "이 채널은 입력의 이런 모양을 보고 있다"를 보여 줄 수 있다. 스마트폰 센서로 사람의 활동을 분류하는 HAR 데이터셋에서는 첫 번째 layer가 세부적인 feature를, 세 번째 layer가 smoothing된 장기 추세를 잡는 것으로 나타났다.

이 논문은 나중에 따로 읽었다. [PTAP 논문 리뷰](../../papers/2025-02-02-ptap.md)에 정리했다.

### 연구 방향

- **시각화**: heatmap으로 표시하는 데 그치지 않고 더 눈에 잘 들어오는 방식을 찾는 것.
- **Higher-order explanation**: 시점 하나하나보다 전체적인 시각에서의 설명이 유용한 경우가 많다. 모델을 global하게 해석하는 일이고, 데이터 포인트별 해석보다 어렵다.
- **평가**: 시계열에만 해당하는 문제는 아니다. XAI 기법은 평가하기 어려워서, 벤치마크 데이터셋이나 수치적인 metric을 만드는 것 자체가 연구 주제다.

## 6. survey에서 정리한 용어

시계열 XAI를 넓게 보려고 "Explainable Artificial Intelligence (XAI) on Time Series Data: A Survey"(2021)를 읽었다. 2024년에 나온 시계열 예측 XAI survey도 있었지만 특정 도메인에 한정되어 있어서, 더 일반적인 범위를 다루고 많이 인용된 이 survey를 골랐다. 여기서는 1장과 2장에서 정리한 내용만 적는다.

### 시계열에서 설명성을 얻기 어려운 이유

시계열 데이터는 어디에나 있고, 분류(classification), 예측(forecasting), 군집화(clustering) 등 여러 task에 머신러닝이 쓰인다. RNN과 CNN의 도입으로 성능은 좋아졌지만 데이터의 복잡성 때문에 해석에는 한계가 있었다. Computer vision과 NLP에서는 설명성 연구가 많이 진행되었지만 시계열은 데이터가 직관적이지 않아 연구가 부족하다. 눈으로 보아도 내재된 정보를 이해하기 어려우므로 설명을 정량적으로 평가하기도 더 어렵다. 내재된 정보를 이해하려면 전문가의 지식이나 그 정보를 드러내 주는 별도의 방법이 필요하다. 이미지에서 특정 물체를 강조해 보여 주는 기술을 떠올리면 차이를 이해하기 쉽다.

### 용어

| 용어 | 뜻 |
|---|---|
| Explainability | 능동적 특성. 모델이 자신의 선택이나 출력을 쉽고 자세하게 설명하는 능력 |
| Interpretability | 수동적 특성. 관찰자에게 합리적인 설명이 되는 정도. 입력이 출력으로 매핑되는 방식을 사용자가 보고 연구할 수 있는 시스템 |
| Trustworthiness | 모델이 주어진 문제에서 의도대로 행동할 것인가. 모델이 결정에 대해 자세한 설명을 줄 수 있을 때 확보된다 |
| Interactivity | 사용자와의 상호작용이 중요한 분야에서 다루는 특성 |
| Stability | 노이즈나 작은 perturbation에도 결과가 달라지지 않는 능력 |
| Robustness | 사람이 의도적으로 만든 방해를 이겨 내는 능력 |
| Reproducibility | 같은 데이터를 여러 번 넣었을 때 항상 같은 결과를 내는 능력 |
| Confidence | 결정의 신뢰성을 정량화한 것. 사용자가 모델의 결과를 얼마나 믿을 수 있는지를 나타낸다 |

Explainability와 interpretability의 차이는 이렇게 이해했다. Explainability는 설명 자체를 제공하는 능력이고, interpretability는 그 설명이 사람에게 얼마나 잘 이해되는지다.

Survey는 이 특성들이 갖추어져야 최종적으로 모델의 trustworthiness가 확보된다고 본다. 설명성이 필요한 경우는 두 가지다. 단순하고 해석 가능한 모델이 수행하기에는 task가 너무 복잡한 경우, 그리고 우리가 이해하지 못하는 모델에 맡기기에는 task가 너무 중대한 경우다.

### Stability, robustness, confidence

분류 task에는 정확도라는 분명한 metric이 있고 대부분의 경우 성능을 평가하기에 적합하다. 그러나 입력에 perturbation이나 노이즈가 들어간 상황에서도 높은 정확도가 유지될지는 알 수 없다. 높은 정확도가 항상 좋은 시스템을 뜻하지는 않으며, 추가 실험이 필요하다. 이 한계에 대응하는 개념이 세 가지다.

- **Stability.** 작은 perturbation에도 출력이 일정하면 stable하다고 한다. 자율주행차가 정지 표지판을 인식하지 못하면 큰 문제가 되므로, 인식하지 못하더라도 적어도 지금의 판단이 불확실하다는 경고는 할 수 있어야 한다.
- **Robustness.** 적대적 공격을 이겨 내는 능력이다. 교통 표지판 이미지에 사람 눈으로는 알 수 없는 노이즈를 섞었을 때 대부분의 모델이 구분하지 못했고, 그런 모델은 robust하다고 할 수 없다. 출력을 바꾸기 위한 의도적인 perturbation에는 counterfactual도 있다. 미리 정한 출력으로 바꿀 수 있는 가장 작은 변화를 말한다.
- **Confidence.** Perturbation에 대비해 훈련하는 것만으로는 충분하지 않을 수 있다. 대신 모델은 입력이 학습 데이터의 분포에서 얼마나 떨어져 있는지를 비교해, 확실하게 분류할 수 없는 샘플을 가려낼 수 있다. 그래서 모든 예측에는 그 결정이 얼마나 확실한지를 나타내는 점수가 필요하고, 점수가 정해 둔 threshold보다 낮으면 모델의 선택을 의심할 수 있어야 한다.

불확실성(uncertainty)은 두 가지로 나뉜다.

- **Aleatoric uncertainty**: 같은 실험을 반복해도 결과가 달라질 수 있는, 우연에서 오는 불확실성.
- **Epistemic uncertainty**: 데이터와 지식의 부족에서 오는 불확실성. 학습 데이터의 분포와 먼 입력이 들어오거나 모델이 제대로 일반화하지 못했을 때 생긴다. Survey는 이쪽을 주로 다룬다.

Survey에 실린 시계열 설명 방법들은 아직 stability, robustness, confidence를 확보하지 못한다고 한다. 모델이 옳게 행동한다고 확신할 수 있는 metric이 아직 없기 때문이다.

### User trust

사용자의 신뢰는 객관적 지표만으로 만들어지지 않는다. 신뢰가 없으면 사용자는 자동화된 시스템을 믿지 않고, 중대한 task일수록 더 그렇다. 중요한 질문 가운데 하나는 사용자에게 피드백을 언제, 어떻게 줄 것인가다. 행동한 뒤가 아니라 행동하기 전에 설명을 주면 더 큰 신뢰를 얻을 수 있다.

시계열 예측은 사용자와 상호작용하는 분야가 아니어서, 이 부분을 내 관심사와 어떻게 연결할지 고민하며 읽었다. 그런데 survey에 따르면 시계열 데이터에 적용된 설명 방법들은 대체로 최종 사용자가 아니라 개발자를 위한 설명을 제공한다. 시계열에서 설명의 일차 독자가 개발자라는 점은 읽기 전에는 생각하지 못한 부분이었다.

## 7. 논문을 읽으며 따로 정리한 개념

### Entangled와 disentangled

Entanglement는 모델 내부의 representation 공간에서 서로 다른 개념이 분리되지 않고 섞여 있는 상태다. 반대로 각 개념이 분리되어 표현된 상태가 disentangled다.

Entangled한 표현에는 세 가지 단점이 있다.

- 해석이 어렵다. 뉴런 하나가 여러 의미에 대응된다.
- 일반화가 어렵다. 새로운 샘플에 대해 잘못된 추론을 할 수 있다.
- 조작과 제어가 어렵다. 원하는 개념만 바꾸기 어렵다.

Disentangle이 잘 되어 있으면 latent space에서 개념을 조작할 수 있다. 얼굴 이미지에 안경만 추가하고 싶다면 그 개념에 해당하는 벡터만 조작하면 된다.

Latent space는 보통 t-SNE나 UMAP으로 2차원에 축소해서 분석한다. t-SNE는 local structure를 잘 보존하고 시각화 품질이 좋다. UMAP은 global 구조와 local 구조를 모두 보존하고 속도가 빠르다.

### Monosemanticity와 polysemanticity

- **Monosemanticity**: 하나의 뉴런이 하나의 개념에만 반응하는 것. 어떤 뉴런이 고양이 이미지에만 강하게 반응한다면 "고양이를 감지하는 뉴런"이라고 해석하면 되므로 사람이 이해하기 쉽다.
- **Polysemanticity**: 하나의 뉴런이 여러 개념에 동시에 반응하는 것. 고양이, 강아지, 털, 귀여움에 모두 반응하는 뉴런은 해석하기 어렵다. 예기치 않은 방식으로 반응할 수 있어 AI safety 관점에서 위험 요인이 되기도 한다.

OpenAI나 Anthropic의 연구에서는 GPT 계열 모델의 뉴런 대부분이 polysemantic하다고 보고한다. "뉴런 하나가 개념 하나"라는 고전적인 그림은 실제 딥러닝 모델에는 잘 맞지 않을 수 있다.

Polysemanticity는 **분산 표현**(distributed representation)의 특징이기도 하다. 분산 표현은 정보를 뉴런 하나가 아니라 여러 뉴런(벡터의 여러 차원)에 나누어 표현하는 방식이다. "고양이"라는 개념은 `[털: 0.9, 동물성: 1.0, 크기: 0.2, 귀여움: 0.8, …]`처럼 여러 차원에 퍼져 있을 수 있다. 여기서 차원은 벡터 공간에서 정보를 표현하는 축 하나를 말한다.

두 쌍의 개념은 서로 대응된다. Polysemanticity는 entanglement에, monosemanticity는 disentanglement에 대응된다. 다만 mono/polysemanticity는 뉴런을 두고 하는 말이고 entanglement는 뉴런 또는 차원을 두고 하는 말이어서 범위가 조금 다르다.

### Counterfactual과 adversarial attack

- **Counterfactual**: 어떤 입력에 대해 "만약 ~였다면 결과가 달라졌을까?"를 묻는다. 실제 상황을 조금 바꾸어 보면서 모델의 결정 경계나 인과관계를 이해하려는 접근이다.
- **Adversarial attack**: 입력에 작은 교란을 더해 모델을 속인다. 모델의 약점을 찾거나 robustness를 시험하기 위한 방법이다.

둘 다 입력을 바꾸어 모델의 반응을 본다는 점은 같다. Counterfactual은 의미 있는 변화를 찾아 해석을 얻으려는 것이고, adversarial attack은 의미 없는 변화로 모델의 취약점을 드러내려는 것이라는 점이 다르다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. SHAP에서 조건부 기댓값을 marginal expectation으로 대체한다는 것은, 빠진 feature를 그 feature의 평균값으로 바꾸어 넣는다는 뜻인가.**

아니다. 평균을 내는 대상이 입력이 아니라 모델의 출력이다. 빠진 feature의 자리에 배경 데이터(background data)의 값을 하나씩 넣어 모델을 여러 번 평가하고, 그 출력들을 평균한다. $S$를 남아 있는 feature의 집합, $\bar{S}$를 빠진 feature의 집합이라고 하면

$$
v(S) = \mathbb{E}_{X_{\bar{S}}}\big[f(x_S, X_{\bar{S}})\big] \approx \frac{1}{n} \sum_{k=1}^{n} f\big(x_S,\, x^{(k)}_{\bar{S}}\big)
$$

이다. 빠진 feature를 평균값으로 바꾸어 한 번만 평가한 값은 $f\big(x_S, \mathbb{E}[X_{\bar{S}}]\big)$이고, 둘은 $f$가 빠진 feature에 대해 선형일 때만 같다. 예를 들어 $f = x_3^2$이고 $x_3$가 $\pm 1$을 반반의 확률로 갖는다면 앞의 값은 1, 뒤의 값은 0이다. SHAP 논문은 조건부 기댓값, feature가 독립이라는 가정 아래의 marginal expectation, 모델이 선형이라는 가정 아래의 평균값 대입 순서로 근사가 한 단계씩 거칠어진다고 정리한다. 계산을 줄이려고 배경 데이터를 평균 벡터 하나로 두는 경우가 있는데, 그것은 마지막 단계의 근사까지 쓴 것이다.

**Q. KernelSHAP의 선형회귀에서 출력으로 쓰는 값은 모델의 예측값이 맞는가.**

맞다. 회귀의 목표값은 $f(h_x(z'))$, 곧 이진 벡터 $z'$이 1로 표시한 feature만 원래 값으로 두고 나머지는 위의 방식으로 처리했을 때의 모델 출력이다. 빠진 feature를 배경 데이터로 채워 여러 번 평가하므로 정확히는 그 예측값들의 평균이다. 분류 모델이라면 설명하려는 class의 확률이나 logit을 쓴다. $z'$이 모두 0이거나 모두 1인 경우에는 본문의 kernel 가중치가 무한대가 되는데, 이는 $\phi_0 = \mathbb{E}[f(X)]$와 $\phi_0 + \sum_j \phi_j = f(x)$를 제약으로 강제하는 것과 같다.

**Q. Interactivity는 ChatGPT처럼 대화를 통해 task를 원활히 수행할 수 있는지를 뜻하는가.**

survey가 말하는 것은 대화로 task를 수행하는 능력이 아니다. survey는 interactivity를 사용자와의 상호작용으로 정의하고, 설명 가능한 모델이 목표로 삼는 것 가운데 하나이며 사용자가 중요한 분야에서 특히 중요하다고만 적는다. 다른 XAI 문헌의 정의를 인용한 것이다. 이어서 대부분의 방법이 최종 사용자가 설명을 어떻게 받아들이는지를 고려하지 않는다고 지적하고, 결론에서는 설명과 피드백을 주고받는 interactive system을 앞으로의 방향으로 든다. 즉 사용자가 설명을 받아 보고 되묻거나 피드백을 줄 수 있는 정도를 말한다. 대화형 인터페이스는 그것을 구현하는 한 가지 형태일 수는 있다.

**Q. 전체 예측이 아니라 시점별, 구간별로 불확실성의 threshold를 정해 신뢰 여부를 보인 연구가 있는가.**

있다. conformal prediction 계열의 연구가 이 질문에 가깝다. 보정용 데이터에서 예측 오차의 분위수를 구해 threshold로 삼고, 새 예측마다 "참값이 이 구간 안에 있을 확률이 $1 - \alpha$ 이상"이 되도록 구간을 만든다. 시계열 예측에서는 예측 시점(horizon)마다 분위수를 따로 구해 시점별 구간을 주는 방법(Stankevičiūtė 외, "Conformal Time-Series Forecasting", NeurIPS 2021)과, 분포가 시간에 따라 변할 때 실제 포함률을 보면서 threshold를 온라인으로 조정하는 방법(Gibbs와 Candès, "Adaptive Conformal Inference Under Distribution Shift", NeurIPS 2021)이 있다. 구간이 넓게 나온 시점은 예측을 덜 믿어야 하는 시점으로 읽는다. 다만 이 연구들이 다루는 것은 예측값의 불확실성이고, 설명 자체의 신뢰도는 아니다.

### 아직 남은 질문

- Survey는 모델이 옳게 행동하는지 확신할 수 있는 metric이 아직 없다고 했는데, 지금은 어떤지 확인하지 못했다. 예측 구간의 포함률처럼 성질 하나에 대한 보증은 있지만, survey가 말한 뜻의 metric이 시계열 설명 방법에 대해 자리 잡았는지는 2021년 이후의 survey를 읽어야 알 수 있다.

## 참고 자료

- 강의 "Local Explanation Methods". LIME, SHAP, LRP 부분의 바탕이다. 노트에 강의 링크를 남겨 두지 않았다.
- 강의 "시계열 데이터에 대한 XAI 적용": <https://www.youtube.com/watch?v=bOympGV_MT4>
- 강의 "설명가능 인공지능의 원리와 응용": <https://www.youtube.com/watch?v=T9FS8nQnGSA>
- XAI 개요 영상: <https://www.youtube.com/watch?v=Grc7egfZP84>
- "Opportunities and Challenges in Explainable Artificial Intelligence (XAI): A Survey". arXiv:2006.11371. <https://arxiv.org/abs/2006.11371>
- "Explainable Artificial Intelligence (XAI) on TimeSeries Data: A Survey". arXiv:2104.00950. <https://arxiv.org/abs/2104.00950>
- S. M. Lundberg, S.-I. Lee, "A Unified Approach to Interpreting Model Predictions" (SHAP). NeurIPS 2017. <https://proceedings.neurips.cc/paper_files/paper/2017/file/8a20a8621978632d76c43dfd28b67767-Paper.pdf>
- K. Stankevičiūtė 외, "Conformal Time-Series Forecasting". NeurIPS 2021.
- I. Gibbs, E. Candès, "Adaptive Conformal Inference Under Distribution Shift". NeurIPS 2021.
