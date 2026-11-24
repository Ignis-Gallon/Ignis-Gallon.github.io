---
title: "Extreme value와 abrupt change"
date: 2025-02-07
last_modified_at: 2025-02-23
category: study
tags: [시계열, 추세 필터링, extreme value, abrupt change, distribution shift]
---

# Extreme value와 abrupt change

DTF-net 논문은 기존 trend filtering의 문제를 "extreme value와 abrupt change를 구분하지 못한다"는 말로 설명한다. 두 값 모두 분포의 꼬리에서 나타나는데, 기존 방법은 이를 가리지 않고 함께 smoothing한다는 것이다. 이 설명을 이해하려면 두 개념이 어떻게 다른지부터 알아야 했다. 이 글은 논문과 관련 자료를 읽으며 두 개념의 정의와 차이, 그리고 abrupt change를 찾는 일이 왜 더 어려운지를 정리한 노트다.

[1편](01-decomposition-vs-trend-filtering.md)에서 trend filtering이 무엇인지 정리했고, 이 글은 그 trend filtering이 지키려는 것(abrupt change)과 버리려는 것(노이즈, extreme value)을 다룬다.

## Extreme value

Extreme value는 데이터 분포의 양 끝, 즉 꼬리 부분에 있는 값이다. 값 자체가 평균에서 지나치게 멀어진 극단치를 뜻한다.

- 추출한 추세나 패턴 위의 값들에 확률 분포를 가정하고, 통계적 임계값(threshold)을 정해 그 값을 넘는 것으로 정의한다.
- 전체 데이터에서 나타나는 빈도가 매우 낮다.
- 양의 극단값과 음의 극단값은 서로 다른 메커니즘으로 생길 수 있다(asymmetry).
- 분포에 따라 그 분포의 특성을 고려한 모델링이 필요하다. 봉우리가 여럿인 분포, 비대칭 분포처럼 정형적이지 않은 분포도 있기 때문이다.

Extreme value와 노이즈도 구분할 필요가 있다. Extreme value는 실제 사건이나 상황 때문에 나타난 매우 크거나 작은 값이다. 노이즈는 무작위적이고 불규칙한 변동으로, 그 자체로는 의미가 없으며 모델이 예측하기 어려운 부분이다. 노이즈가 많으면 데이터의 변동성이 커져 extreme value를 가려내기 어려워진다.

정리하면 extreme value는 어떤 지점의 값을 분포에 비추어 보았을 때 통계적 임계값을 넘는 값이다. 시계열에서는 특정 시점에 날카롭게 솟았다가 빠르게 원래 수준으로 돌아오는 값으로 나타난다. 오래 유지되지 않으므로 데이터 전체의 통계적 특성을 바꾸지는 않는다.

## Abrupt change

Abrupt change는 추세가 급격한 전환을 겪는 지점이다. DTF-net 논문은 다음과 같이 정의한다.

> An abrupt change denotes a point in a time series where the trend experiences a sharp transition, signaling a change in slope. Given that abrupt changes determine the direction and persistence of the slope, it is crucial to incorporate them into the trend.

즉 기울기가 바뀌는 지점이고, 그 뒤의 기울기 방향과 지속성을 결정하므로 추세에 반드시 포함해야 하는 지점이다.

- 추출한 추세나 패턴 위에서, 도메인에 따라 다르게(domain-specific) 정의된다.
- 일반적으로는 평균, 분산 같은 통계적 성질이 갑자기 변하는 지점이다. 다시 말해 distribution shift를 일으키는 지점이다.
- 일시적인 변동이 아니다. 변화 뒤의 새로운 평균 수준이나 분산이 이어진다.
- 다변량 시계열에서는 여러 변수의 상호작용으로 여러 변수에서 동시에 나타나는 경우가 많다.

처음에는 abrupt change와 노이즈의 관계가 헷갈렸는데, 다음과 같이 정리했다. 추세는 노이즈를 제거한 값이다. abrupt change는 그 추세가 급격히 변하는 지점이므로 노이즈와는 관련이 없다. 추세가 급격히 변한다는 것은 통계적 성질이 변한다는 것이다.

## 두 개념의 비교

### 한 시계열 안에서 값이 변하는 모양

같은 시점 $t$에서 값이 크게 뛰더라도 그 뒤가 다르다.

- **Extreme value**: 시점 $t$에서 날카로운 피크로 나타났다가 빠르게 이전 수준으로 돌아온다.
- **Abrupt change**: 시점 $t$에서 갑자기 오르거나 내린 뒤, 새로운 수준이 그대로 이어진다.

Abrupt change는 distribution shift를 일으키는 지점이므로 비정상(non-stationary) 데이터를 설명할 때 특히 중요하다. 비정상 데이터에서는 seasonality 같은 반복 패턴이 잘 드러나지 않기 때문에, 추세의 변화를 잡아내는 것이 그만큼 중요해진다.

### 다변량 시계열에서 보는 관점

- **Extreme value**: 단변량 시계열 하나에서 나타나는 특이값이다. 특정 시점의 값이 같은 시계열의 다른 시점에 비해 비정상적으로 크게 벗어난다. 여러 시계열을 함께 놓고 보면 그중 하나에서만 일부 시점의 값이 튀는 모습이다.
- **Abrupt change**: 다변량 시계열의 관점에서, 데이터 분포로부터 나타나는 특성이다. 여러 시계열이 공통으로 보이는 변화다.

이 구분은 다음과 같이 이해했다. 다변량 시계열의 변수들은 서로 영향을 주고받으므로, 분포가 바뀌는 수준의 변화가 한 변수에서만 일어나는 경우는 드물다. 반대로 한 변수에서만 값이 튀었다면 그것은 그 변수의 extreme value일 가능성이 높다.

| | Extreme value | Abrupt change |
|---|---|---|
| 정의 | 분포의 꼬리에 있는 극단치 | 추세의 기울기가 급격히 바뀌는 지점 |
| 판단 기준 | 값 자체 (통계적 임계값) | 통계적 성질의 변화 (distribution shift) |
| 지속성 | 일시적, 곧 원래 수준으로 돌아온다 | 변화 뒤의 수준이 이어진다 |
| 추세에 대한 영향 | 추세에 큰 영향을 주지 않는다 | 기울기의 방향과 지속성을 정한다 |
| 다변량 관점 | 한 시계열에서 튀는 값 | 여러 변수에 걸친 분포의 변화 |
| 접근 방법 | 확률 분포에 기반한 정형화된 방법 (Extreme Value Theory) | 도메인에 따라 다른 접근 |

## Abrupt change를 찾는 일이 더 어려운 이유

Extreme value는 Extreme Value Theory(EVT)로 꼬리 분포를 모델링하고, 꼬리에서 적절한 임계값을 정해 찾아낼 수 있다. 확률 분포에 기반한 정형화된 방법이 있고, 도메인과 무관하게 같은 통계적 방법으로 접근할 수 있다. 대체로 한 지점의 극단적인 값을 찾는 문제다.

Abrupt change는 그렇지 않다. 정리한 이유는 네 가지다.

1. **여러 인과관계가 얽혀 있다.** distribution shift는 한 시계열 안에서 생기기보다 다변량 시계열 사이의 복잡한 상호작용으로 생긴다. 그래서 여러 변수의 상호작용을 함께 고려해야 한다.
2. **변화 지점이 여러 개다.** 한 지점이 아니라 시계열의 여러 지점에서 변화가 생길 수 있다. distribution shift는 계속 일어나기 때문이다.
3. **변화의 유형이 다양하다.** 변화는 급격하게(abrupt) 일어날 수도, 점진적으로(gradual) 일어날 수도, 특정 주기나 패턴에 따라 반복적으로 일어날 수도 있다.
4. **비선형성과 이분산성이 있다.** 변수 사이의 관계가 선형으로 표현되지 않을 수 있고, 시간에 따라 분산이 일정하지 않을 수 있다. 어떤 구간에서는 조금씩 움직이다가 다른 구간에서는 크게 움직이는 식이다. 그래서 단순한 선형 모델이나, 모든 구간의 분산이 같다는 가정을 쓰기 어렵다.

3번을 정리하면서 든 생각도 있다. 비선형적인 distribution shift라도 주기적으로 반복된다면 그것은 하나의 패턴으로 인식할 수 있다. 그런 경우에는 변화라기보다 정상성의 일부로 파악할 수도 있을 것이다.

쉽게 말하면 extreme value는 값만 보고 판단할 수 있지만, abrupt change는 값만으로 판단하기 어렵다. 그 지점이 통계적 특성의 변화를 일으키는가를 보아야 하고, 그 기준은 도메인마다 다르다. 단순히 임계값을 정하는 방식으로는 잡아낼 수 없다.

그래서 분포를 고려해 extreme value를 찾아내는 것은 하위 문제이고, 데이터 안의 패턴을 찾아 그 패턴 안에서 abrupt change를 탐지하는 것이 더 어렵고 중요한 문제라고 정리했다.

## 헷갈렸던 부분

"abrupt change는 꼬리 분포에 extreme value와 같이 존재한다"는 설명을 처음 읽었을 때, 그렇다면 abrupt change는 값의 분포에서 항상 꼬리에만 있는가 하는 의문이 들었다.

꼭 그렇지는 않다는 것이 정리한 답이다. 전체 데이터의 분포 하나만 놓고 볼 것이 아니라, 일부 구간에서의 분포를 고려해야 한다. distribution shift는 국소적으로 계속 나타날 수 있으므로, abrupt change는 "그 주변 구간의 분포에 비추어 꼬리에 있는 값"이라고 볼 수 있다. 그리고 그 국소적인 꼬리에는 노이즈, extreme value, abrupt change가 섞여 있다.

이것이 trend filtering이 어려운 이유이기도 하다. Trend filtering은 원본과의 근접성을 유지하면서 smoothing으로 노이즈를 제거해 추세와 abrupt change를 찾으려 한다. 그런데 abrupt change가 노이즈, extreme value와 함께 꼬리에 있으니, 노이즈만 골라 제거하기가 어렵다. 꼬리를 한꺼번에 깎는 방법은 abrupt change까지 깎아 버린다.

## Change point와의 관계

비슷한 말로 change point가 있다. Change point는 데이터의 통계적 특성(평균, 분산, 분포 등)이 변하는 지점이다. 급격한 변화가 아니더라도 통계적 특성이 변하는 모든 지점을 포함하므로, abrupt change를 포함하는 더 넓은 개념으로 볼 수 있다. Change point를 찾는 방법은 [3편](03-change-point-detection-overview.md)에서, 용어 사이의 관계는 [4편](04-change-point-terms.md)에서 정리한다.

## 참고 자료

- "Towards Dynamic Trend Filtering through Trend Point Detection with Reinforcement Learning", IJCAI 2024. <https://arxiv.org/abs/2406.03665>
- D. Ding, M. Zhang, X. Pan, M. Yang, X. He, "Modeling Extreme Events in Time Series Prediction", KDD 2019. <https://doi.org/10.1145/3292500.3330896> ([리뷰](../../papers/2025-02-04-modeling-extreme-events.md))
