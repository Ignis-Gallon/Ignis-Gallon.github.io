---
title: "MML 2.1 ~ 2.3: 연립선형방정식과 행렬"
date: 2025-06-12
last_modified_at: 2025-06-12
category: study
tags: [선형대수, 연립선형방정식, 행렬, 가우스 소거법, MML]
---

# MML 2.1 ~ 2.3: 연립선형방정식과 행렬

작성일 2025-06-12
{ .post-date }

"Mathematics for Machine Learning"(이하 MML) 2장 Linear Algebra의 앞부분인 2.1 Systems of Linear Equations, 2.2 Matrices, 2.3 Solving Systems of Linear Equations를 읽고 정리한 글이다. 연립선형방정식이 무엇이고 해가 어떤 모양으로 나오는지, 그리고 그 해를 체계적으로 구하는 방법(특수해와 일반해, Gaussian Elimination)을 예제를 직접 따라 풀며 정리했다.

## 들어가며: 벡터와 닫힘

- 대수학은 복잡한 개념을 직관적으로 다루기 위해 기호와, 그 기호를 다루는 규칙을 만드는 학문이다.
- 선형대수학(Linear Algebra)은 vector와, vector를 조작하는 규칙을 다룬다.

vector의 핵심 성질은 두 가지다. 두 vector $x, y$는 더할 수 있고 그 합 $x + y = z$도 vector다. 그리고 vector에 scalar를 곱한 $\lambda x \ (\lambda \in \mathbb{R})$도 vector다.

$$
a = \begin{bmatrix} 1 \\ 2 \\ 3 \end{bmatrix} \in \mathbb{R}^3
$$

$n$차원 실수 vector는 위와 같이 쓰며, 덧셈과 scalar 곱은 모두 성분별(element-wise)로 계산한다.

여기서 닫힘(closure)이라는 개념이 나온다. 어떤 연산을 반복해서 적용했을 때 나올 수 있는 결과를 모두 포함하는 집합, 그중 가장 작은 것을 그 연산에 대한 closure라고 한다. 어떤 연산을 기준으로 하느냐에 따라 closure는 달라진다. vector의 경우 덧셈과 scalar 곱에 대해 닫혀 있는 집합이 vector space이며, 이는 [다음 글](02-vector-spaces-and-basis.md)에서 정식으로 다룬다.

## 2.1 연립선형방정식

연립선형방정식(system of linear equations)은 선형대수학의 중심에 있고, 많은 문제가 이 형태로 표현된다. 가장 일반적인 모양은 다음과 같다.

$$
\begin{aligned}
a_{11}x_{1} + \cdots + a_{1n}x_{n} &= b_{1} \\
&\ \ \vdots \\
a_{m1}x_{1} + \cdots + a_{mn}x_{n} &= b_{m}
\end{aligned}
$$

이 식을 모두 만족하는 $n$-tuple $(x_1, \dots, x_n) \in \mathbb{R}^n$을 연립선형방정식의 해(solution)라고 한다.

책의 예시로 이해하면 쉽다. 한 공장에 자원이 $m$종류, 만들 수 있는 제품이 $n$종류 있다고 하자. $a_{ij}$는 제품 $j$를 한 단위 만드는 데 필요한 자원 $i$의 양, $b_i$는 자원 $i$의 보유량, $x_j$는 제품 $j$의 생산량이다. 자원을 남기지 않고 모두 써서 생산하는 계획을 찾는 일이 곧 위의 식을 푸는 일이다.

### 해의 개수와 기하학적 의미

실수 연립선형방정식의 해는 다음 셋 중 하나다.

1. 해가 없다.
2. 해가 하나다.
3. 해가 무수히 많다.

해가 없는 경우에도 근사적인 해는 구할 수 있는데, 이것이 Linear Regression에서 다루는 문제다.

기하학적으로 보면, 연립선형방정식의 해집합은 각 방정식이 나타내는 도형이 서로 겹치는 영역(intersection)이다. 변수가 $x_1, x_2$ 두 개라면 방정식 하나가 직선 하나이므로, 방정식 두 개의 해집합은 다음과 같이 나뉜다.

| 해의 개수 | 두 직선의 관계 | 해집합 |
|---|---|---|
| 없다 | 평행해서 만나지 않는다 | 공집합 |
| 하나다 | 한 점에서 만난다 | 점 |
| 무수히 많다 | 완전히 겹친다 | 직선 |

변수가 $x_1, x_2, x_3$ 세 개가 되면 방정식 하나는 평면을 나타내고, 해집합으로 평면까지 나올 수 있다.

### 동차와 비동차

주어진 연립선형방정식의 모든 해가 모여 이루는 공간을 solution space라고 한다. 상수항에 따라 두 경우로 나눈다.

- **Homogeneous system(동차)**: $Ax = \mathbf{0}$처럼 상수항이 모두 0인 경우다. 영벡터 $\mathbf{0}$이 항상 해이므로 해가 적어도 하나는 있다. 해끼리 선형 결합해도 다시 해가 되므로 해 공간은 vector space이고, 항상 원점을 지나는 부분공간이다.
- **Non-homogeneous system(비동차)**: $Ax = b$에서 $b \neq \mathbf{0}$인 경우다. 해가 없거나, 하나이거나, 무수히 많다.

### 행렬 표기

같은 식을 열벡터의 선형 결합으로 쓸 수 있다.

$$
x_1 \begin{bmatrix} a_{11} \\ \vdots \\ a_{m1} \end{bmatrix}

+ x_2 \begin{bmatrix} a_{12} \\ \vdots \\ a_{m2} \end{bmatrix}
+ \cdots
+ x_n \begin{bmatrix} a_{1n} \\ \vdots \\ a_{mn} \end{bmatrix}
= \begin{bmatrix} b_{1} \\ \vdots \\ b_{m} \end{bmatrix}
$$

이를 다시 묶으면 $Ax = b$ 형태가 된다.

$$
\begin{bmatrix} a_{11} & \cdots & a_{1n} \\ \vdots & & \vdots \\ a_{m1} & \cdots & a_{mn} \end{bmatrix}
\begin{bmatrix} x_1 \\ \vdots \\ x_n \end{bmatrix}
= \begin{bmatrix} b_1 \\ \vdots \\ b_m \end{bmatrix}
$$

"$b$를 $A$의 열들의 선형 결합으로 표현할 수 있는가"라는 이 관점은 뒤에서 해를 구할 때 그대로 쓰인다.

## 2.2 행렬

### 곱셈의 성질

행렬 곱셈에 대해서는 다음만 기억해 두었다.

- 결합법칙(associativity)이 성립한다: $(AB)C = A(BC)$
- 분배법칙(distributivity)이 성립한다: $(A + B)C = AC + BC$, $A(C + D) = AC + AD$
- 교환법칙(commutativity)은 일반적으로 성립하지 않는다: $AB \neq BA$

### 역행렬과 전치

정방행렬 $A \in \mathbb{R}^{n \times n}$에 대해 $AB = I_n = BA$를 만족하는 $B \in \mathbb{R}^{n \times n}$가 있으면 $B$를 $A$의 역행렬(inverse)이라 하고 $A^{-1}$로 쓴다. 역행렬이 있으면 $A$를 regular / invertible / nonsingular하다고 하고, 없으면 singular / noninvertible하다고 한다.

$2 \times 2$ 행렬의 역행렬은 다음과 같다.

$$
A^{-1} = \frac{1}{a_{11}a_{22} - a_{12}a_{21}}
\begin{bmatrix} a_{22} & -a_{12} \\ -a_{21} & a_{11} \end{bmatrix}
$$

따라서 $2 \times 2$ 행렬의 역행렬은 $a_{11}a_{22} - a_{12}a_{21} \neq 0$, 즉 $\det(A) \neq 0$일 때, 그리고 그때에만 존재한다.

역행렬과 전치(transpose)의 계산 규칙은 다음과 같다.

$$
\begin{aligned}
AA^{-1} &= I = A^{-1}A \\
(AB)^{-1} &= B^{-1}A^{-1} \\
(A + B)^{-1} &\neq A^{-1} + B^{-1} \\
(A^\top)^\top &= A \\
(AB)^\top &= B^\top A^\top \\
(A + B)^\top &= A^\top + B^\top
\end{aligned}
$$

$A = A^\top$인 행렬을 대칭행렬(symmetric matrix)이라 한다. 정방행렬만 대칭행렬이 될 수 있다. 또한 $A$가 invertible하면 $A^\top$도 invertible하고 다음이 성립한다.

$$
(A^{-1})^\top = (A^\top)^{-1} =: A^{-\top}
$$

scalar 곱(2.2.3)과 연립선형방정식의 행렬 표기(2.2.4)는 위에서 본 내용과 겹쳐 따로 정리하지 않았다.

## 2.3 연립선형방정식 풀기

### 특수해와 일반해

다음 시스템을 보자.

$$
\begin{bmatrix} 1 & 0 & 8 & -4 \\ 0 & 1 & 2 & 12 \end{bmatrix}
\begin{bmatrix} x_1 \\ x_2 \\ x_3 \\ x_4 \end{bmatrix}
= \begin{bmatrix} 42 \\ 8 \end{bmatrix}
$$

미지수가 4개, 방정식이 2개라서 해가 무수히 많을 것으로 예상할 수 있다. 여기서 우변 $b$를 $A$의 열벡터의 조합으로 표현할 수 있다면, 그 계수가 곧 해 하나가 된다. $A$의 열을 왼쪽부터 $c_1, c_2, c_3, c_4$라고 부르겠다.

**특수해(particular solution).** 첫 번째 열과 두 번째 열이 $\begin{bmatrix} 1 \\ 0 \end{bmatrix}$, $\begin{bmatrix} 0 \\ 1 \end{bmatrix}$이므로 바로 다음과 같이 쓸 수 있다.

$$
\begin{bmatrix} 42 \\ 8 \end{bmatrix}
= 42 \begin{bmatrix} 1 \\ 0 \end{bmatrix} + 8 \begin{bmatrix} 0 \\ 1 \end{bmatrix}
$$

즉 $x_1 = 42,\ x_2 = 8,\ x_3 = 0,\ x_4 = 0$이 해 하나이고, 이를 $x_p = [42, 8, 0, 0]^\top$라 한다.

**동차해(homogeneous solution).** 나머지 해를 모두 찾으려면 $Ax_h = \mathbf{0}$을 만족하는 $x_h$를 찾으면 된다. $A(x_p + x_h) = b + \mathbf{0} = b$이므로 전체 해는 $x = x_p + x_h$ 꼴이 된다. 동차해의 집합이 null space이며, 이를 구하려면 열벡터 사이의 선형 종속 관계를 찾으면 된다.

세 번째 열은 앞의 두 열로 표현된다.

$$
\begin{bmatrix} 8 \\ 2 \end{bmatrix}
= 8 \begin{bmatrix} 1 \\ 0 \end{bmatrix} + 2 \begin{bmatrix} 0 \\ 1 \end{bmatrix}
\quad\Longrightarrow\quad c_3 = 8c_1 + 2c_2
$$

동차방정식 $Ax = x_1c_1 + x_2c_2 + x_3c_3 + x_4c_4 = \mathbf{0}$에 이 관계를 대입하면 다음과 같다.

$$
(x_1 + 8x_3)c_1 + (x_2 + 2x_3)c_2 + x_4c_4 = \mathbf{0}
$$

우선 $x_4 = 0$으로 두자. $c_1, c_2$는 선형 독립이므로 남은 두 계수가 모두 0이어야 한다.

$$
x_1 + 8x_3 = 0, \qquad x_2 + 2x_3 = 0
$$

$x_3$은 자유롭게 고를 수 있으므로 $x_3 = 1$로 두면 $x_1 = -8,\ x_2 = -2$다. 따라서 첫 번째 동차해는 다음과 같다.

$$
x_h^{(1)} = \lambda_1 \begin{bmatrix} -8 \\ -2 \\ 1 \\ 0 \end{bmatrix}, \quad \lambda_1 \in \mathbb{R}
$$

네 번째 열도 같은 방식으로 처리한다.

$$
\begin{bmatrix} -4 \\ 12 \end{bmatrix}
= -4 \begin{bmatrix} 1 \\ 0 \end{bmatrix} + 12 \begin{bmatrix} 0 \\ 1 \end{bmatrix}
\quad\Longrightarrow\quad c_4 = -4c_1 + 12c_2
$$

이번에는 $x_3 = 0,\ x_4 = 1$로 두면 $x_1 = 4,\ x_2 = -12$를 얻는다.

$$
x_h^{(2)} = \lambda_2 \begin{bmatrix} 4 \\ -12 \\ 0 \\ 1 \end{bmatrix}, \quad \lambda_2 \in \mathbb{R}
$$

이렇게 구한 두 벡터는 null space의 basis로 볼 수 있다.

**일반해(general solution).** 특수해와 동차해를 합치면 모든 해를 나타낼 수 있다.

$$
x = \begin{bmatrix} 42 \\ 8 \\ 0 \\ 0 \end{bmatrix}

+ \lambda_1 \begin{bmatrix} -8 \\ -2 \\ 1 \\ 0 \end{bmatrix}
+ \lambda_2 \begin{bmatrix} 4 \\ -12 \\ 0 \\ 1 \end{bmatrix},
\quad \lambda_1, \lambda_2 \in \mathbb{R}
$$

정리하면 일반해를 구하는 과정은 다음과 같다.

1. $Ax = b$의 특수해를 하나 찾는다.
2. $Ax = \mathbf{0}$의 모든 해(동차해)를 찾는다.
3. 1과 2를 더해 일반해를 얻는다.

단, 특수해는 하나로 정해져 있지 않고(해 중 어느 것을 골라도 된다), 동차해를 나타내는 벡터의 선택도 유일하지 않다.

위 예제는 처음부터 풀기 쉬운 형태여서 일반해를 바로 구할 수 있었다. 일반적인 시스템은 그렇지 않으므로, 먼저 시스템을 이런 간단한 형태로 바꿔야 한다. 그 절차가 Gaussian Elimination이다.

### 기본 행 연산과 행사다리꼴

시스템을 푸는 핵심 도구는 elementary transformations다. 해집합은 그대로 유지하면서 시스템을 더 간단한 형태로 바꾼다.

1. 두 행을 교환한다.
2. 한 행에 0이 아닌 scalar를 곱한다.
3. 한 행에 다른 행의 scalar 배를 더한다.

이 연산으로 아래 시스템의 해를 구해 보자. $a \in \mathbb{R}$는 아직 정해지지 않은 상수다.

$$
\begin{aligned}
-2x_1 + 4x_2 - 2x_3 - x_4 + 4x_5 &= -3 \\
4x_1 - 8x_2 + 3x_3 - 3x_4 + x_5 &= 2 \\
x_1 - 2x_2 + x_3 - x_4 + x_5 &= 0 \\
x_1 - 2x_2 - 3x_4 + 4x_5 &= a
\end{aligned}
$$

먼저 계수와 우변을 붙여 augmented matrix $[A \mid b]$로 쓴다.

$$
\left[
\begin{array}{ccccc|c}
-2 & 4 & -2 & -1 & 4 & -3 \\
4 & -8 & 3 & -3 & 1 & 2 \\
1 & -2 & 1 & -1 & 1 & 0 \\
1 & -2 & 0 & -3 & 4 & a
\end{array}
\right]
$$

1행과 3행을 바꾼 뒤 위에서부터 차례로 아래 행의 성분을 소거하고, 마지막에 pivot이 1이 되도록 행에 scalar를 곱하면 row-echelon form(행사다리꼴)이 된다.

$$
\left[
\begin{array}{ccccc|c}
1 & -2 & 1 & -1 & 1 & 0 \\
0 & 0 & 1 & -1 & 3 & -2 \\
0 & 0 & 0 & 1 & -2 & 1 \\
0 & 0 & 0 & 0 & 0 & a + 1
\end{array}
\right]
$$

마지막 행은 $0 = a + 1$을 뜻하므로, 이 시스템은 $a = -1$일 때만 해를 가진다.

**Pivot과 변수의 구분.** 각 행에서 왼쪽부터 처음 나오는 0이 아닌 수를 pivot이라 한다. row-echelon form에서는 pivot이 항상 윗행의 pivot보다 오른쪽에 있으므로 전체가 계단(staircase) 모양이 된다.

- pivot이 있는 열의 변수를 basic variable(leading variable)이라 한다. 위 예제에서는 $x_1, x_3, x_4$다.
- pivot이 없는 열의 변수를 free variable이라 한다. 위 예제에서는 $x_2, x_5$다.

**row-echelon form에서 특수해 구하기.** row-echelon form은 특수해를 구하기 쉽게 만들어 준다. free variable을 0으로 두고, 우변을 pivot column의 선형 결합으로 표현하면 된다. $a = -1$일 때 식은 다음과 같다.

$$
\lambda_1 \begin{bmatrix} 1 \\ 0 \\ 0 \\ 0 \end{bmatrix}

+ \lambda_2 \begin{bmatrix} 1 \\ 1 \\ 0 \\ 0 \end{bmatrix}
+ \lambda_3 \begin{bmatrix} -1 \\ -1 \\ 1 \\ 0 \end{bmatrix}
= \begin{bmatrix} 0 \\ -2 \\ 1 \\ 0 \end{bmatrix}
$$

아래 행부터 순서대로 풀면 $\lambda_3 = 1$, 이어서 $\lambda_2 = -1$, $\lambda_1 = 2$를 얻는다. free variable 자리에 0을 채우면 특수해는 다음과 같다.

$$
x = [2, 0, -1, 1, 0]^\top
$$

### 기약 행사다리꼴과 동차해

다음 조건을 만족하는 행렬을 reduced row-echelon form(기약 행사다리꼴)이라 한다.

1. row-echelon form이다.
2. 모든 pivot이 1이다.
3. pivot은 그 열에서 유일한 0이 아닌 성분이다.

reduced row-echelon form까지 만들면 일반해를 아주 간단하게 구할 수 있다. 다음 행렬을 예로 들자.

$$
A = \begin{bmatrix}
\mathbf{1} & 3 & 0 & 0 & 3 \\
0 & 0 & \mathbf{1} & 0 & 9 \\
0 & 0 & 0 & \mathbf{1} & -4
\end{bmatrix}
$$

$Ax = \mathbf{0}$의 해를 구하는 가장 좋은 방법은 non-pivot column을 그 왼쪽에 있는 pivot column으로 표현하는 것이다. 여기서 pivot column은 1, 3, 4번째 열이고 non-pivot column은 2, 5번째 열이다. 각 행을 식으로 풀어 쓰면 다음과 같다.

$$
\begin{aligned}
x_1 &= -3x_2 - 3x_5 \\
x_2 &= x_2 \\
x_3 &= -9x_5 \\
x_4 &= 4x_5 \\
x_5 &= x_5
\end{aligned}
$$

$x_2, x_5$는 free variable이라 어떤 값을 넣어도 식이 성립한다. $(x_2, x_5) = (-1, 0)$과 $(0, -1)$을 각각 넣으면 동차해 전체, 즉 null space를 얻는다.

$$
\left\{
x \in \mathbb{R}^5 : x = \lambda_1 \begin{bmatrix} 3 \\ -1 \\ 0 \\ 0 \\ 0 \end{bmatrix}

+ \lambda_2 \begin{bmatrix} 3 \\ 0 \\ 9 \\ -4 \\ -1 \end{bmatrix},
\ \lambda_1, \lambda_2 \in \mathbb{R}
\right\}
$$

free variable 자리에 $-1$이 놓인 이 형태는 책에서 Minus-1 Trick이라는 이름으로 소개하는 방법의 결과와 같다. Minus-1 Trick 자체의 절차(2.3.3)는 따로 정리하지 않았다.

어떤 시스템의 해를 구하기 위해 elementary transformations로 시스템을 row-echelon form으로 만드는 과정을 **Gaussian Elimination**이라 한다.

### 역행렬 계산

$A \in \mathbb{R}^{n \times n}$의 역행렬은 $AA^{-1} = I_n$ 관계를 이용해 구한다. $A$와 단위행렬을 나란히 붙인 augmented matrix를 만들고, elementary transformations로 왼쪽을 단위행렬로 바꾸면 오른쪽에 $A^{-1}$이 남는다.

$$
[\,A \mid I_n\,] \ \longrightarrow \ [\,I_n \mid A^{-1}\,]
$$

### 연립선형방정식을 푸는 알고리즘

**Moore-Penrose pseudo-inverse.** $A$가 정방행렬이고 invertible하면 $x = A^{-1}b$로 바로 풀 수 있다. 그렇지 않더라도 $A$의 열들이 선형 독립이면 pseudo-inverse $(A^\top A)^{-1}A^\top$를 써서 해를 구할 수 있다.

$$
Ax = b \ \Longrightarrow \ A^\top Ax = A^\top b \ \iff \ x = (A^\top A)^{-1}A^\top b
$$

$Ax = b$를 정확히 만족하는 해가 없을 때 이 식이 주는 $x$는 최소제곱 해다. 이 방식의 단점은 $A^\top A$의 곱과 역행렬을 계산하는 데 연산이 많이 든다는 것이다.

**Gaussian Elimination.** determinant 계산, vector들의 선형 독립 여부 확인, 역행렬 계산, 행렬의 rank 계산, vector space의 basis 계산 등에 폭넓게 쓰인다. 변수가 수천 개인 시스템에는 효과적이지만 수백만 개 수준에서는 비효율적이다.

**Iterative methods.** 변수가 아주 많은 시스템은 직접 푸는 대신 다음과 같은 식을 반복해서 계산한다.

$$
x^{(k+1)} = Cx^{(k)} + d
$$

$C$와 $d$를 적절히 고르면 반복할 때마다 오차 $\lVert x^{(k+1)} - x_* \rVert$가 줄어들어 $x_*$로 수렴한다. $x_*$는 $Ax = b$의 해이고, norm $\lVert \cdot \rVert$은 두 vector가 얼마나 가까운지를 재는 기준이다.

## 정리

- 연립선형방정식의 해는 없거나, 하나이거나, 무수히 많다. 기하학적으로는 각 방정식이 나타내는 도형의 교집합이다.
- 일반해는 특수해 하나와 동차해 전체의 합이다: $x = x_p + x_h$.
- Gaussian Elimination으로 row-echelon form을 만들면 pivot과 free variable이 드러나고, reduced row-echelon form까지 가면 null space를 바로 읽을 수 있다.
- free variable 하나마다 동차해 벡터가 하나씩 나온다. 이 관찰은 [3편](03-linear-mappings-and-affine-spaces.md)의 rank-nullity theorem과 해집합의 차원으로 이어진다.

## 참고 자료

- M. P. Deisenroth, A. A. Faisal, C. S. Ong, "Mathematics for Machine Learning", Cambridge University Press, 2020. 2.1 ~ 2.3절. <https://mml-book.github.io/>
