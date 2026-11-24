---
title: "인공지능"
---

# 인공지능

3학년 2학기에 수강한 인공지능 수업의 노트입니다. Agent와 탐색, 지식표현과 추론 같은 전통적인 인공지능 주제에서 시작해 신경망(예측자와 분류자, 순전파와 오차의 역전파, Gradient Vanishing)과 머신러닝(지도학습, 비지도학습)까지 다룹니다.

- [Agent](01-agent.md): Agent의 개념과 주요 응용 분야, Intelligent Agent와 성과 측정, 합리적인 에이전트, PEAS와 환경의 유형
- [탐색](02-search.md): 직접적 기법과 인공지능적 해법의 차이, 상태 공간과 문제 정형화, Romania 지도와 8-puzzle 예제, DFS와 BFS
- [탐색에서의 Heuristic(Uniform-Cost Search)](03-heuristic-uniform-cost-search.md): 균일비용탐색의 경로 선택 과정, 전향·후향·양방향 탐색, 언덕등반기법과 그 실패 사례
- [다양한 탐색 기법](04-search-techniques.md): Greedy Best-First Search와 A, A\* 알고리즘을 Romania 문제에 적용해 비교, 게임트리의 최소최대 탐색법, 제약조건만족 문제
- [지식표현과 추론](05-knowledge-representation-and-reasoning.md): 데이터에서 지혜까지의 단계, 규칙과 프레임을 이용한 지식 표현, 명제 논리와 술어 논리의 구문과 추론, 논리 프로그래밍 언어
- [인공지능의 역사](06-history-of-ai.md): 인간처럼 또는 합리적으로 생각하고 행동한다는 네 가지 관점, 지능의 의미, 인공지능의 시작과 발전 과정
- [신경망과 예측자/분류자](07-neural-network-predictor-classifier.md): km를 mile로 바꾸는 예제로 본 오차와 상수 조정, 직선 분류자의 기울기 갱신과 학습률, 선형 분류자의 한계, 뉴런의 동작 원리
- [순전파와 오차의 역전파](08-forward-and-backpropagation.md): 다층 퍼셉트론의 층별 출력 계산과 행렬곱 표현, 오차 역전파의 행렬 표현, 경사하강법, 오차함수의 편미분
- [인공지능 - 신경망](09-neural-network.md): 심층 신경망의 Gradient Vanishing 문제와 Sigmoid의 한계, 활성화 함수 교체, 가중치 초기화 방법 교체
- [머신러닝이란?](10-what-is-machine-learning.md): 지도학습, 비지도학습, 강화학습, 준지도학습의 개념, 데이터가 잘못되었거나 부족할 때 생기는 문제점
- [머신러닝 : 지도학습(회귀모델)](11-supervised-learning-regression.md): 가설과 오차, Label과 Feature, 회귀와 분류의 차이, 단순 오차·MAE·MSE 비교, 최소 제곱법
- [머신러닝 : 지도학습/비지도학습](12-supervised-and-unsupervised-learning.md): k-NN과 거리 척도, 앙상블, Clustering, 다항 회귀와 과적합·과소적합, 결정 트리의 분할 기준(Entropy, ID3, CART), SVM의 마진

같은 수업에서 한 과제와 프로젝트는 프로젝트 탭의 [인공지능 수업 프로젝트](../../../projects/ai-course/index.md)에 있습니다.
