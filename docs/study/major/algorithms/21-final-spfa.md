---
title: "SPFA Algorithm"
date: 2022-12-04
last_modified_at: 2022-12-05
category: major
tags: [알고리즘, SPFA, 최단 경로]
---

# SPFA Algorithm

작성일 2022-12-04 · 최종 수정일 2022-12-05
{ .post-date }

- Shortest Path Faster Algorithm : 벨만-포드 알고리즘과 유사하게 `음수 가중치` 가 있을때 사용 가능한 `단일 시작점` 알고리즘이다. 벨만-포드 알고리즘은 총 E개의 간선을 V번 방문하기 때문에, $O(VE)$의 시간 복잡도를 갖는다.

    하지만, SPFA는 경로의 길이가 갱신된 정점에 `인접한 간선`에 대해서만 다음 방문에서 체크를 하기 때문에, `같은 간선`을 `V`번 방문할 필요가 없어서 평균 시간 복잡도가 `O(E)`이다.

    단, 경로의 길이가 갱신되지 않았다면 방문을 계속해야하기 때문에 최악의 경우에는 $O(VE)$의 시간 복잡도를 갖는다.

- SPFA는 정점들을 큐에 저장하면서, 각 정점을 V번 방문하는 것이 아니라, 더 이상 방문할 정점이 없어지는 시점. 즉, 큐가 비워질 때 함수를 종료하면 된다.

[https://lemidia.github.io/algorithm/SPFA/](https://lemidia.github.io/algorithm/SPFA/)
