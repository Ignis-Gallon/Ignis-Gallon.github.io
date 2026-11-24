---
title: "9주차-2: Clustering, Dijkstra"
date: 2022-11-02
last_modified_at: 2024-11-15
category: major
tags: [알고리즘, 근사 알고리즘, Clustering, Dijkstra]
---

# 9주차-2: Clustering, Dijkstra

**Clustering**

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/01.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/02.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/03.webp)

느낌 상 더 나아보이는 것은 오른쪽, 가장 먼 점을 선택하는 것이다. 왜냐면, 가장 직경이 큰 클러스터의 직경을 최소화해야 하기 때문이다.

→ Greedy Algorithm의 사용으로 볼 수 있음. Approximation ratio가 2보다는 작다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/04.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/05.webp)

→ 클러스터의 헤드를 첫 번째를 랜덤으로 고르는 것은 비효율적일 수도 있다. 이에 대한 알고리즘을 추가해보는 것도 좋다. ex) neighbor가 가장 많은 노드

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/06.webp)

- 가장 큰 노드를 선택한다. D[3] = 20, 가장 거리가 먼 것을 고른다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/07.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/08.webp)

- head가 된 것은, 나중에 후보군에서 탈락한다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/09.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/10.webp)

> 클러스터링이 어떤 개념이고, 어떤 아이디어가 사용되었는가?

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/11.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/12.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/13.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/14.webp)

이차원에서 특정 지점들이 주어졌을 때, 주어진 지점들을 묶기 위해 헤드를 찾는 문제. 그런데, 가장 큰 그룹의 반경이 최소화되어야 하는 문제이다.

1. TSP
2. Edge Cover
3. Bin Packing
4. Job Scheduling
5. Clustering

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/15.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/16.webp)

---

> 맵 매칭 알고리즘 소개

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/17.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/18.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/19.webp)

→ GPS와 지도의 오차를 해결하고자 Map Matching 기술 개발

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/20.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/21.webp)

→ 여기서 라인은 도로가 될 수 있을 것이다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/22.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/23.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/24.webp)

---

**Dijkstra Algorithm**

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/25.webp)

최단 경로라는 것은, 한 네트워크를 그래프로 표현할 수 있을 때, 정점 U에서 시작해서 V까지 가는데 거치는 경로의 가중치 합이 최소가 되는 경로를 찾는 문제이다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/26.webp)

- 큰 카테고리는 최단 경로를 찾는 것이고, 네 개의 sub problem이 있다.

<!-- -->

1. 하나의 정점에서 다른 하나의 정점까지의 최단 경로
2. 하나의 정점에서 다른 모든 정점까지의 최단 경로
3. 하나의 목적지로 가는 모든 최단 경로를 구하는 문제
4. 모든 최단 경로를 구하는 문제

→ 모든 노드에 대해서, 각 노드에서 다른 노드로 가는 모든 최단 경로를 찾는 문제

Dijkstra는 sub problem에서 2번 문제에 대해 다루는 내용이다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/27.webp)

집합 S는 시작 정점 v로부터의 최단 경로가 이미 발견된 정점들의 집합이다.

distance 배열은 계속 update된다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/28.webp)

distance 배열을 먼저 생성한다. size는 node의 개수인 n만큼이다.

시작 정점이 v라면, distance[v] = 0. 자기 자신에게 가는 거리는 0이므로.  
시작 정점과 직접적인 연결이 없는 정점은 distance를 INF로 세팅한다.  
이를 인접 행렬로 표현할 수 있다.

매 단계에서 distance를 업데이트하면서, 거리가 가장 작은 정점을 추가한다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/29.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/30.webp)

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/31.webp)

dijkstra는 시작 정점은 주어지며, 모든 다른 노드까지의 최단 거리를 찾는다.

![](../../../assets/img/study/major/algorithms/09-week-09-2-clustering-dijkstra/32.webp)
