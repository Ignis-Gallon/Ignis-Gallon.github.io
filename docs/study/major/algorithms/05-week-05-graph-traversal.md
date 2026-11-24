---
title: "5주차(Graph Traversal Algorithm)"
date: 2022-09-29
last_modified_at: 2022-10-12
category: major
tags: [알고리즘, 스케줄링, 그래프, DFS, BFS]
---

# 5주차(Graph Traversal Algorithm)

## # `스케쥴링 알고리즘`

**`스케쥴링 알고리즘`**

- Scheduling

    1. Preemptive scheduling policy : 프로세스가 프로세서(CPU)를 선점하는 방식  
        interrupts processing of a job and transfers the CPU to another job  
        emergent한 process가 생기면, 먼저 emergent한 job을 실행한다. 이전까지 실행한 job의 과정을 기록해두고 job이 다시 재개될 때 돌아간다.

    2. Non-preemptive scheduling policy :   
        특정 process 수행이 끝나기 전까지 CPU를 차지해서 사용함.

- Scheduling Algorithm Criteria (CPU 설계에서 고려할 점들)

    1. CPU utilization - keep the CPU as busy as possible  
        (CPU를 가능한 바쁘게 만드는 것)  
        Maximize CPU efficiency(utilization) by keeping CPU busy 100% of time.

    2. Throughput - # of processes that complete their execution per time unit  
        (단위 시간동안 얼마나 많은 양을 처리할 수 있는가)  
        Maximized throughput by running as many jobs as possible in a given amount of time.  
        \+ Ensure fairness for all jobs by giving everyone an equal amount of CPU an I/O time.

    3. Turnaround time - amount of time to execute a particular process  
        (특정한 job을 실행하는 시간, 반환 시간)

    4. Waiting time - amount of time a process has been waiting in the ready queue  
        (job이 queue에서 대기하고 있는 시간)

- Process Scheduling Algorithms

    1. First Come First Served (FCFS) -  Non-Preemptive
    2. Shortest Job Next (SJN) - Non-Preemptive
    3. Priority Scheduling
    4. Shortest Remaining Time (SRT) - preemptive
    5. Round Robin - preemptive  
        (여러 개의 큐가 순환하면서 Job을 처리하는 것)

---

## # `그래프(Graph)`

**`그래프(Graph)`**

- `그래프` : 연결되어 있는 객체(노드) 간의 관계를 표현하는 자료구조
- `그래프의 예` :  
    트리  
    지도, 지하철 노선도  
    전기회로의 연결 상태  
    OS의 프로세스와 자원 관계 → 시스템의 목적에 따라 처리되는 순서, 기준을 정함.

- `오일러 정리`

    모든 정점에 연결된 간선의 수가 짝수이면 오일러 경로가 존재한다. (NP 문제)

- `그래프 정의`

    그래프 G는 (V, E)로 표시  
    V는 Vertex의 set을 의미하며, E는 Edge(또는 link)들의 set을 의미한다.

- `그래프의 종류`

    1. `무방향 그래프 (undirected graph)`  
        간선을 통해서 양방향으로 갈 수 있음  
        (A, B)로 표현  
        (A, B) = (B, A)

    2. `방향 그래프 (directed graph)`  
        간선을 통해서 한 쪽 방향으로만 갈 수 있음, 일방통행 길  
        &lt;A, B&gt;로 표현  
        &lt;A, B&gt; ≠ &lt;B, A&gt;

> `Graph 용어`

- `가중치 그래프 (weighted graph)`

    간선에 비용(cost)이나 가중치(weight)가 할당된 그래프

- `부분 그래프 (Sub graph)`

    정점 집합 V(G)와 간선 집합 E(G)의 부분 집합으로 이루어진 그래프

- `인접 정점 (adjacent vertex)`

    하나의 정점에서 간선에 의해 직접 연결된 정점

- `차수 (degree)`

    - `무방향 그래프`  
        하나의 정점에 연결된 다른 정점의 수  
        차수의 총 합은 간선 수의 2배이다.
    - `방향 그래프`  
        진입 차수 (in-degree) : 외부에서 오는 간선의 수  
        진출 차수 (out-degree) : 외부로 향하는 간선의 수

- `그래프의 경로 (path)`

    - `무방향 그래프`의 정점 s로부터 정점 e까지의 경로  
        정점의 나열 s, v1, v2, …, vk, e  
        반드시 간선 (s, v1), (v1, v2), …, (vk, e) 존재해야 한다.
    - `방향 그래프`의 정점 s로부터 정점 e까지의 경로  
        정점의 나열 s, v1, v2, …, vk, e  
        반드시 간선 &lt;s, v1&gt;, &lt;v1, v2&gt;, …, &lt;vk, e&gt; 존재해야 한다.

- `경로의 길이 (length)`

    경로를 구성하는데 사용된 간선의 수  
    → weighted graph에서는 경로의 길이에 대한 정의가 달라질 수 있다.

- `단순 경로 (simple path)`

    경로 중에서 반복되는 간선이 없는 경로

- `사이클 (cycle)`

    단순 경로의 시작 정점과 종료 정점이 동일한 경로

- `연결 그래프`

    무방향 그래프 G에 있는 모든 정점에 대하여 항상 경로가 존재하는 그래프

- `완전 그래프`

    모든 정점이 연결되어 있는 그래프  
    n 개의 정점을 가진 무방향 완전 그래프의 간선의 수

    $$
    n * (n - 1) / 2
    $$

- `그래프 ADT`

    1. `create_graph()`  
        그래프를 생성한다.
    2. `init(g)`  
        그래프 g를 초기화한다.
    3. `insert_vertex(g, v)`  
        그래프 g에 정점 v를 삽입한다.
    4. `insert_edge(g, u, v)`  
        그래프 g에 간선 (u, v)를 삽입한다.
    5. `delete_vertex(g, v)`  
        그래프 g의 정점 v를 삭제한다
    6. `delete_edge(g, u, v)`  
        그래프 g의 간선 (u, v)를 삭제한다.
    7. `is_empty(g)`  
        그래프 g가 공백 상태인지를 확인한다.
    8. `adjacent(v)`  
        정점 v에 인접한 정점들의 리스트를 반환한다.
    9. `destroy_graph(g)`  
        그래프 g를 제거한다.

- `그래프 표현 방법`

    1. 인접 행렬 방법
    2. 인접 리스트 방법

---

## # `그래프 탐색(Graph Search)`

**`그래프 탐색(Graph Search)`**

- `Graph Search or Traversal`

    1. `깊이 우선 탐색 (DFS: Depth First Search)`
    2. `너비 우선 탐색 (BFS: Breadth First Search)`

- `DFS`

    한 방향으로 갈 수 있을 때까지 가다가, 더 이상 갈 수 없게 되면 가장 가까운 갈림길로 돌아와서 이곳으로부터 다른 방향으로 다시 탐색을 진행한다.  
    → 되돌아 가기 위해서는 `스택` 필요 (`Recursive Call` 활용으로 묵시적인 스택 이용)

- `BFS`

    시작 정점으로부터 가까운 정점을 먼저 방문하고 멀리 떨어져 있는 정점을 나중에 방문하는 순회 방법. 큐를 사용하여 구현됨.

```text
breadthFirstSearch(root)
    root를 방문되었다고 표시;
    큐 Q에 정점 root를 삽입;
    while (is_not_empty(Q)) do
        Q에서 dequeue한 값이 정점 v;
        for all w ㅌ (v에 인접한 정점) do
            if (w가 방문되지 않았으면)
                then w를 방문되었다고 표시;
                       w를 큐에 삽입;
```
