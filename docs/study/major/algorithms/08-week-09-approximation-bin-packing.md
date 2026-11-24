---
title: "9주차: 근사 알고리즘, 통 채우기 문제"
date: 2022-10-27
last_modified_at: 2024-11-15
category: major
tags: [알고리즘, 근사 알고리즘, 통 채우기, 작업 스케줄링]
---

# 9주차: 근사 알고리즘, 통 채우기 문제

작성일 2022-10-27 · 최종 수정일 2024-11-15
{ .post-date }

- Approximation Algorithms : 최적화된 해에 최대한 근접하게 접근하여 해를 찾는 방식

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/01.webp)

- 각 물건의 크기는 통보다는 작다.
- 통에 물건을 넣으려고 하려면, 그 통에 물건이 들어갈 여유 공간이 있어야 한다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/02.webp)

- Greedy Algorithm : 현재 할 수 있는 최선의 선택을 한다. 구현과 실행이 쉽지만, 근시안적인 선택으로 optimal solution이 나오지 않을 수 있다. Greedy는 optimal solution 실행 시간의 최소 2배 이하를 보장한다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/03.webp)

1. 최초 (처음) 적합 (First Fit) : 첫 번째 통부터 차례로 살펴보며, 가장 먼저 여유가 있는 통에 새 물건을 넣는다. 
2. 다음 적합 (Next Fit) : 직전에 물건을 넣은 통에 여유가 있으면 새 물건을 넣는다.
3. 최선 적합 (Best Fit) : 기존의 통 중에서 새 물건이 들어가면 남는 부분이 가장 작은 통에 새 물건을 넣는다.
4. 최악 적합 (Worst Fit) : 기존의 통 중에서 새 물건이 들어가면 남는 부분이 가장 큰 통에 새 물건을 넣는다.

<!-- -->

- 각 방법으로 새 물건을 기존의 통에 넣을 수 없으므로 새로운 통에 새 물건을 넣는다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/04.webp)

- 처음 적합(First Fit) : 첫 번째 통부터, 새로운 물건이 들어갈 수 있다면 넣어주는 방식. 넣을 수 없다면 새로운 통을 만들어준다.
- 다음 적합(Next Fit) : 다섯 번째로 2가 들어가는데, 이전에 넣을 곳에 들어갈 수 없다면, 그냥 늘려버린다. (7번째로 들어가는 7도 새로 만들어진다. 들어갈 수 없기 때문이다. 비효율적으로 보인다.)
- 최선 적합 (Best Fit) : 지금 있는 통들 중에서(현재 활성화되어 있는 통들 중에서), 여유 공간이 새로운 물건의 사이즈와 가장 맞는 공간에 넣는다.
- 최악 적합 (Worst Fit) : Worst, 낭비가 심할수록 좋은 것이므로 물건의 사이즈와 비교했을 때 공간이 가장 많은 곳에, 여유가 있는 곳에 넣는다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/05.webp)

- 입력은 n개 물건의 각각의 크기이고, 출력은 통의 개수이다.
- 네 개의 방식 중 하나를 선택하여 if( ) 문을 완성한다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/06.webp)

- Next fit은 기존의 통을 둘러보지 않으므로, O(n)이다. 빅오는 최악의 경우, 최소한의 성능을 guarantee 해준다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/07.webp)

- Greedy를 사용하므로, 통의 개수는 최적해에서 2배 이상을 넘지 않는다. 

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/08.webp)

- optimal solution은 모든 물건 크기의 합 / C 보다 크거나 같다. (추후에 증명에 사용)

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/09.webp)

OPT’ ⇒ Approximation Solution

- OPT’ 통이 만약 다섯개가 있다면, 1개의 통은 1/2 이하이고, 4개의 통은 반 이상이라는 뜻이다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/10.webp)

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/11.webp)

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/12.webp)

---

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/13.webp)

- t(1) : 1번 job의 수행시간, t(2) : 2번 job의 수행시간 … 이게 크다는 것은 cpu가 해당 job을 수행하는데 시간이 많이 필요하다는 뜻이다.
- 모든 작업이 가장 빨리 종료되도록 작업을 기계에 배정하는 문제이다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/14.webp)

- 새로운 job을 assign 할 때, 비어있는 것에 빨리 끝나는 job부터 채워준다. 만약 위에 있는 6개의 job에서 추가로 1개의 job이 추가된다면, 어느 곳에서 실행되는가? 2번에서 실행돼야 할 것이다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/15.webp)

- output은 모든 작업이 종료된 시간이다. 
- for j = 1 to m : 머신의 숫자가 m.
- L[]은 기계 M에 배정된 마지막 작업의 종료 시간
- 3번 라인부터는 가장 일찍 끝나는 기계를 찾는다.
- job i를 수행이 가장 빨리 끝나는 머신에 할당된다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/16.webp)

- Line 4 : 가장 일찍 끝나는 기계의 번호 min → Greedy Algorithm임을 알 수 있음.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/17.webp)

- 처음에는 네 개의 머신이 비어있으므로, 순서대로 job을 machine에 할당한다.
- 가장 빨리 끝나는 machine에 새로운 job을 할당한다는 것은 사실 job을 고르게 분배하는 것을 내포해서 job이 최대한 빨리 끝나도록 함을 목표로 한다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/18.webp)

- 이후에도 마찬가지로, 순서대로 job이 가장 빨리 끝나는 machine에 배정해준다.

L[0] = 8

L[1] = 10

L[2] = 13

L[3] = 10

이 중에서 가장 큰 값을 return하므로, return L[2]; ⇒ 13

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/19.webp)

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/20.webp)

- JobScheduling도 마찬가지로, 최적 해의 2배를 넘지 않는다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/21.webp)

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/22.webp)

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/23.webp)

- Greedy Algorithm은 최적해의 최소 2배의 performance 내에는 포함된다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/24.webp)

---

- 클러스터 : 정부 사업 등에서 자주 등장하는 용어로, 특정 지역구를 묶어주는 것. 여기에서는 클러스터의 중심이 되는 head를 center라고 표현한다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/25.webp)

- 각 클러스터 내에서 비어있는 점은 head(center)이다.
- 최적화 문제에서 이런 형태를 Min/Max 문제라고 한다. 클러스터링 문제는 center를 중심으로 하는 원(반경) 가장 큰 값을 최소화 하는 것이므로, Min/Max 문제의 하나에 속한다.

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/26.webp)

- 첫 번째 센터를 random하게 선택해본다. 

![](../../../assets/img/study/major/algorithms/08-week-09-approximation-bin-packing/27.webp)

- 그 후 두 번째 센터를 선택하는 방법은 첫 번째 센터에서 가장 가까운 점을 고르거나, 가장 먼 점을 고르는 방법이 있다.  그런데, 직관적으로 봐서는 당연히 먼 점을 고르는 것이 좋으므로 먼 점을 선택하는 방법을 채택한다.
