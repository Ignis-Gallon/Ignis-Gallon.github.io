---
title: "선형대수 복습: determinant, eigenvalue, 행렬 분해, PCA"
date: 2024-12-01
last_modified_at: 2024-12-01
category: study
tags: [선형대수, Determinant, Eigenvalue, SVD, PCA, Eigenface]
---

# 선형대수 복습: determinant, eigenvalue, 행렬 분해, PCA

작성일 2024-12-01
{ .post-date }

머신러닝에서 자주 쓰는 선형대수 개념을 PCA까지 이어지도록 짧게 복습한 노트다. 분산과 공분산에서 시작해 determinant, eigenvalue, 행렬 분해(EVD, SVD)를 거쳐 PCA에 이르고, 마지막에 학부 때 했던 얼굴 인식 프로젝트를 PCA의 예로 다시 정리했다. 선형대수의 기초(연립방정식, 벡터 공간, 선형 사상)는 나중에 [선형대수 (MML 2장)](../linear-algebra/index.md) 시리즈로 따로 공부했다.

## 기본 용어

**분산(variance).** 각 값에서 평균을 뺀 것을 제곱해 모두 더하고, 데이터 개수에서 1을 뺀 값으로 나눈다.

$$
\mathrm{Var}(a) = \frac{1}{M-1} \sum_{i=1}^{M} (a_i - \bar{a})^2
$$

**공분산(covariance).** 길이가 같은 두 벡터에 대해, 각각 평균을 뺀 값을 서로 곱해 더하고 $M-1$로 나눈다.

$$
\mathrm{Cov}(a, b) = \frac{1}{M-1} \sum_{i=1}^{M} (a_i - \bar{a})(b_i - \bar{b})
$$

Covariance matrix에서 대각 성분 $C_{a_1 a_1}, C_{a_2 a_2}$는 같은 변수끼리 구한 값이므로 auto-covariance, 곧 분산이다. 두 index가 같기 때문이다. 대각이 아닌 성분 $C_{a_1 a_2}, C_{a_2 a_1}$은 서로 다른 변수 사이에서 구한 cross-covariance다.

**상관계수(correlation).** 두 데이터가 얼마나 관련되어 있는지를 본다. 공분산을 두 벡터의 표준편차의 곱으로 나눈 값이고, 최대 1, 최소 $-1$이다.

$$
\rho_{ab} = \frac{\mathrm{Cov}(a, b)}{\sigma_a \sigma_b}
$$

**Orthogonal matrix.** 전치행렬과 곱하면 identity matrix가 되는 행렬이다.

$$
Q^{\top} Q = I
$$

노트에는 "orthonormal matrix는 orthogonal matrix이면서 열벡터의 길이가 모두 1인 것"이라고 따로 적어 두었는데, 이 구분은 정확하지 않다. $Q^{\top}Q = I$라는 조건에 이미 열벡터들이 서로 직교한다는 것(대각이 아닌 성분이 0)과 길이가 1이라는 것(대각 성분이 1)이 모두 들어 있다. Orthogonal과 orthonormal을 구분하는 것은 행렬이 아니라 벡터의 집합에 대해서다. 서로 직교하기만 하면 orthogonal, 거기에 길이까지 1이면 orthonormal이다. Orthogonal matrix는 열벡터가 orthonormal한 행렬이다.

## Determinant

정사각 행렬을 scalar 값 하나로 바꾸는 연산이다. 가우스 소거법 등으로 구할 수 있다. 세 가지 의미로 정리했다.

- **부피의 변화.** 행렬이 나타내는 선형 변환이 대상의 부피를 몇 배로 scaling하는지를 나타낸다.
- **가역성.** Determinant가 0이면 역행렬이 존재하지 않고(비가역), 0이 아니면 역행렬이 존재한다.
- **선형 독립.** Determinant가 0이면 행렬의 행벡터들(또는 열벡터들)이 선형 종속이고, 0이 아니면 선형 독립이다.

선형 종속은 적어도 하나의 벡터가 다른 벡터들의 선형 결합으로 표현되는 경우이고, 선형 독립은 어느 벡터도 다른 벡터들의 조합으로 표현되지 않는 경우다.

**Rank.** 행렬에서 선형 독립인 행(또는 열)의 최대 개수다. 열벡터 가운데 선형 독립인 것의 최대 개수와 행벡터 가운데 선형 독립인 것의 최대 개수는 같다.

## Eigenvalue와 eigenvector

행렬 $A$에 벡터 $x$를 곱한 결과가 $x$의 scalar 배가 될 때, 그 scalar $\lambda$를 eigenvalue(고윳값), 그때의 영벡터가 아닌 $x$를 eigenvector(고유벡터)라고 한다.

$$
A x = \lambda x, \qquad x \neq 0
$$

구하는 방법은 다음과 같다. 위 식을 $(A - \lambda I)x = 0$으로 쓰면, 영벡터가 아닌 해 $x$가 존재하려면 $A - \lambda I$가 비가역이어야 한다. 따라서

$$
\det(A - \lambda I) = 0
$$

을 만족하는 $\lambda$를 구하고, 각 $\lambda$에 대해 $(A - \lambda I)x = 0$을 풀어 eigenvector를 얻는다.

Eigenvector는 행렬의 특성을 이해하고 데이터에서 주요한 방향을 뽑아낼 때 쓰인다. PCA와 SVD가 대표적인 예다.

## 행렬 분해

Decomposition은 복잡한 행렬을 다루기 쉬운 행렬 여러 개의 곱으로 나누는 기법이다. 행렬의 구조적인 성질을 이해하거나 계산을 효율적으로 하는 데 쓴다.

| 분해 | 내용 |
|---|---|
| LU | 하삼각행렬과 상삼각행렬로 분해 |
| QR | Orthogonal matrix와 상삼각행렬로 분해 |
| EVD | Eigenvector와 eigenvalue로 분해 |
| SVD | 세 개의 행렬로 분해. 직사각 행렬에도 적용 가능 |

### EVD (Eigenvalue Decomposition)

행렬을 eigenvalue와 eigenvector로 분해한다. 정사각 행렬에만 쓸 수 있고, 선형 독립인 eigenvector가 행렬의 크기만큼 있어야 한다.

$$
A = P D P^{-1}
$$

$P$는 eigenvector를 열로 모은 행렬이고 $D$는 eigenvalue를 대각에 놓은 행렬이다.

### SVD (Singular Value Decomposition)

정사각이 아닌 직사각 행렬에도 적용할 수 있어 EVD보다 범용적이다. 행렬과 그 전치행렬을 곱해 만든 새 행렬의 eigenvector와 eigenvalue로부터 분해를 구한다.

$$
A = U \Sigma V^{\top}
$$

- $U$: $A A^{\top}$의 eigenvector로 이루어진 orthogonal matrix
- $\Sigma$: $A^{\top} A$의 eigenvalue의 제곱근(singular value)을 대각에 놓은 행렬. $A A^{\top}$와 $A^{\top} A$는 0이 아닌 eigenvalue가 같으므로 어느 쪽에서 구해도 된다
- $V$: $A^{\top} A$의 eigenvector로 이루어진 orthogonal matrix

노트에는 $U$와 $V$의 설명이 서로 바뀌어 적혀 있었다($U$를 $A^{\top}A$에서, $V$를 $AA^{\top}$에서 구한다고 적음). $A = U\Sigma V^{\top}$를 대입해 보면 $AA^{\top} = U \Sigma \Sigma^{\top} U^{\top}$, $A^{\top}A = V \Sigma^{\top} \Sigma V^{\top}$이므로 위와 같이 짝지어야 맞다.

구하는 순서는 다음과 같다.

1. $F = A A^{\top}$를 구한다.
2. $F$의 eigenvalue와 eigenvector를 구한다.
3. 구한 eigenvector를 길이 1로 정규화해 열로 모은다. 이것이 $U$다.
4. $A^{\top} A$에 대해서도 같은 방법으로 eigenvector를 구하고 정규화해 $V$를 만든다.
5. Eigenvalue의 제곱근을 큰 순서대로 대각에 놓아 $\Sigma$를 만든다.

2번 단계를 강의 자료의 예로 따라가 보면 이렇다.

$$
F = \begin{bmatrix} 11 & 1 \\ 1 & 11 \end{bmatrix}, \qquad
F x = \lambda x
\ \Rightarrow\
\begin{cases}
(11 - \lambda)\, x_1 + x_2 = 0 \\
x_1 + (11 - \lambda)\, x_2 = 0
\end{cases}
$$

영벡터가 아닌 해(non-trivial solution)가 있으려면

$$
\det(F - \lambda I) = \begin{vmatrix} 11 - \lambda & 1 \\ 1 & 11 - \lambda \end{vmatrix} = (11 - \lambda)^2 - 1 = 0
$$

이어야 하므로 $\lambda = 12,\ 10$이다. $\lambda = 12$를 넣으면 $x_1 = x_2$이고, $\lambda = 10$을 넣으면 $x_1 = -x_2$다. 정규화한 eigenvector는 각각 $\frac{1}{\sqrt{2}}(1, 1)^{\top}$, $\frac{1}{\sqrt{2}}(1, -1)^{\top}$이고, singular value는 $\sqrt{12}$와 $\sqrt{10}$이다.

SVD는 PCA, 이미지 복원, 이미지 축소(압축) 등에 쓰인다.

## PCA

PCA(Principal Component Analysis, 주성분 분석)는 고차원 데이터가 있을 때 데이터의 분산을 기준으로 분포의 주성분을 찾는 방법이다. 데이터가 가장 넓게 퍼져 있는 방향을 찾아 그 방향을 새 좌표축으로 삼는다.

1. 차원(변수)마다 평균을 구해 모든 데이터에서 평균을 뺀다.
2. Covariance matrix를 구한다.
3. Covariance matrix의 eigenvalue와 eigenvector를 구한다. Eigenvalue의 비율로 각 주성분의 explained variance ratio를 구할 수 있다.
4. Eigenvalue가 큰 eigenvector부터 주축으로 골라, 데이터를 그 축들로 이루어진 좌표계로 옮긴다. 고른 축의 수가 원래 차원보다 적으면 차원이 축소된다.
5. 복원할 때는 축소된 좌표에 eigenvector를 다시 곱하고, 1번에서 뺐던 평균을 더한다.

## 예: 얼굴 인식 프로젝트

학부 때 PCA로 얼굴을 인식하는 프로젝트를 한 적이 있다. 이른바 eigenface 방법이고, 위의 절차가 그대로 쓰인다.

**학습.**

1. 얼굴 이미지를 1차원 벡터로 펼친다(flatten). 이미지 한 장이 10304차원의 벡터가 되고, 25장을 열로 모으면 $10304 \times 25$ 행렬이 된다.
2. 인식률을 높이기 위해 각 이미지를 평균 128, 표준편차 128이 되도록 표준화한다.
3. 차원(픽셀)마다 평균을 구해 빼서 데이터를 원점 중심으로 옮긴다. 이 평균이 평균 얼굴이다.
4. Covariance matrix를 구한다. 데이터 행렬을 $X$($10304 \times 25$)라고 할 때 $X^{\top} X$를 쓰면 크기가 $25 \times 25$다.
5. 이 행렬의 eigenvalue와 eigenvector를 구하고, eigenvector를 정규화한다. Eigenvector는 모두 25개다.
6. 정규화한 각 이미지를 eigenvector들에 투영해 가중치를 계산한다. 한 사람(이미지)마다 가중치 25개를 갖게 된다.

**인식.**

1. 새 이미지가 들어오면, 학습 때 구해 둔 평균으로 같은 방식으로 정규화한다.
2. 학습 때 구한 eigenvector들에 투영해 가중치를 구한다.
3. 저장해 둔 가중치들과 비교해 가장 가까운 것을 그 얼굴로 판단한다.

4번 단계에 설명을 보탠다. 픽셀 사이의 covariance matrix를 정의대로 구하면 $X X^{\top}$의 꼴이고 크기가 $10304 \times 10304$여서 eigenvector를 구하기에 너무 크다. 대신 $25 \times 25$인 $X^{\top} X$의 eigenvector $v$를 구하면

$$
X^{\top} X v = \lambda v \ \Rightarrow\ X X^{\top} (X v) = \lambda (X v)
$$

이므로 $Xv$가 $X X^{\top}$의 eigenvector가 된다. 작은 행렬에서 구한 $v$에 $X$를 곱하고 길이를 1로 맞추면 원래 공간(10304차원)의 eigenvector, 곧 eigenface를 얻는다. 이미지가 25장뿐이므로 0이 아닌 eigenvalue도 많아야 25개이고, 작은 행렬로 계산해도 잃는 것이 없다.

## 참고 자료

노트에 출처를 따로 적어 두지 않았다. SVD 계산 예는 강의 자료를 캡처해 둔 것에서 수식만 옮겼다. 얼굴 인식 방법의 원 논문은 다음과 같다.

- M. Turk, A. Pentland, "Eigenfaces for Recognition", Journal of Cognitive Neuroscience 3(1), 1991.
