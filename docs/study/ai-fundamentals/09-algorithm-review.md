---
title: "알고리즘 복습: 정렬, 그래프 탐색, 최단 경로, greedy와 backtracking"
date: 2024-12-01
last_modified_at: 2024-12-01
category: study
tags: [알고리즘, Merge Sort, Quick Sort, DFS, BFS, Dijkstra, Greedy, Backtracking]
---

# 알고리즘 복습: 정렬, 그래프 탐색, 최단 경로, greedy와 backtracking

기본 알고리즘을 짧게 복습한 노트다. 정렬 두 가지와 Dijkstra는 C 코드를 한 줄씩 따라가며 동작을 말로 풀어 적었고, 나머지는 정의와 시간 복잡도 위주로 정리했다. 코드는 교재에서 흔히 볼 수 있는 표준적인 구현이다.

## 정렬

### Merge sort

Divide and conquer 방식이다. 데이터를 반으로 계속 나눈 다음, 가장 작은 단위부터 정렬하면서 다시 합쳐 올라온다. 시간 복잡도는 $O(n \log n)$이다.

```c
void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}
```

처음에는 배열의 첫 index와 마지막 index를 넘긴다. `left`가 `right`보다 작으면 가운데를 기준으로 둘로 나누고, 나눈 양쪽에 대해 `mergeSort`를 재귀 호출한다. 원소가 하나만 남으면 더 나누지 않는다.

```c
void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    int *L = (int *)malloc(n1 * sizeof(int));
    int *R = (int *)malloc(n2 * sizeof(int));

    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int i = 0; i < n2; i++) R[i] = arr[mid + 1 + i];

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j])
            arr[k++] = L[i++];
        else
            arr[k++] = R[j++];
    }

    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];

    free(L);
    free(R);
}
```

합치는 과정은 다음과 같다.

1. `left`부터 `mid`까지, `mid + 1`부터 `right`까지의 크기만큼 메모리를 따로 잡고 값을 그대로 복사한다. 두 개의 임시 배열이 생기고, 각각을 가리키는 index `i`, `j`를 둔다.
2. 왼쪽과 오른쪽의 맨 앞 값을 비교해 더 작은 쪽을 원래 배열 `arr`에 차례로 채운다. 왼쪽의 첫 값이 2이고 오른쪽의 첫 값이 3이라면 2를 넣고 왼쪽 index를 1 늘린다.
3. 한쪽을 먼저 다 쓰면, 다른 쪽에 남아 있는 값을 그대로 `arr`에 옮긴다.

양쪽이 각각 이미 정렬된 상태에서 합치므로 합친 결과도 정렬되어 있다. 가장 작은 단위에서부터 이 과정이 반복되어 올라오므로 최종적으로 전체가 정렬된다. 임시 배열을 쓰기 때문에 입력 크기에 비례하는 추가 메모리가 필요하다.

### Quick sort

Pivot을 기준으로 데이터를 둘로 나누고 각 부분을 재귀적으로 정렬하는 divide and conquer 알고리즘이다. Pivot의 선택에 큰 영향을 받는다. 평균 시간 복잡도는 $O(n \log n)$이고 최악은 $O(n^2)$이다.

```c
void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);

        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}
```

`low`와 `high`는 구간의 첫 index와 마지막 index다. `low < high`일 때만 partition을 진행한다는 점은 merge sort와 같고, 구간에 원소가 하나만 남으면 재귀가 끝난다.

```c
int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low - 1;

    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            swap(&arr[i], &arr[j]);
        }
    }
    swap(&arr[i + 1], &arr[high]);
    return i + 1;
}
```

1. 구간의 마지막 원소를 pivot으로 정하고, `i`를 `low - 1`로 둔다.
2. `j`가 `low`부터 `high` 바로 앞까지(pivot은 제외) 순회한다.
3. `j` 위치의 값이 pivot보다 작으면 `i`를 1 늘리고 `i`와 `j` 위치의 값을 바꾼다. `i`는 pivot보다 작은 값들을 앞쪽에 몰아 두는 경계이고, `j`는 순회하면서 그런 값을 찾아 주는 역할이다.
4. 순회가 끝나면 `i + 1` 위치와 pivot을 바꾼다. 이제 pivot 앞에는 pivot보다 작은 값만, 뒤에는 그렇지 않은 값만 있다.

Pivot은 자기 자리를 찾았으므로 더 움직일 필요가 없다. Pivot을 기준으로 왼쪽과 오른쪽 구간에 대해 각각 재귀 호출을 하면 된다.

최악의 경우는 나눌 때마다 한쪽으로 쏠리는 경우다. 위 구현처럼 마지막 원소를 pivot으로 쓰면, 이미 정렬된 배열이 들어올 때 그렇게 된다.

## 그래프 탐색: DFS와 BFS

- **DFS(깊이 우선 탐색)**: 그래프나 트리에서 한 경로를 끝까지 따라간 다음, 되돌아오면서(backtracking) 다른 경로를 탐색한다. 재귀 또는 스택을 쓴다.
- **BFS(너비 우선 탐색)**: 시작 노드에 인접한 노드를 먼저 모두 탐색하고, 그다음 단계의 노드로 넘어간다. 큐를 쓴다. 간선의 가중치가 없는 그래프에서 최단 경로를 찾는 데 적합하다.

시간 복잡도는 그래프를 어떻게 저장하느냐에 달려 있다.

| 표현 | 시간 복잡도 | 이유 |
|---|---|---|
| 인접 행렬 | $O(V^2)$ | $V \times V$ 행렬 전체를 살펴야 한다 |
| 인접 리스트 | $O(V + E)$ | 실제로 존재하는 간선만 기록하므로 정점과 간선을 한 번씩만 본다 |

## 최단 경로: Dijkstra

Dijkstra 알고리즘은 그래프의 한 노드에서 출발해 다른 모든 노드까지의 최단 경로를 구한다. "가장 적은 비용으로 목적지까지 가는 길"을 계산하는 방법이다. 간선의 가중치가 음수가 아니어야 한다.

1. 시작 노드에서 각 노드까지의 최소 비용을 기록할 배열을 만든다. 시작 노드는 0, 나머지는 무한대로 둔다.
2. 아직 방문하지 않은 노드 가운데 지금까지의 비용이 가장 작은 노드를 고른다.
3. 그 노드를 거쳐 이웃 노드로 가는 비용을 계산하고, 기존 값보다 작으면 갱신한다.
4. 모든 노드를 처리할 때까지 2와 3을 반복한다.

```c
void dijkstra(int graph[V][V], int src) {
    int dist[V];          // 최단 거리
    int visited[V] = {0}; // 방문 여부

    for (int i = 0; i < V; i++) dist[i] = INT_MAX;
    dist[src] = 0;

    for (int count = 0; count < V - 1; count++) {
        int u = findMinDistance(dist, visited); // 방문하지 않은 노드 중 거리가 최소인 노드
        visited[u] = 1;

        for (int v = 0; v < V; v++) {
            if (!visited[v] && graph[u][v] && dist[u] != INT_MAX
                && dist[u] + graph[u][v] < dist[v]) {
                dist[v] = dist[u] + graph[u][v];
            }
        }
    }
}
```

그래프는 인접 행렬로 주고, 0은 연결되지 않았음을 뜻한다. 바깥 반복에서 거리가 최소인 노드 `u`를 골라 방문 처리하고, 안쪽 반복에서 모든 노드 `v`를 본다. `v`가 `u`와 연결되어 있고, 아직 방문하지 않았으며, `u`를 거쳐 가는 거리가 기존 거리보다 짧으면 `v`의 거리를 갱신한다.

이 구현의 시간 복잡도는 $O(V^2)$이다. 노드를 고르는 데 $O(V)$, 이웃을 보는 데 $O(V)$가 들고 이것을 $V$번 반복하기 때문이다. 인접 리스트와 우선순위 큐를 쓰면 $O((V + E) \log V)$로 줄일 수 있다.

## Greedy와 backtracking

**Greedy.** 매 순간 그 상황에서 가장 좋아 보이는 선택을 반복해 전체의 최적해에 이르려는 방법이다. 항상 최적해를 보장하지는 않는다.

**Backtracking.** 해를 찾아 나가다가 막히면 이전 단계로 돌아가 다른 경로를 탐색하는 방법이다. 가능한 경우를 체계적으로 탐색하되, 해가 될 수 없거나 최적해가 나올 가능성이 없는 경로는 가지치기(pruning)로 잘라 낸다.

### Greedy를 쓰는 문제

**Bin packing.** 크기가 정해진 물체들을 통에 어떻게 채울 것인가의 문제다. First Fit, Next Fit, Best Fit, Worst Fit 같은 방법이 있다. First Fit은 물체를 하나씩 보면서, 첫 번째 통부터 차례로 살펴 처음으로 들어갈 공간이 남아 있는 통에 넣는다.

**Job scheduling.** 수행 시간이 정해진 작업 $n$개를 기계 $m$대에 배정해, 모든 작업이 가장 빨리 끝나도록 하는 문제다. 새 작업을 배정할 때 지금까지 배정된 작업이 가장 빨리 끝나는 기계에 넣는다.

두 문제 모두 greedy한 방법이 최적해를 보장하지는 않는다. 빠르게 쓸 만한 해를 구하는 근사 알고리즘으로 쓰인다.

### TSP

TSP(Traveling Salesman Problem)처럼 현실적인 시간 안에 최적해를 찾기 어려운 문제에는 근사 알고리즘을 쓴다. 최적해를 포기하는 대신, 최적해에 가까운 해를 다항 시간 안에 구하도록 설계한 알고리즘이다.

- **MST를 이용한 근사.** MST(Minimum Spanning Tree)는 모든 정점을 사이클 없이 연결하는 트리 가운데 간선 가중치의 합이 최소인 트리다. Kruskal이나 Prim 알고리즘으로 MST를 구한 뒤, 트리를 따라 정점을 방문하면서 이미 방문한 정점은 건너뛰어 순회 경로를 만든다.
- **Backtracking으로 정확한 해 찾기.** 노드를 차례로 방문하며 경로를 만들어 가다가, 지금까지의 가중치 합이 이전에 구한 가장 좋은 해보다 커지는 순간 그 경로는 더 가지 않고 되돌아온다.

## 참고 자료

노트에 출처를 따로 적어 두지 않았다. 다룬 내용은 알고리즘 교재의 표준적인 내용이다.
