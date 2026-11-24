---
title: "13주차: Backtracking, NP, TSP"
date: 2022-11-24
last_modified_at: 2024-11-15
category: major
tags: [알고리즘, Backtracking, NP, TSP]
---

# 13주차: Backtracking, NP, TSP

작성일 2022-11-24 · 최종 수정일 2024-11-15
{ .post-date }

- Backtracking Algorithm

백트래킹을 하는 이유는 해 탐색, 전체 큰 카테고리는 optimal solution 중 하나이다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/01.webp)

느낌상 그리디와 비슷한데, 그리디는 현재 상황에서 (근시안적으로) 좋은 선택을 하는 것인데 한 번 선택하거나 결정하면 되돌릴 수 없다. 이와 다르게 Backtracking은 solution을 되돌아가서 선택할 수 있는 알고리즘이다.

NP(non-polynomial) : 다항시간 안에 해결되는 해를 찾을 수 있을지 없을지 조차 완전하게 증명할 수 없는 문제… 

---

- TSP문제 (그리디로 적용도 가능 하지만, Backtracking으로도 적용이 가능하다.)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/02.webp)

- 대칭성

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/03.webp)

- 대부분의 문제는 기존에 있는 solution을 적용하여 해결하게 될 수도 있다. TSP도 마찬가지로 MST를 이용하면 solution을 얻는데 활용할 수 있다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/04.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/05.webp)

중간에 중복 방문된 부분을 뺄려고 한다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/06.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/07.webp)

- tour는 도시의 순서(sequence)이고, 거리는 bestSolution으로 들어간다. 초기에는 tour가 시작점밖에 없으므로 (처음엔) tour는 시작점을 의미하고, 거리는 무한대로 초기화한다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/08.webp)

- if, else구조이다. tour가 완전한 해(모든 노드를 다 방문한 경우)는 if로 들어가고, 아니면 else로 들어간다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/09.webp)

- 확장 가능한 점 : 현재 tour(sequence)에 없고, 가장 마지막에 방문한 점과 선분으로 연결된 점…

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/10.webp)

- pruning : 잘라낸다, 가지치기, 버리기를 한다는 의미의 용어

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/11.webp)

- 시작점은 A이므로 tour = A. tour는 sequence, bestSolution은 지금까지 방문한 것의 거리이다. 

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/12.webp)

확장할 수 있는 점이란, 가장 최근에 방문한 점과 연결성을 갖는 점을 의미한다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/13.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/14.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/15.webp)

한 branch의 끝에 도달하면 optimal solution은 아니더라도, 완전한 해를 하나 얻게된다. 현재 첫 번째로 얻게된 solution은 A→B→C→D→E→A = 30이다. 

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/16.webp)

이런 식으로 재귀 호출을 따라가며 계속 찾아간다…

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/17.webp)

현재까지는 A→B 에서 나온 branch를 탐색한 것이고, 계속해서 찾아간다. 이 과정에서 지속적으로 가지치기를 수행하게 된다.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/18.webp)

- 탐색해보면서, 한 branch의 최종 bestSolution을 얻기 전에, 이전에 얻은 bestSolution의 값(여기서는 16) 보다 크거나 같다면, 그냥 버린다. pruning.

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/19.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/20.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/21.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/22.webp)

![](../../../assets/img/study/major/algorithms/13-week-13-backtracking-np-tsp/23.webp)

- factorial or exponential time이 소요된다.
