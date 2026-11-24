---
title: "AI 기초 복습"
---

# AI 기초 복습

2024년 11월, 시계열과 설명가능 인공지능을 연구하는 연구실의 인턴 면접을 준비하면서 기초를 다시 훑어본 복습 노트입니다. 딥러닝의 기본 구조에서 시작해 시계열 모델, 학습 기법, 선형대수, 알고리즘까지 노트를 쓴 순서대로 아홉 편으로 나누었습니다. 대부분 논문 리뷰 영상이나 공개 세미나를 보며 정리한 것이고, 공부하면서 스스로 던진 질문과 찾아본 답도 함께 남겼습니다. 출처는 각 글의 끝에 적었습니다.

- [1. ResNet: 깊은 네트워크를 학습시키는 residual learning](01-resnet.md): degradation problem, residual block과 identity mapping, shortcut의 선택지, bottleneck 구조
- [2. LSTM: cell state와 gate로 긴 시퀀스를 학습하기](02-lstm.md): RNN과 BPTT의 한계, cell state가 gradient를 지키는 원리, gate, forget gate가 추가된 이유, ResNet과의 연결
- [3. Autoencoder: manifold learning, 규제, 응용](03-autoencoder.md): manifold learning, bias-variance tradeoff, sparse, denoising, contractive autoencoder, 네 가지 응용
- [4. LSTM Autoencoder로 시계열 이상 탐지하기: EncDec-AD](04-lstm-autoencoder-anomaly-detection.md): 재구성 기반 이상 탐지, teacher forcing, anomaly score, 데이터 분할과 $F_\beta$ score
- [5. 시계열 foundation 모델 개요](05-time-series-foundation-models.md): foundation 모델과 transfer learning, fine-tuning의 관계, LLM을 활용하는 세 가지 방법과 시계열 전용 모델, Time-LLM
- [6. 시계열의 비정상성과 주기성 다루기: 분해, Non-stationary Transformer, TimesNet](06-nonstationarity-and-periodicity.md): 이동평균 분해, RevIN과 over-stationarization, De-stationary Attention, FFT로 주기를 찾아 2D로 바꾸는 TimesNet
- [7. 학습 기법 복습: regularization, optimizer, weight initialization](07-training-techniques.md): L1과 L2, Batch Normalization, Momentum, RMSprop, Adam, Xavier와 He 초기화
- [8. 선형대수 복습: determinant, eigenvalue, 행렬 분해, PCA](08-linear-algebra-and-pca.md): 공분산, determinant와 rank, eigenvalue, EVD와 SVD, PCA와 얼굴 인식 예
- [9. 알고리즘 복습: 정렬, 그래프 탐색, 최단 경로, greedy와 backtracking](09-algorithm-review.md): merge sort와 quick sort, DFS와 BFS, Dijkstra, greedy와 backtracking

같은 시기에 정리한 노트 가운데 다른 곳에 실은 것도 있습니다.

- Seq2Seq와 attention, Transformer: [Attention Is All You Need 리뷰](../../papers/2025-01-06-attention-is-all-you-need.md)
- PatchTST와 다변량 시계열을 다루는 전략: [PatchTST 리뷰](../../papers/2024-11-25-patchtst.md)
- 설명가능 인공지능: [XAI 개요](../xai/01-xai-overview.md)
- 강화학습: [강화학습 시리즈](../reinforcement-learning/index.md)
