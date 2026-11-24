---
title: "모델 의존적 변수 선택: 트리의 변수 중요도와 SHAP"
date: 2025-05-20
last_modified_at: 2025-05-22
category: study
tags: [변수 선택, 변수 중요도, SHAP, XGBoost, Optimal Trees]
---

# 모델 의존적 변수 선택: 트리의 변수 중요도와 SHAP

작성일 2025-05-20 · 최종 수정일 2025-05-22
{ .post-date }

변수 선택에는 [Markov blanket](05-markov-blanket-feature-selection.md)처럼 변수 사이의 독립성 구조를 이용하는 방법 외에, 학습된 모델에게 "어떤 변수를 중요하게 썼는가"를 묻는 방법이 있다. 이 글에서는 후자, 곧 모델 의존적(model-dependent) 변수 선택을 정리하고자 한다. 트리 계열 모델의 변수 중요도와 SHAP을 중심으로, 두 방식을 비교한 논문 두 편을 읽은 내용이다.

## 1. 분류

조사를 시작할 때는 방법을 다음 두 묶음으로 나누어 문헌을 모았다.

| 묶음 | 방법 |
|---|---|
| Importance 기반 | CART, 트리 앙상블(Random Forest, XGBoost), Optimal Trees |
| XAI 기반 | SHAP, LRP(Layer-wise Relevance Propagation), Integrated Gradients |

정리하다가 처음의 이해가 조금 틀렸다는 것을 알았다. 두 묶음은 나란히 놓이는 것이 아니다. 모델 의존적이라는 말은 어느 쪽이든 학습된 모델이 있어야 한다는 뜻이다. XAI 기법은 모델 없이 따로 설명될 수 없고, 이미 학습된 모델 위에서 변수 중요도를 계산하는 또 하나의 방법이다. 예를 들어 SHAP은 Random Forest나 XGBoost가 내장한 중요도 대신 쓸 수 있는 대안이다. 따라서 설명의 순서는 모델에 내장된 중요도가 먼저이고, 그 모델에 XAI 기법을 적용해 중요도를 다시 계산하는 방식이 그다음이다.

## 2. 트리 계열 모델의 변수 중요도

변수 선택의 가장 일반적인 접근은 기계 학습 모델로 변수 중요도(variable importance)를 평가하는 것이다. 변수 중요도는 각 변수를 모델에서 사용했을 때 생기는 성능 향상의 크기로 잰다. 중요도는 설명 가능성도 준다. 모델이 학습을 거쳐 어떤 변수를 중요하게 보는지 알면 예측이 어떻게 만들어지는지 이해할 수 있다. 문제는 중요도가 잘못 평가되면 엉뚱한 변수를 제거하게 된다는 점이다.

### CART

CART 같은 단일 트리는 최종 트리의 분할을 따라가면 예측을 이해할 수 있어 해석하기 쉽다. 약점은 두 가지다.

- 탐욕적(greedy) 휴리스틱으로 한 번에 분할 하나씩 트리를 키운다. 지금 시점의 최선인 분할이 전체로 보면 최선이 아닐 수 있어 전역 최적에서 멀어진다.
- 분할을 고르는 방식이, 가능한 분할 수가 많은 변수에 유리하게 편향되어 있다. 매 단계에서 모든 변수의 모든 분할을 탐색하기 때문에 고유한 값이 많은 변수가 우연히 좋은 분할을 가질 가능성이 높다. 이런 변수가 트리의 위쪽에서 잘못 선택될 수 있는데, 위쪽의 변수일수록 중요도가 크게 매겨진다.

### 트리 앙상블

Random Forest와 gradient boosting(XGBoost 등)은 많은 트리의 예측을 합쳐 CART의 문제를 개선하고 높은 성능을 얻었다. 대신 해석 가능성을 잃었다. 수천 개 트리의 상호작용을 사람이 해석하는 것은 불가능하다. 그래서 이 모델들은 변수 중요도로 설명하는 것이 일반적이다.

| 모델 | 중요도 | 계산 |
|---|---|---|
| Random Forest | Gini importance (mean decrease in impurity) | 각 트리의 분할에서 불순도를 얼마나 줄였는지를 변수별로 합산한다 |
| XGBoost | Gain | 그 변수로 분할해서 손실 함수가 얼마나 줄었는가. 가장 많이 쓰인다 |
| | Cover | 그 변수의 분할을 지나간 표본 수 |
| | Frequency | 그 변수가 분할에 쓰인 횟수 |

이 중요도들도 CART와 마찬가지로 고유한 값이 많아 분할 가능성이 큰 변수에 편향될 수 있다.

### Optimal Trees

탐욕적 휴리스틱을 전역 최적화로 바꾸어 문제를 해결하려는 흐름이다. Optimal Trees는 혼합 정수 최적화(mixed-integer optimization)로 전역 최적인 결정 트리를 한 번에 찾는다. 모든 분할을 동시에 고려하므로 분할을 하나씩 고르는 CART보다 편향에 덜 취약하다. 단일 트리의 해석 가능성을 유지하면서 블랙박스 모델에 견줄 만한 성능을 낸다고 한다.

## 3. SHAP

SHAP(SHapley Additive exPlanations)은 게임 이론의 개념을 가져온 설명 기법이다. 각 변수가 최종 예측에 얼마나 기여했는지를 잰다. 변수 선택에는 두 단계로 쓴다.

1. 모든 변수로 모델을 학습한다.
2. 변수마다 SHAP 값을 계산해 순위를 매기고, 예측에 크게 기여하는 변수를 고른다.

SHAP은 내장 중요도의 편향 문제에 대한 해결책으로 제안되었고, 강건하다는 평가 때문에 블랙박스 모델의 설명과 변수 선택 양쪽에서 널리 쓰이게 되었다. 시계열에서도 쓰인다. 예를 들어 시계열 예측에서 온라인으로 트리 기반 모델을 선택할 때, TreeSHAP으로 각 입력 변수가 예측 오차에 얼마나 기여하는지를 계산하는 연구가 있다.

SHAP은 모델의 종류와 무관하게 쓸 수 있다. LRP나 Integrated Gradients는 신경망 구조에 맞춘 기법으로, 같은 방식으로 변수 선택에 쓰인 사례를 문헌 목록에 올려 두었다.

## 4. 비교 논문

### Comparing interpretability and explainability for feature selection

XGBoost 같은 블랙박스 모델은 성능이 좋지만 해석이 쉽지 않아, 변수 중요도 점수나 SHAP 같은 기법에 기댄다. 이 논문은 그렇게 얻은 중요도로 변수 선택을 했을 때의 성능을 여러 모델에 대해 비교한다. 비교 대상은 CART, Optimal Trees, XGBoost(내장 중요도), XGBoost에 SHAP을 적용한 것이다.

결론은 세 가지다.

- **XGBoost는 SHAP을 쓰든 쓰지 않든 변수 선택에 효과적이지 않았다.** 아주 단순한 경우에도, 타깃과 관련이 없는 변수에 큰 중요도를 부여했다.
- **Optimal Trees가 가장 믿을 만했다.** 다룬 모든 경우에서 관련 없는 변수에 중요도를 줄 확률이 가장 낮았고, CART보다 데이터를 효율적으로 썼다.
- **CART에는 편향 문제가 있지만**, 전역 최적화로 개선될 수 있음이 확인되었다.

이 논문에서 얻은 것은 결과보다 지형에 대한 이해였다. XGBoost와 Random Forest를 이용한 변수 선택은 중요도 점수 기반 방법으로 분류된다. 가장 기본이 되는 것이 CART이고 그 위에 앙상블 방법이 있다. 그리고 XGBoost의 부정확한 중요도를 SHAP으로 보완하려는 시도가 있었지만, 이 논문의 실험에서는 문제가 해결되지 않았다.

### Feature selection strategies: a comparative analysis of SHAP-value and importance-based methods

두 가지 변수 선택 방식을 비교한다.

- SHAP 값 기반 선택: 위의 2단계 절차다.
- Importance 기반 선택: 모델의 학습 과정에서 계산되는, 모델에 내장된 변수 중요도를 쓴다.

주의할 점은 SHAP과 모델을 맞세우는 비교가 아니라는 것이다. 같은 모델에 대해 내장 중요도를 쓴 경우와 SHAP으로 중요도를 계산한 경우를 비교한다. 대상 모델은 XGBoost, Decision Tree, CatBoost, Extremely Randomized Trees, Random Forest의 다섯 가지이고, 각각에 SHAP을 적용한 것을 더해 모두 10가지 방법이 된다. LightGBM은 성능이 낮아 제외되었다. 이 논문은 실험 설계까지만 읽었고 결과는 정리하지 못했다.

## 5. 정리

| | 내장 중요도 | SHAP 적용 |
|---|---|---|
| 필요한 것 | 학습된 트리 계열 모델 | 학습된 모델과 SHAP 계산 |
| 계산 시점 | 학습 과정에서 함께 나온다 | 학습이 끝난 뒤 따로 계산한다 |
| 알려진 문제 | 분할 가능성이 큰 변수에 편향된다 | 편향을 줄이려고 제안되었지만, 변수 선택 성능이 나아지지 않았다는 보고가 있다 |

어느 쪽이든 이 방법들이 재는 것은 "모델이 예측에 그 변수를 얼마나 썼는가"다. 변수가 타깃의 원인인지와는 다른 질문이다. Shapley value가 크다고 해서 인과와 연결되는 것은 아니라는 분석은 [Markov blanket 글](05-markov-blanket-feature-selection.md)의 8절에서 다루었다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. 두 번째 논문의 결과는 무엇이고, 첫 번째 논문과 같은 방향의 결론인가.**

논문 초록에 따르면, Kaggle의 Credit Card Fraud Detection 데이터에서 AUPRC로 평가했을 때 내장 중요도로 고른 변수가 SHAP 값으로 고른 변수보다 다섯 분류기와 여러 변수 개수에 걸쳐 더 나은 성능을 냈고, 통계 검정도 이를 뒷받침한다. 그래서 논문은 데이터가 큰 경우 내장 중요도를 먼저 쓰라고 권한다. 내장 중요도는 학습 과정에서 함께 얻어지지만 SHAP은 따로 계산해야 한다는 점도 이유로 든다. "SHAP으로 바꾼다고 변수 선택이 나아지지 않는다"는 점에서는 첫 번째 논문과 같은 방향이다. 다만 두 논문이 본 것은 서로 다르다. 첫 번째 논문은 타깃과 관련 없는 변수에 중요도를 주는지를 보았고, 내장 중요도와 SHAP을 모두 믿기 어렵다고 했다. 두 번째 논문은 고른 변수로 다시 학습한 모델의 성능을 보았고, 내장 중요도 쪽이 낫다고 했다. 데이터셋 하나에서 얻은 결과라는 점도 감안해야 한다.

### 아직 남은 질문

- LRP와 Integrated Gradients를 변수 선택에 쓴 연구는 목록만 모았고 내용을 읽지 못했다. 각 논문이 어떤 모델과 데이터에서 무엇과 비교했는지를 읽어야 한다.

## 참고 자료

- "Comparing interpretability and explainability for feature selection".
- "Feature selection strategies: a comparative analysis of SHAP-value and importance-based methods", Journal of Big Data, 2024. <https://journalofbigdata.springeropen.com/articles/10.1186/s40537-024-00905-w>
- L. Breiman, J. Friedman, R. Olshen, C. Stone, "Classification and Regression Trees". <https://www.taylorfrancis.com/books/mono/10.1201/9781315139470/classification-regression-trees-leo-breiman-jerome-friedman-olshen-charles-stone>
- L. Breiman, "Random Forests". <https://link.springer.com/article/10.1023/A:1010933404324>
- "XGBoost: A Scalable Tree Boosting System". <https://dl.acm.org/doi/10.1145/2939672.2939785>
- "Optimal classification trees". <https://link.springer.com/article/10.1007/s10994-017-5633-9>
- "A Unified Approach to Interpreting Model Predictions" (SHAP). <https://proceedings.neurips.cc/paper_files/paper/2017/file/8a20a8621978632d76c43dfd28b67767-Paper.pdf>
- TreeSHAP을 이용한 온라인 모델 선택 연구. <https://arxiv.org/pdf/2401.01124>
- 트리 앙상블의 중요도와 SHAP을 변수 선택에 쓴 그 밖의 문헌(목록만 모아 둔 것): <https://arxiv.org/pdf/2210.02176>, <https://www.nature.com/articles/s41598-025-01761-1>, <https://pubmed.ncbi.nlm.nih.gov/40216915/>
