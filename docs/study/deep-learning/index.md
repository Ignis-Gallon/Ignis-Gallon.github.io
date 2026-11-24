---
title: "딥러닝"
---

# 딥러닝

2023년 3월부터 2024년 5월 사이에 딥러닝을 공부하면서 강의와 책, 블로그 글을 보고 정리한 노트입니다. 신경망의 기본 개념에서 시작해 오토인코더와 LSTM, 시계열 예측과 이상 탐지로 이어집니다. 여러 곳에 흩어져 있던 기록을 주제에 따라 네 묶음으로 나누었습니다.

**기초 개념**

2023년 7월에 유튜브 딥러닝 강의를 들으며 정리한 노트입니다.

- [Tensor](01-tensor.md): 스칼라, 벡터, 행렬을 rank 0 ~ 2 텐서로 보고 N차원까지 확장하기, 문장을 원-핫 벡터로 바꿔 미니배치로 넣는 예
- [Linearity, Non-linearity](02-linearity-non-linearity.md): 선형 모델과 비선형 모델의 차이, 선형 회귀와 로지스틱 회귀
- [Node](03-node.md): 가중치 곱, 편향 덧셈, 활성화 함수로 이루어진 노드의 연산과 순전파 코드
- [Backpropagation](04-backpropagation.md): 노드 하나를 직접 만들어 AND, OR 연산을 학습시키는 코드
- [Dense Layer](05-dense-layer.md): 노드 하나가 의사 결정선 하나를 그린다는 관점에서 보는 XOR 문제, 입력 차원 지정
- [Multi-Layer Perceptron](06-multi-layer-perceptron.md): Dense Layer를 쌓아 손글씨 숫자를 분류하는 실습, Softmax를 쓰는 이유
- [Dropout, Early stopping](07-dropout-early-stopping.md): 과적합을 줄이는 두 기법
- [Autoencoder](08-autoencoder.md): Encoder, Latent Vector, Decoder의 구조와 손글씨 숫자 데이터로 만드는 모델
- [CNN(Convolutional Neural Network)](09-cnn.md): 다층 퍼셉트론의 한계, 커널과 필터, 풀링, 제로 패딩
- [RNN(Recurrent Neural Network)](10-rnn.md): 입력의 순서를 고려하는 구조, 셀이 상태값을 계산하는 과정
- [LSTM](11-lstm.md): RNN의 장기 의존성 문제, cell state와 gate, 기울기 소실과 폭주
- [batch size & epochs](12-batch-size-epochs.md): 배치 크기와 에폭의 의미, 크기에 따른 차이
- [NumPy > ndarray](13-numpy-ndarray.md): ndarray의 특징, ndim과 shape, reshape

**신경망 이론**

2023년 3월에 책 "신경망 첫걸음"을 읽고 쓴 노트 한 편과, 2023년 6 ~ 7월에 딥러닝 강의를 들으며 정리한 노트입니다.

- [신경망 첫걸음 > 신경망 이론](14-neural-network-theory.md): 예측자와 분류자, 학습률, 행렬곱, 오차의 역전파, 경사하강법, 오차함수
- [Anomaly Detection for Time Series Data With Autoencoder](15-anomaly-detection-time-series-autoencoder.md): 시계열 이상 탐지의 어려움과 LSTM 오토인코더를 다룬 세미나 정리
- [Ch1. Introduction](16-ch1-introduction.md): 인공지능, 머신러닝, 딥러닝의 관계와 학습의 종류
- [Ch2-1. Introduction to Supervised Learning](17-ch2-1-supervised-learning.md): 선형 회귀, 로지스틱 회귀, 비용 함수와 경사 하강법, 평가 지표
- [Ch2-2. Neural Network](18-ch2-2-neural-network.md): 역전파, 기울기 소실, ReLU, 과적합과 규제, 드롭아웃, 전이 학습
- [Ch2-3. CNN(Convolutional Neural Network)](19-ch2-3-cnn.md): 필터, 스트라이드, 패딩, 풀링, 완전 연결 층, TensorFlow 구현 흐름
- [Ch3-1. 비지도학습 기초 A](20-ch3-1-unsupervised-basics-a.md): 조건부 확률, 베이즈 정리, 확률 분포, Cross-entropy와 KLD, 기댓값
- [Ch.3-1 비지도학습 기초 B](21-ch3-1-unsupervised-basics-b.md): 최대우도 관점에서 본 손실 함수, Negative log-likelihood, 생성 모델과 매니폴드
- [Ch.3-2 Autoencoder & Anomaly Detection](22-ch3-2-autoencoder-anomaly-detection.md): 오토인코더의 원리와 손실 함수, 이상 탐지에의 활용, 혼동 행렬
- [Ch.3-2 Autoencoder & Anomaly Detection Examples](23-ch3-2-examples.md): 오토인코더 이상 탐지 실습을 준비하며 남긴 짧은 메모
- [Autoencoder & Anomaly Detection](24-autoencoder-anomaly-detection.md): 오토인코더의 압축과 복원 구조, 용어, 손실 함수

**시계열과 이상 탐지**

2023년 7월에 오토인코더와 LSTM으로 이상을 탐지하는 방법을 공부한 노트와, 2024년 3 ~ 5월에 시계열 예측과 이상 탐지를 다시 공부한 노트입니다.

- [About Anomaly Detection](25-about-anomaly-detection.md): 이상 탐지의 용어, 지도·준지도·비지도 방식의 장단점
- [About Anomaly Detection using AE](26-anomaly-detection-using-ae.md): 복원 오차를 이용한 이상 탐지, 비선형 차원 축소, 매니폴드 가설
- [LSTM Autoencoder Example](27-lstm-autoencoder-example.md): 공개 진동 센서 데이터셋으로 이상 탐지를 구현하는 튜토리얼을 따라 하며 코드를 분석한 기록
- [Concepts of LSTM > 2)](28-concepts-of-lstm-2.md): 희귀 이벤트 분류 튜토리얼의 데이터 준비, Curve Shifting, Pandas의 iloc과 loc
- [LSTM TensorFlow](29-lstm-tensorflow.md): hidden state와 cell state, return_sequences와 return_state
- [시계열 분석](30-time-series-analysis.md): 시계열 데이터의 종류, ARIMA와 Prophet, RNN과 LSTM, 다변량 예측
- [Time Series Forecasting With Deep Learning: A Survey + Stationarity(정상성)](31-tsf-deep-learning-survey-stationarity.md): 딥러닝 시계열 예측 서베이 논문 리뷰와 정상성 개념 정리
- [딥러닝 시계열 예측 모델 관련 소개](32-dl-time-series-forecasting-models.md): ARIMA에서 Transformer, DLinear, TimesNet까지의 흐름
- [LSTM Time Series Forecasting](33-lstm-time-series-forecasting.md): 공개 기후 데이터로 LSTM, 1D CNN, GRU 예측 모델을 만드는 실습, 배치 정규화
- [Anomaly Detection - Overview](34-anomaly-detection-overview.md): Outlier, Novelty, Anomaly의 차이, 분류와 이상 탐지의 차이
- [이상 탐지(Anomaly Detection) 정리](35-anomaly-detection-summary.md): 통계적 기법, 이상의 세 유형, 시계열 이상 탐지 기법
- [Anomaly Detection 관련 공부 및 조사](36-anomaly-detection-study.md): 시계열 예측과 예측 기반 이상 탐지를 공부하며 모은 강의, 글, 논문 목록

**환경 설정**

- [실습을 위한 Tensorflow와 Jupyter Notebook, Colab 설치](37-tensorflow-jupyter-colab-setup.md): Anaconda 가상환경에 TensorFlow 설치하기, Jupyter Notebook, Colab
- [PyTorch 실습을 위한 Anaconda 설정](38-pytorch-anaconda-setup.md): conda 명령어와 PyTorch 설치
- [WSL2 Tensorflow PyTorch CUDA](39-wsl2-tensorflow-pytorch-cuda.md): 그래픽카드에 맞는 CUDA, 드라이버, TensorFlow 버전 확인과 설치 순서
