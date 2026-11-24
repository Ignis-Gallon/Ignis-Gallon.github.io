---
title: "임베디드시스템특강"
---

# 임베디드시스템특강

3학년 2학기에 수강한 과목의 노트입니다. 과목 이름과 달리 머신러닝과 딥러닝을 다룬 과목으로, 선형대수와 NumPy 기초에서 시작해 Logistic Regression의 Forward·Backward Pass, 다층 신경망, Regularization과 Optimization 같은 학습 기법, CNN, Tensorflow/Keras 실습까지 정리했습니다.

- [History of ML & DL](01-history-of-ml-dl.md): Rule 기반 방식과 학습 기반 방식의 차이, Perceptron에서 Back Propagation, CNN, GPU 활용까지의 흐름
- [Math for ML](02-math-for-ml.md): Scalar·Vector·Matrix, Vector Space와 Basis, Linear Mapping, Basis Change, Norm, Determinant, 고윳값, Rank
- [NumPy for ML](03-numpy-for-ml.md): Tensor 생성과 Reshaping, Indexing과 Slicing, 배열 연산, 집계 함수, Broadcasting
- [Forward Pass](04-forward-pass.md): Logistic Regression을 이용한 이진 분류, Sigmoid 함수, Loss Function과 Cost Function, Gradient Descent
- [3주차 수업 중 정리: Loss Function, Gradient Descent, Back Propagation](05-week3-loss-gradient-descent-backprop.md): Squared Error와 Cross Entropy, Cost Function, Gradient Descent, Computation Graph와 Back Propagation
- [Backward Pass & Vectorizing](06-backward-pass-vectorizing.md): Computation Graph로 보는 Back Propagation, Logistic Regression의 의사 코드와 벡터화 구현
- [Multi Layer Network](07-multi-layer-network.md): Hidden Layer의 파라미터와 행렬곱, m개 예제에 대한 Vectorization, 활성화 함수, 역전파, Random Initialization
- [Multi Hidden Layer Network](08-multi-hidden-layer-network.md): 층을 깊게 쌓는 이유, Deep Neural Network 표기법, Forward와 Backward Pass의 모듈화, Shape 일반화
- [Regularization](09-regularization.md): 머신러닝 개발 흐름, Train/Val/Test 분리와 Cross-Validation, L2 Regularization, Dropout, Data Augmentation, Early Stopping
- [Weight Initialization](10-weight-initialization.md): 입력 Normalize, 초깃값 크기에 따른 Vanishing/Exploding Gradient, Xavier와 He 초기화
- [Batch Normalization](11-batch-normalization.md): 학습 시의 Forward와 Backward 계산, γ와 β의 역할, 테스트 시의 동작과 Exponentially Weighted Moving Average
- [Optimization](12-optimization.md): Mini-batch가 필요한 이유, Batch·Stochastic 방식과의 차이, Momentum, RMSprop, Adam, Learning Rate Decay
- [Hyperparameter Tuning](13-hyperparameter-tuning.md): Learning Rate 탐색 예시, Grid Search와 Random Search, 적절한 Scale, 모델을 돌리는 두 가지 전략
- [Multiclass Classification](14-multiclass-classification.md): 출력을 확률로 바꾸는 Softmax, Cross-Entropy Loss, One Hot Encoding
- [Convolutional Network](15-convolutional-network.md): Filter, Padding, Stride, RGB 이미지의 Convolution, 파라미터 수 계산, Max Pooling, 1x1 Convolution, Keras로 만든 LeNet5
- [Tensorflow/Keras Laboratory](16-tensorflow-keras-laboratory.md): tf.constant, tf.Variable, tf.matmul, tf.GradientTape 사용법, Keras로 MNIST 모델을 만들어 학습과 예측까지

딥러닝을 따로 공부하며 정리한 노트는 [딥러닝 스터디](../../deep-learning/index.md)에 있습니다.
