---
title: "7주차: Prim MST, 근사 알고리즘"
date: 2022-10-13
last_modified_at: 2024-12-01
category: major
tags: [알고리즘, Prim, MST, 근사 알고리즘, TSP]
---

# 7주차: Prim MST, 근사 알고리즘

## # `MST Prim`

- 이전의 kruskal은 정점의 가중치를 정렬하고 선택하므로, 정점을 선택할 수 없다. Prim은 이와 다르게 주어진 가중치 그래프에서 임의의 정점 하나를 선택한 후, (n-1)개의 간선을 하나씩 추가시켜 트리를 만든다.
- 추가되는 간선은 현재까지 만들어진 트리에 연결시킬 때 ‘욕심을 내어서 `Greedy`’ 항상 최소의 가중치로 연결되는 간선이다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/01.webp)

- kruskal은 edge를 추가하는 것이고, prim은 vertex를 추가하는 것이다.   
    즉, 선택된 vertex에서 neighbor를 선택한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/02.webp)

- 만약 두 개의 vertex의 weight가 같을 경우, 알파벳, 숫자 순으로 낮은 것을 선택한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/03.webp)

- 현재 선택된 vertex는 a와 f인데, MST Prim은 선택된 vertex의 neighbors 들 중에서 weight이 가장 작은 vertex를 선택하므로, 다음 iteration에서는 e가 포함될 것이다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/04.webp)

1. [ a ] a가 MST에 들어간다. 현재 neighbor는 b, f가 있는데, 인접 neighbor들 중 가중치가 작은 것이 f이므로, f를 선택해서 MST의 member로 f를 포함한다.
2. [ a, f ] neighbor 중 f-e가 가장 작으므로, e를 선택해서 넣는다.
3. [ a, f, e ] 위와 같은 방식으로 계속해서 진행한다.
4. [ a, f, e, d ]
5. [ a, f, e, d, c ]
6. [ a, f, e, d, c, b ]
7. [ a, f, e, d, c, b, g]

> 현재 선택된 영역을 기준으로 neighbor를 판단해서 weight이 가장 작은 vertex를  MST에 집어넣는다.

- sudo code (1)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/05.webp)

V는 Graph에 있는 vertex의 집합이다.

Outer for loop은 추가하면서 delete하는 것이고, 아래 있는 for는 작은 distance를 선택한다는 것을 수식으로 표현한 것이다.

- (2)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/06.webp)

1. 시작점 p를 정하고, D[p] = 0으로 설정한다. 즉, p가 가진 distance를 0으로 설정한다.  
    // D[v]는 T에 있는 정점 u와 v를 연결하는 간선의 최소 가중치를 저장하기 위한 것.  
    // u는 이미 선택된 영역이고, v는 아직 방문하지 않은 vertex를 의미한다.
2. 정점 p가 아닌 각 정점 v에 대하여 간선 p, v가 그래프에 있으면, D[v] = 가중치,  
    그게 아니면 inf

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/07.webp)

- while은 n-1번 돌아간다. 
- T는 MST with distance이다.
- (2)번 방식 설명

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/08.webp)

- 선택하지 않은 영역에서 고르는 이유는, 선택한 영역에서 고르면 Cycle이 선택될 수 있기 때문이다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/09.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/10.webp)

- Vmin이라는 것은, 10, 7, 15 중 작은 것. 그것이 Vmin이라고 보면 된다. 

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/11.webp)

- 처음 시작하는 노드는 가중치를 거쳐서 방문하지 않으므로, D[c] = 0으로 초기화한다.  
    아직 선택되지 않은 다른 모든 노드는 inf로 초기화하는 것으로 보인다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/12.webp)

- D[b] = 1, D[f] = 1. 나머지는 D[v] = inf
- b와 f의 가중치가 같으므로, c-b를 선택.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/13.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/14.webp)

- b-f가 2이지만, 이전에 결정된 D[f] = 1로 더 작으므로, 업데이트 하지 않는다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/15.webp)

- distance 업데이트는 가장 최근에 선택된 vertex를 기준으로 진행한다. 이 단계에서는 가장 짧은 것이 a이므로, a를 선택한다. 

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/16.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/17.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/18.webp)

- Prim은 선택되지 않은 영역으로부터 연결을 하므로, Cycle이 발생하지 않는다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/19.webp)

- 루프가 n-1번 반복될 수 있고, D[v]가 최소인 점 Vmin을 찾는데 O(n)시간이 걸린다. 왜냐하면 1차원 배열 D에서 최소값을 찾는 것이고, 배열의 크기는 그래프의 정점의 개수가 n이기 때문이다.
- Prim의 알고리즘은 O($n^2$)의 복잡도
- Kruskal과 Prim 알고리즘의 수행 과정 비교

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/20.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/21.webp)

- Sparse는 rough한 그래프로, dense의 반대말이다.  
    노드 수가 적으면 Kruskal이 유리하며, 많으면 Prim이 유리함.

## # `근사 알고리즘`

- NP-완전 문제의 해결 (Non-Polynomial 문제)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/22.webp)

- NP 문제란, 다항시간 안에 풀 수 있는지 없는지 조차 모르는 문제들을 말한다.
- NP 문제의 조건을 하나 정도 빼면, relax된 상태로 접근할 수 있게 된다.
- NP-Complete, NP-Hard…

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/23.webp)

- 근사 알고리즘은, 만약 최적해가 존재한다고 가정하면 Approximation한 Solution이 최소 최적해의 2배보다는 낫다. 라고 볼 수 있다. 즉, Greedy Algorithm 자체는 최적해가 존재한다고 하면, 최적해의 실행시간의 2배 보다는 낫다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/24.webp)

- `여행자 문제`

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/25.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/26.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/27.webp)

- MST와 TSP가 다른 이유는, TSP는 중복 없이 방문해야만 하는 문제이기 때문이다.
- 그래서, 이를 해결하려면 중복되는 걸 없애고, 위에 있는 사진의 4번 째 그림처럼 이어준다.
- 이를 근사해로 볼 수 있다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/28.webp)

- 결국 모든 정점을 한 번씩만 방문해야 하기 때문에, 시작 정점을 제외한 나머지 정점은 중복을 제거해야만 한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/29.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/30.webp)

- 순서는 Distance까지 같이 저장할 수 있다.

<!-- -->

1. Prim or Kruskal을 이용해 MST를 생성한다.
2. MST가 있더라도, 방문하는 방법은 다 다를 수 있으므로 그건 사용자에 따라 정한다.
3. 중복을 제거하되, 마지막 도시는 제거하지 않는다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/31.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/32.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/33.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/34.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/35.webp)

- `정점 커버 문제`

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/36.webp)

- 정점 커버 문제, 이게 무슨 문제냐 하면 그래프 G가 주어지면, 각 선분의 양 끝점들 중에서 적어도 하나의 끝점을 포함하는 점들의 집합들 중에서 최소 크기의 집합을 찾는 문제이다.
- 가장 적은 수의 집합을 선택하여 모든 노드를 커버하는 집합을 찾는다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/37.webp)

- 위 예제에서 나올 수 있는 모든 정점 커버의 집합들을 찾고, 거기서 최소 크기를 찾는다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/38.webp)

- 카메라를 가능한 적게 설치해서, 모든 복도를 감시할 수 있도록 (Optimal) 카메라를 구성한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/39.webp)

- 결국에는, 가장먼저 어떤 점을 선택해야 하는지를 생각해보아야 한다.
- 선택된 선분의 양 끝점에 인접한 선분이 모두 커버된다. (인접 노드는 커버됨)
- 정점 커버는 vertex의 집합으로, 주어진 Graph의 모든 vertex에 대하여 연결성을 가져야 한다.
- 단, 최소성을 가져야 한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/40.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/41.webp)

- 매칭이란, 각 선분의 양쪽 끝점들이 중복되지 않는 선분의 집합.
- 더 이상 새로운 선분을 추가할 수 없는 상태를 극대 매칭이라고 한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/42.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/43.webp)

- a의 양쪽 두 점, b 의 양쪽 두 점 … f의 양쪽 두 점을 리턴한다.

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/44.webp)

![](../../../assets/img/study/major/algorithms/07-week-07-prim-mst-approximation/45.webp)
