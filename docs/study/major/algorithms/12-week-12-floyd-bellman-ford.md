---
title: "12주차: Floyd, Bellman-Ford"
date: 2022-11-17
last_modified_at: 2024-12-01
category: major
tags: [알고리즘, Floyd, Bellman-Ford, SPFA, 최대 유량]
---

# 12주차: Floyd, Bellman-Ford

작성일 2022-11-17 · 최종 수정일 2024-12-01
{ .post-date }

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/01.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/02.webp)

특징 : 모든 정점 사이의 최단 경로를 다 찾는다. (All pairs!)  
2차원 배열 A를 이용하여 3중 반복 루프를 구성한다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/03.webp)

A^(n-1)[i][j]는 0부터 n-1까지의 모든 정점을 이용한 최단경로이다.  

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/04.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/05.webp)

---

- Floyd 구현

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/06.webp)

---

- 벨만-포드 알고리즘

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/07.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/08.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/09.webp)

1~2 까지는 초기화이고, 3~4번이 메인이다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/10.webp)

벨만 포드 알고리즘은 다익스트라와 같이 시작점은 정해져있지만, 음수 가중치가 존재할 수 있다. 시작 정점을 1번 노드라고 할 때, 1번의 가중치는 0이고 나머지는 INF이다. (우선적으로 초기화 상태이므로,)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/11.webp)

시작 정점인 1번과 관련있는 노드가 2번이므로, 2번을 갱신한다. 1 → 2의 비용은 5이므로 업데이트한다. 

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/12.webp)

4번의 경우, 1→2→4의 가중치를 합쳐서 8로 설정한다. 5번은 음수값이 고려되므로 가중치가 달라진다. 1→2→5 는 5 - 1 이므로, 가중치는 4이다. 

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/13.webp)

이 경우는 4~5가 아직 뚫리지 않은 것이다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/14.webp)

4번 까지 뚫린 경우. 4번은 갈 수 있는 노드가 없으므로 넘어간다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/15.webp)

밑줄 친 특성 다익스트라도 해당함.

---

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/16.webp)

이전에는 3→5가 양수였지만, 여기서는 음수로 교체한다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/17.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/18.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/19.webp)

가중치에 양수만 있다면, 다익스트라를 택한다. 하지만 가중치에 음수가 있다면 벨만 포드를 활용한다. 단, 시간 복잡도는 벨만 포드가 더 크다.

---

- 최단 경로 알고리즘 (SPFA : Shortest Path Faster Algorithm)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/20.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/21.webp)

벨만 포드와 거의 동일하지만, SPFA는 갱신되는 정점만 방문한다는 점에서 차이가 있다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/22.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/23.webp)

큐가 비워질 때, 함수가 종료된다. 그래서 벨만 포드보다 더 빠르다.

---

- 플로이드 와샬 알고리즘

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/24.webp)

음수  가중치가 있어도 사용 가능한 플로이드 알고리즘

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/25.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/26.webp)

1번을 기준으로 갱신할 점이 있다면 테이블을 갱신한다. 1번을 기준으로 할 때 현재 예제에서는 갱신할 것이 없으므로 넘어간다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/27.webp)

플로이드-와샬도 이전과 마찬가지로 하나씩 노드를 뚫어가면서 경유했을 때, 더 짧은 거리가 나오면 갱신하는 것이라고 생각하면 된다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/28.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/29.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/30.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/31.webp)

---

- Maximum Flow Algorithms

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/32.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/33.webp)

- 잔여 용량 : 현재 간선에 실제로 흐르는 유량을 빼서 남는 간선 공간…
- 증가 경로(augment path) : 시작점에서 도착점으로 보낼 수 있는 경로.

더 이상 augmenting path가 없으면 iteration을 멈춘다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/34.webp)

> 중요한 내용은 용량 제한 속성, 유량의 대칭성이다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/35.webp)

ex) 0/1 : 현재 flow 0, capacity 1

S → T의 경로(augmented path)는 총 세 개있는데, 이 중 capacity가 가장 큰 것을 찾는 것이 문제이다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/36.webp)

S → a의 남는 용량은 0, a→T의 남는 용량은 1.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/37.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/38.webp)

- 역방향을 고려해서 추가적인 경로가 존재할 수 있는지 찾아본다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/39.webp)

ex) b→a는 실제로 존재하지 않는 경로이므로, C는 0, f는 -1

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/40.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/41.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/42.webp)

둘 다 똑같은데, 차이점이 하나 있다. DFS, BFS. 상황에 따라서 DFS로 하면 시간이 많이 걸리는데 BFS를 하면 빠른 경우도 있고, 반대의 경우도 있다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/43.webp)

c_f(p)에 따라서 업데이트가 될 수 있는 것으로 보인다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/44.webp)

빨간색은 유량 상쇄를 의미하며, 추가적인 경로를 찾기 위해 사용한다. 

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/45.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/46.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/47.webp)

오른쪽은 C(Capacity) 왼쪽은 F(Flow)이다. 시작점은 1, 도착점은 4인데 계속 유량을 흘려보낼 수 있는지 없는지를 체크한다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/48.webp)

Augmenting Path : residual이 작은 값으로 경로를 채운거

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/49.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/50.webp)

이제 유량 상쇄를 할 수 있다.

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/51.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/52.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/53.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/54.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/55.webp)

![](../../../assets/img/study/major/algorithms/12-week-12-floyd-bellman-ford/56.webp)

에드몬드-카프가 대부분의 경우 더 효율적이다.
