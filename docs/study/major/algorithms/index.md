---
title: "알고리즘"
---

# 알고리즘

2학년 2학기에 수강한 알고리즘 수업의 노트입니다. 기본 자료구조와 정렬에서 시작해 그래프 탐색, 최소 신장 트리, 근사 알고리즘, 최단 경로, Dynamic Programming, Backtracking, 허프만 압축까지 다룹니다. 주차별 노트 뒤에는 학기 후반부의 내용을 주제별로 다시 정리한 글을 묶었습니다.

**주차별 노트**

- [1주차(Stack Queue Deque, List, Tree)](01-week-01-stack-queue-deque-list-tree.md): 스택·큐·덱의 ADT와 배열을 이용한 스택 구현, 연결 리스트(SLL, DLL)의 연산, Binary Search Tree
- [2주차(Recursion, Tree, Big-O)](02-week-02-recursion-tree-big-o.md): 재귀의 Base Case, 이진 트리의 종류, 추상 데이터 타입, Big-O·Big-Ω·Big-θ 표기법
- [3주차(Various Sorting)](03-week-03-various-sorting.md): Selection Sort, Insertion Sort, Bubble Sort의 비교·이동 횟수와 정렬의 안정성
- [4주차(Merge, Quick, Heap)](04-week-04-merge-quick-heap.md): Merge Sort, Quick Sort, Heap Sort, 분할 정복, 선택 문제, 최근접 점의 쌍 찾기
- [5주차(Graph Traversal Algorithm)](05-week-05-graph-traversal.md): 스케줄링 기준과 알고리즘, 그래프 용어와 ADT, DFS와 BFS
- [6주차: 가중치 그래프, Greedy, Kruskal](06-week-06-weighted-graph-greedy-kruskal.md): 인접 행렬을 이용한 가중치 표현, 신장 트리와 MST, Greedy Algorithm, Kruskal Algorithm
- [7주차: Prim MST, 근사 알고리즘](07-week-07-prim-mst-approximation.md): Prim 알고리즘으로 MST를 만드는 과정, Kruskal과의 비교, 여행자 문제와 정점 커버 문제의 근사 알고리즘
- [9주차: 근사 알고리즘, 통 채우기 문제](08-week-09-approximation-bin-packing.md): First Fit·Next Fit·Best Fit·Worst Fit, 작업 스케줄링 문제, 클러스터링 문제의 도입
- [9주차-2: Clustering, Dijkstra](09-week-09-2-clustering-dijkstra.md): 가장 먼 점을 다음 센터로 고르는 Clustering, 맵 매칭 소개, Dijkstra Algorithm
- [10주차: Dijkstra, Floyd](10-week-10-dijkstra-floyd.md): 배열 D를 갱신하는 Dijkstra의 절차, Point-to-Curve 맵 매칭, 모든 쌍의 최단 경로를 구하는 Floyd
- [11주차: 맵 매칭, Dynamic Programming, Knapsack](11-week-11-map-matching-dp-knapsack.md): 맵 매칭 알고리즘, 분할 정복과 DP의 차이, 0-1 Knapsack 문제
- [12주차: Floyd, Bellman-Ford](12-week-12-floyd-bellman-ford.md): Floyd 구현, 음수 가중치를 다루는 Bellman-Ford와 SPFA, Floyd-Warshall, 최대 유량 문제
- [13주차: Backtracking, NP, TSP](13-week-13-backtracking-np-tsp.md): Greedy와 Backtracking의 차이, MST를 활용한 TSP 풀이, 가지치기
- [14주차 (1): 허프만 압축](14-week-14-1-huffman.md): 접두부 특성, 문자 빈도수 기반 이진 트리로 허프만 코드를 만드는 과정, 시간 복잡도
- [14주차 (2): DP 동전 거스름돈](15-week-14-2-dp-coin-change.md): 거스름돈을 1원씩 늘려 가며 최소 동전 수를 1차원 배열 C에 저장하는 DP 풀이

**기말 총정리**

- [Approximation Algorithm](16-final-approximation-algorithm.md): 통 채우기, 작업 스케줄링, 클러스터링 문제와 각 방법의 시간 복잡도
- [Dijkstra](17-final-dijkstra.md): 최단 경로 문제의 네 가지 유형, 집합 S에 정점을 추가하며 거리를 갱신하는 과정, 시간 복잡도
- [Floyd](18-final-floyd.md): 2차원 배열 A를 3중 반복으로 갱신해 모든 정점 사이의 최단 경로를 찾는 방법
- [Dynamic Programming](19-final-dynamic-programming.md): 분할 정복과의 차이, 0-1 배낭 문제의 부분 문제 K[i, w]와 알고리즘의 흐름
- [Bellman-Ford Algorithm](20-final-bellman-ford.md): 음수 가중치 간선이 있을 때의 최단 경로, 음수 사이클 판단, 시간 복잡도
- [SPFA Algorithm](21-final-spfa.md): 거리가 갱신된 정점에 인접한 간선만 다시 확인하는 Shortest Path Faster Algorithm
- [Floyd-Warshall Algorithm](22-final-floyd-warshall.md): 음수 가중치가 있어도 모든 쌍 최단 경로를 구하는 방법, 음수 사이클의 판단
- [Maximum Flow Problem](23-final-maximum-flow.md): 용량·유량·잔여 용량 등 기본 용어, 유량 상쇄, 포드-풀커슨과 에드몬드-카프 알고리즘
- [Backtracking](24-final-backtracking.md): 최적화 문제와 결정 문제, MST를 이용한 TSP 풀이, BacktrackTSP 알고리즘의 수행 과정
- [Huffman Coding](25-final-huffman-coding.md): 접두부 특성, 우선순위 큐에서 빈도수가 낮은 두 노드를 합쳐 트리를 만드는 절차, 압축률 계산
- [DP coinChange](26-final-dp-coin-change.md): C[j-dk]에 동전 1개를 더해 C[j]를 구하는 과정과 Greedy 풀이와의 비교
