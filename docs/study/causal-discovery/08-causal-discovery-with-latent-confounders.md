---
title: "잠재 교란이 있을 때의 시계열 인과 발견: FCI에서 LPCMCI까지"
date: 2025-05-20
last_modified_at: 2025-05-21
category: study
tags: [인과 발견, 잠재 교란 변수, FCI, LPCMCI, PAG, 시계열]
---

# 잠재 교란이 있을 때의 시계열 인과 발견: FCI에서 LPCMCI까지

작성일 2025-05-20 · 최종 수정일 2025-05-21
{ .post-date }

대부분의 인과 발견 방법은 모든 공통 원인이 관측되었다는 인과 충분성을 가정한다. 실제 데이터에서 이 가정이 성립하는 경우는 드물다. 이 글에서는 인과 충분성 없이 구조를 찾는 방법들을 정리하고자 한다. 이런 방법이 무엇을 출력하는지(MAG가 아니라 PAG인 이유)를 먼저 보고, FCI와 그 시계열 확장인 tsFCI, SVAR-FCI, LPCMCI, TS-ICD를 차례로 살펴본다.

## 1. 가정의 사슬

방법을 보기 전에, DAG를 복원하는 방법이 어떤 가정을 어떤 순서로 쌓는지 다시 정리했다. 각 가정은 앞의 가정만으로 부족한 부분을 메우려고 들어온다. 정의는 [인과 그래프와 가정](03-causal-graphs-and-assumptions.md)에 있다.

1. DAG로 인과 구조를 나타내려면 **인과 충분성**이 필요하다.
2. **Markov condition**은 각 변수가 부모를 조건으로 모든 비자손과 독립이라는 것이다. 이를 바탕으로 DAG $G$에서 d-separation을 평가할 수 있고, $G$의 d-separation이 분포 $P$의 조건부 독립으로 나타나면 $P$와 $G$가 호환된다고 한다.
3. 그런데 서로 다른 DAG가 같은 $P$와 호환될 수 있다. 분포만으로는 인과 추론을 할 수 없다.
4. 그래서 **minimality**가 들어온다. $P$가 $G$의 어떤 진부분그래프와도 호환되지 않아야 한다. 간선이 더 많은 그래프가 같은 $P$를 표현한다면 불필요한 의존성을 담고 있는 것이다.
5. 이것만으로는 unfaithful한 경우를 막지 못한다. Markov condition은 그래프에서 분포로 가는 방향만 보장하고, 역은 일반적으로 성립하지 않는다. 파라미터 값 때문에 우연히 독립이 나타날 수 있기 때문이다. 그래서 **faithfulness**를 가정한다. $P$의 모든 조건부 독립이 $G$의 구조에서만 나온다고 보면, $P$로부터 $G$를 거꾸로 추정할 수 있다.
6. 그래도 모든 간선의 방향이 정해지지는 않는다. 결국 찾는 것은 **Markov equivalence class**다.

아래의 방법들은 1번을 버린다. 그러면 2번 이하의 가정은 대상 그래프를 바꾸어 유지되고, 6번의 equivalence class도 다른 종류의 그래프가 된다.

## 2. 무엇을 복원하는가: MAG와 PAG

인과 충분성을 가정하는 방법은 DAG를 복원 대상으로 삼는다. 조건부 독립성만 쓰는 방법이라면 그 Markov equivalence class까지다. DYNOTEARS, VARLiNGAM, PCMCI와 그 확장인 F-PCMCI가 모두 DAG를 대상으로 한다. 숨은 교란 변수와 selection bias는 고려하지 못한다.

MAG(Maximal Ancestral Graph)는 인과 충분성 없이 관측 변수 사이의 구조를 표현한다. 양방향 간선($\leftrightarrow$)으로 숨은 교란 변수를, undirected 간선($-$)으로 관측되지 않은 selection 변수를 나타낸다.

그렇다면 MAG를 복원하면 되지 않는가. 조사해 보니 MAG 자체를 복원하는 알고리즘은 있지만, 주로 개입 데이터나 강한 구조적 가정, 또는 점수 기반 탐색에 의존한다. 잠재 교란 변수와 selection bias가 있는 일반적인 상황에서 관측 데이터만으로 MAG 하나를 완전히 정하는 것은 불가능하다. 같은 조건부 독립성을 나타내는 MAG가 여럿이기 때문이다. 그래서 가능한 MAG들의 Markov equivalence class를 나타내는 PAG(Partial Ancestral Graph)를 복원하는 것이 일반적이다. MAG 복원은 강한 가정이 충족될 때만 실용적이고, PAG 복원이 더 현실적인 선택으로 받아들여지고 있다.

처음에는 "MAG 기반 방법"이라는 이름으로 시계열 방법을 찾았는데, 찾은 방법이 거의 모두 PAG를 출력했다. 잠재 교란을 다루는 시계열 방법은 사실상 PAG 기반이라고 정리했다.

| 그래프 | 복원하는 방법의 가정 | 한계 |
|---|---|---|
| DAG (의 equivalence class) | 인과 충분성 | 숨은 교란 변수와 selection bias를 표현하지 못한다 |
| MAG | 인과 충분성은 없지만, 개입 데이터나 강한 구조적 가정이 추가로 필요하다 | 일반적인 상황에서는 하나로 정해지지 않는다 |
| PAG | Markov condition, faithfulness | 방향이 정해지지 않은 간선이 남는다 |

### 식별 가능성의 두 가지 뜻

이 과정에서 identifiability라는 말이 두 가지 뜻으로 쓰인다는 것을 구분하게 되었다.

| | 구조의 식별 가능성 | 인과 효과의 식별 가능성 |
|---|---|---|
| 질문 | 참인 인과 그래프(DAG, MAG)를 유일하게 정할 수 있는가 | $P(Y \mid do(X = x))$ 같은 인과적 양을 관측 데이터만으로 계산할 수 있는가 |
| 어려움 | Markov equivalence class가 있어 대부분 유일하게 정해지지 않는다 | 잠재 변수나 selection bias가 있으면 일반적으로 식별되지 않는다 |
| 대응 | equivalence class(PAG)를 식별한다 | do-calculus나 ID 알고리즘으로 식별 가능한 조건을 판정한다 |

## 3. FCI

FCI(Fast Causal Inference)는 이 계열의 출발점이다. PC 알고리즘에 기반한 제약 기반 방법으로, 숨은 교란 변수와 selection bias가 있을 수 있는 상황에서 관측 변수 사이의 PAG를 복원한다.

약점은 계산량이다. 조건부 독립성 검정의 수가 변수 수에 따라 지수적으로 늘어난다. 이를 줄이려고 RFCI(Really Fast Causal Inference)가 제안되었고, FCI+ 같은 변형도 있다.

제약 기반 방법은 기본적으로 비모수적 조건부 독립성 검정을 쓰기 때문에 차원의 저주에 취약하다는 점도 약점이다. 이를 피하려는 시도 가운데 하나가 Chu와 Glymour(2008)의 ANLTSM(additive non-linear time series model)이다. 숨은 교란 변수가 선형이고 동시점으로 작용하는 경우를 다룬다.

$$
X_t^q = \sum_{\substack{1 \le p \le d \\ p \ne q}} a_{q,p} X_t^p

+ \sum_{\substack{1 \le p \le d \\ 1 \le l \le \tau}} f_{q,p,l}\big(X_{t-l}^p\big)
+ \sum_{r=1}^{h} b_{q,r}\, U_t^r + \xi_t
$$

$a$와 $b$는 상수, $f$는 매끄러운 일변수 함수다. $U^r$과 $\xi$는 관측되지 않은 가우시안 백색 잡음으로, $U^r$은 여러 변수에 함께 작용하는 잠재 공통 원인이고 $\xi$는 해당 변수에만 직접 작용하는 관측되지 않은 원인이다. 동시점 항은 선형, 지연 항은 가법적인 비선형으로 제한했기 때문에 고차원의 비모수 검정을 피할 수 있다. 이 방법은 ANLTSM에 따라 생성된 데이터에 대해 일관성을 보였다.

## 4. 시계열로의 확장

### tsFCI

Entner와 Hoyer(2010)는 FCI를 시계열에 적용했다. 원래의 시계열을 길이 $\tau$의 sliding window로 잘라 확률 벡터로 바꾸고, 그 위에서 FCI를 수행한다. 시계열의 배경지식 두 가지를 반영한다.

- temporal priority: 미래가 과거의 원인일 수 없으므로 시점이 다른 간선의 방향이 미리 정해진다.
- consistency throughout time: 같은 지연의 관계는 모든 시점에서 같다.

출력은 window 단위의 PAG다. FCI와 달리 tsFCI는 selection 변수와 동시점 관계를 고려하지 않는다.

### SVAR-FCI와 SVAR-RFCI

Malinsky와 Spirtes(2018)는 다변량 시계열을 위한 SVAR-FCI를 제안했다. 데이터 생성 과정으로 잠재 변수가 있는 structural vector autoregression(SVAR)을 가정한다. tsFCI와 달리 동시점 인과 관계와 임의의 잠재 교란을 함께 고려한다. 정상성(stationarity)을 이용해 간선을 추가로 제거한다. 한 시점 쌍에서 간선이 지워지면 같은 지연의 다른 시점 쌍에서도 지울 수 있다. SVAR-RFCI는 같은 생각을 RFCI에 적용한 것이다.

### LPCMCI

Gerhardus와 Runge(2020)는 PCMCI를 확장한 LPCMCI(Latent PCMCI)를 제안했다. 선형과 비선형 시계열에서, 잠재 교란 변수가 있는 상황의 지연 관계와 동시점 관계를 모두 다룬다.

논문의 문제의식은 자기상관이다. 자기상관이 강한 시계열에서는 조건부 독립성 검정의 효과 크기(effect size)가 작아져 검정력이 떨어지고, 그 결과 실제로 있는 간선을 놓친다. 제목의 high-recall은 이 문제를 겨냥한 것이다. 해결 방식은 세 가지다.

- 인과적 부모를 기본 조건 집합에 넣어 효과 크기를 키우고 검정력을 높인다.
- 간선 제거 단계와 방향 결정 단계를 분리하지 않고 엮는다. 초기 단계부터 방향 정보를 뽑아내고, 그 정보를 다시 조건 집합을 정하는 데 쓴다.
- 새로운 방향 결정 규칙과 중간 단계의 그래프 구조를 도입해 PAG를 더 일찍, 더 정확하게 식별한다.

공개된 LPCMCI 저장소에는 비교 대상인 SVAR-FCI와 SVAR-RFCI도 함께 구현되어 있다.

### ICD와 TS-ICD

ICD(Iterative Causal Discovery, NeurIPS 2021)는 잠재 교란 변수와 selection bias가 있을 수 있는 상황을 위한 알고리즘이다. 원래 논문은 시계열을 다루지 않는다.

- causal Markov assumption과 faithfulness에 의존하며, 바탕에 있는 인과 그래프의 equivalence class를 복원한다.
- 모든 노드 쌍이 연결된 complete graph에서 시작해, 연결된 노드 사이의 조건부 독립성을 확인하며 그래프를 점진적으로 다듬는 하나의 반복 단계로 이루어진다.
- sound하고 complete하다. soundness는 찾아낸 관계가 항상 참이라는 것이고, completeness는 주어진 가정 아래에서 알아낼 수 있는 관계를 모두 찾아낸다는 것이다.
- 매 반복이 끝난 시점의 독립성과 인과 관계가 모두 옳으므로, 중간에 멈추어도 그때까지의 결과를 쓸 수 있다(anytime).

TS-ICD("From Temporal to Contemporaneous Iterative Causal Discovery in the Presence of Latent Confounders", ICML 2023)는 그 시계열 확장이다.

- 이산 시간의 정상 SVAR 과정을 가정하고, 지연 관계와 동시점 관계를 모두 고려한다.
- 시간 순서에 따라 각 시점의 변수를 내생과 외생으로 나누어 조건부 독립성 검정의 수를 줄인다.
- 지연이 긴 관계에서 시작해 짧은 지연, 동시점 관계 순서로 가짜 간선을 제거한다. 이 순서가 정확도에 중요하다고 한다.
- 같은 지연을 갖는 간선들은 정상성에 따라 함께 움직이므로, 그 가운데 하나가 인과적이지 않다고 판정되면 나머지도 제거한다. 이것으로 계산량을 줄인다.

논문은 SVAR-FCI, SVAR-RFCI, LPCMCI와 비교해 더 적은 검정 횟수로 더 높은 정확도를 얻었다고 보고한다. 실제 데이터로는 하천 유량 데이터를 쓴다.

### LPCMCI 이후

- **CAnDOIT**: LPCMCI의 확장이다. 숨은 요인이 있는 상황에서 관측 시계열과 개입 시계열을 함께 써서 인과 모형을 재구성한다.
- **PD-DAG**: 매개 변수 $M$이 관측되지 않으면($A \to M \to B$) $A$와 $B$의 관계가 직접적인지 간접적인지 알기 어렵다. 관측 가능한 미래 시점의 변수를 숨은 변수의 proxy로 삼아 숨은 변수의 영향을 반영하고, 이를 통해 직접 인과와 매개된 인과를 구분해 summary graph를 복원할 수 있음을 보였다.

### 정리

| 방법 | 연도 | 기반 | 출력 | 동시점 관계 | 특징 |
|---|---|---|---|---|---|
| FCI | | PC | PAG | (비시계열) | 숨은 교란과 selection bias를 허용한다 |
| RFCI | | FCI | PAG | (비시계열) | 검정 횟수를 줄인다 |
| ANLTSM | 2008 | 가법적 비선형 모델 | | 선형 동시점 항 | 차원의 저주를 피하려고 모델을 제한한다 |
| tsFCI | 2010 | FCI + sliding window | window PAG | 고려하지 않음 | temporal priority와 consistency throughout time을 쓴다 |
| SVAR-FCI, SVAR-RFCI | 2018 | FCI/RFCI + SVAR | PAG | 고려함 | 정상성으로 간선을 추가 제거한다 |
| LPCMCI | 2020 | PCMCI | PAG | 고려함 | 부모를 조건에 넣어 검정력을 높인다 |
| TS-ICD | 2023 | ICD | PAG | 고려함 | 긴 지연에서 동시점 순서로 학습한다 |
| CAnDOIT | | LPCMCI | | | 개입 데이터를 함께 쓴다 |

## 5. 제약 기반이 아닌 접근

잠재 교란을 다루는 방법이 모두 제약 기반인 것은 아니다. 학회 논문을 훑으며 본 것 가운데 다음이 있다. 시계열을 대상으로 한 것은 아니다.

- 비선형 SCM을 가정하고 VAE로 관측 변수와 잠재 교란 변수의 구조를 함께 복원하는 방법("Nonlinear Causal Discovery with Latent Confounders")
- 잠재 변수가 있는 LiNGAM(lvLiNGAM) 구조를 고차 누적량으로 학습하는 방법
- 뼈대의 사후 분포를 이용해 MAG를 연속 최적화로 학습하는 방법
- proxy 변수로 조건부 독립성 검정의 왜곡을 보정하는 방법

MAG의 구조 학습을 직접 다루는 논문으로는 "Maximal Ancestral Graph Structure Learning via Exact Search", "ExMAG: Learning of Maximally Ancestral Graphs", "On Scoring Maximal Ancestral Graphs with the Max–Min Hill Climbing Algorithm"을 목록에 올려 두었다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. "MAG는 DAG의 equivalence class"라는 설명은 맞는가.**

Markov equivalence class라는 뜻이라면 맞지 않다. 그 말은 같은 조건부 독립성을 나타내는 같은 종류의 그래프들의 집합에 쓰며, DAG들의 class는 CPDAG로, MAG들의 class는 PAG로 나타낸다. 다만 MAG 하나가 여러 DAG를 대표하는 것은 사실이다. 잠재 변수를 포함한 DAG가 주어지면 관측 변수 위의 MAG는 하나로 정해지지만, 반대로 MAG 하나에는 잠재 변수의 수와 연결이 다른 DAG가 얼마든지 대응한다. $A \leftrightarrow B$는 잠재 공통 원인이 하나인 DAG도, 둘인 DAG도 나타낸다. 이 DAG들은 관측 변수 사이의 조건부 독립성과 조상 관계가 같다는 점에서 한데 묶인다. 정리하면 MAG는 "관측 변수에서 보면 구분되지 않는 DAG들"을 대표하고, PAG는 "독립성만으로는 구분되지 않는 MAG들"을 대표한다.

**Q. tsFCI가 selection 변수와 동시점 관계를 고려하지 않는다면 잠재 교란 변수만 다루는 것인가.**

그렇다. Malinsky와 Spirtes(2018)는 tsFCI가 동시점 인과 영향의 가능성을 배제한다고 설명하고, 이를 허용한 것을 SVAR-FCI의 차이점으로 든다. 즉 tsFCI가 가정하는 데이터 생성 과정에는 지연된 인과 관계와 잠재 변수만 있다. selection 변수가 없으므로 출력에는 selection을 뜻하는 방향 없는 간선($-$)이 나오지 않고, $\to$, $\leftrightarrow$와 끝이 정해지지 않은 $\circ\!\!\to$, $\circ\!\!-\!\!\circ$만 나온다. 출력이 window 위의 PAG이므로 MAG 계열의 표현을 쓴다는 이해도 맞다. 덧붙이면, 동시점 인과가 없다고 가정해도 같은 시점의 두 변수가 잠재 공통 원인을 가지면 둘 사이에 간선이 남을 수 있다. 이 가정 아래에서 그 간선은 인과가 아니라 잠재 교란으로 읽어야 한다.

**Q. ICD는 인과 충분성 없이 어떻게 causal Markov assumption과 faithfulness에 의존하는가.**

두 가정의 대상이 관측 변수만의 그래프가 아니기 때문이다. ICD 논문은 causal Markov assumption, faithfulness와 함께 바탕의 인과 관계가 DAG라고 가정하고, 그 DAG를 관측 변수 $O$, selection 변수 $S$, 잠재 변수 $L$ 위의 $D(O, S, L)$로 둔다. 모든 공통 원인을 변수로 포함한 이 DAG에는 Markov condition을 그대로 적용할 수 있다. 앞에서 인과 충분성이 필요하다고 정리한 것은 관측 변수만으로 DAG를 그리고 거기에 Markov condition을 적용하려는 경우였다. ICD는 그렇게 하지 않고, $D$에서 잠재 변수를 주변화하고 selection 변수를 조건화해 얻는 MAG를 대상으로 삼는다. 전체 DAG의 d-separation 가운데 관측 변수에 대한 것은 MAG의 m-separation과 일치하므로, 관측 데이터의 조건부 독립성 검정 결과를 MAG의 구조로 읽을 수 있다.

**Q. ICD는 complete graph에서 시작한다. TS-ICD는 모든 시점의 모든 변수가 연결된 상태에서 시작하는가, 그리고 이를 어떻게 줄이는가.**

window 안에서는 그렇다. 논문의 알고리즘은 최대 지연 $\tau$까지의 각 시점마다 관측 변수 전체를 $\circ\!\!-\!\!\circ$로 이은 완전 그래프를 만들고, 서로 다른 시점의 모든 변수 쌍을 과거에서 현재로 향하는 $\circ\!\!\to$로 잇는다. 줄이는 것은 초기 그래프가 아니라 검정의 대상과 순서다. 시간을 건너는 간선의 화살촉은 처음부터 고정해 방향을 다시 정하지 않는다. 매 반복에서는 현재 시점의 노드와 과거 노드를 잇는 간선을 시간 간격이 긴 것부터 검정하고, 그 다음에 현재 시점 안의 동시점 간선을 검정한다. 한 간선이 독립으로 판정되면 시간 간격이 같은 간선들(논문의 용어로 homology)을 모두 함께 지운다. 조건 집합의 크기를 0부터 하나씩 늘려 가며 이 과정을 반복하는 것은 ICD와 같다.

### 아직 남은 질문

- 시계열에서 PAG가 아니라 MAG를 직접 복원하는 방법이 있는지는 찾지 못했다. 5절에 목록으로만 적어 둔 MAG 구조 학습 논문들이 시계열을 다루는지 확인해야 한다.

## 참고 자료

- C. K. Assaad, E. Devijver, E. Gaussier, "Survey and Evaluation of Causal Discovery Methods for Time Series". (FCI의 시계열 확장, ANLTSM)
- P. Spirtes, C. Glymour, R. Scheines, "Causation, Prediction, and Search". (FCI)
- T. Chu, C. Glymour, "Search for Additive Nonlinear Time Series Causal Models", 2008.
- D. Entner, P. O. Hoyer, "On Causal Discovery from Time Series Data using FCI", PGM 2010.
- D. Malinsky, P. Spirtes, "Causal Structure Learning from Multivariate Time Series in Settings with Unmeasured Confounding", 2018.
- A. Gerhardus, J. Runge, "High-recall causal discovery for autocorrelated time series with latent confounders", NeurIPS 2020. <https://proceedings.neurips.cc/paper/2020/file/94e70705efae423efda1088614128d0b-Paper.pdf>
- "Iterative Causal Discovery in the Possible Presence of Latent Confounders and Selection Bias", NeurIPS 2021.
- "From Temporal to Contemporaneous Iterative Causal Discovery in the Presence of Latent Confounders", ICML 2023.
- "CAnDOIT: Causal Discovery with Observational and Interventional Data from Time Series".
- "Nonlinear Causal Discovery with Latent Confounders", ICML 2023.
- "Causal Discovery with Latent Confounders Based on Higher-Order Cumulants", ICML 2023.
- "Scalable Differentiable Causal Discovery in the Presence of Latent Confounders with Skeleton Posterior", KDD 2024.
- "Causal Discovery via Conditional Independence Testing with Proxy Variables", ICML 2024.
- "Causal Effect Identification in LiNGAM Models with Latent Confounders", ICML 2024.
