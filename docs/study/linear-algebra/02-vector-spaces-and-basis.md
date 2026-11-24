---
title: "MML 2.4 ~ 2.6: 벡터 공간, 선형 독립, 기저와 랭크"
date: 2025-06-12
last_modified_at: 2025-06-12
category: study
tags: [선형대수, 벡터 공간, 선형 독립, 기저, 랭크, MML]
---

# MML 2.4 ~ 2.6: 벡터 공간, 선형 독립, 기저와 랭크

작성일 2025-06-12
{ .post-date }

"Mathematics for Machine Learning"(이하 MML) 2장의 2.4 Vector Spaces, 2.5 Linear Independence, 2.6 Basis and Rank를 읽고 정리한 글이다. [앞 글](01-linear-systems-and-matrices.md)에서 "덧셈과 scalar 곱에 대해 닫힌 집합"이라고만 말했던 vector space를 정식으로 정의하고, 그 공간을 최소한의 vector로 표현하는 방법인 basis와, 행렬이 가진 독립적인 방향의 수인 rank까지 다룬다.

## 2.4 벡터 공간

### Group

집합 $\mathcal{G}$와 그 위의 연산 $\otimes : \mathcal{G} \times \mathcal{G} \to \mathcal{G}$가 있다고 하자. 연산의 정의부터가 "$\mathcal{G}$의 원소 둘에 연산을 적용해도 결과가 여전히 $\mathcal{G}$ 안에 있다"는 뜻이다. $G := (\mathcal{G}, \otimes)$가 다음 네 가지를 만족하면 group이라 한다.

1. **Closure**: $\forall x, y \in \mathcal{G} : x \otimes y \in \mathcal{G}$
2. **Associativity**: $\forall x, y, z \in \mathcal{G} : (x \otimes y) \otimes z = x \otimes (y \otimes z)$
3. **Neutral element**: $\exists e \in \mathcal{G}\ \forall x \in \mathcal{G} : x \otimes e = x$ 이고 $e \otimes x = x$
4. **Inverse element**: $\forall x \in \mathcal{G}\ \exists y \in \mathcal{G} : x \otimes y = e$ 이고 $y \otimes x = e$ ($e$는 neutral element)

$x$의 inverse는 $x^{-1}$로 쓴다. 여기에 더해 교환법칙 $\forall x, y \in \mathcal{G} : x \otimes y = y \otimes x$까지 성립하면 Abelian group이라 한다.

### Vector space

실수 vector space $V = (\mathcal{V}, +, \cdot)$는 집합 $\mathcal{V}$와 다음 두 연산으로 이루어진다.

$$
\begin{aligned}

+ &: \mathcal{V} \times \mathcal{V} \to \mathcal{V} \\
\cdot &: \mathbb{R} \times \mathcal{V} \to \mathcal{V}
\end{aligned}
$$

그리고 다음을 만족한다.

1. $(\mathcal{V}, +)$는 Abelian group이다.
2. **Distributivity**: scalar 곱에 대한 분배법칙이 성립한다.
    - $\forall \lambda \in \mathbb{R},\ x, y \in \mathcal{V} : \lambda \cdot (x + y) = \lambda \cdot x + \lambda \cdot y$
    - $\forall \lambda, \psi \in \mathbb{R},\ x \in \mathcal{V} : (\lambda + \psi) \cdot x = \lambda \cdot x + \psi \cdot x$
3. **Associativity (outer operation)**: scalar 곱에 대한 결합법칙이 성립한다.
    - $\forall \lambda, \psi \in \mathbb{R},\ x \in \mathcal{V} : \lambda \cdot (\psi \cdot x) = (\lambda\psi) \cdot x$
4. **Neutral element (outer operation)**: scalar 1이 scalar 곱의 중립 원소다.
    - $\forall x \in \mathcal{V} : 1 \cdot x = x$

용어를 정리하면 다음과 같다.

- $\mathcal{V}$의 원소 $x$를 vector라 한다.
- $(\mathcal{V}, +)$의 neutral element는 zero vector $\mathbf{0} = [0, \dots, 0]^\top$이다.
- $+$는 inner operation(vector addition), $\cdot$은 outer operation(multiplication by scalars)이라 한다.

vector끼리의 곱 $ab\ (a, b \in \mathbb{R}^n)$는 정의되지 않는다. 성분별 곱 $c_j = a_jb_j$ 같은 것을 정의할 수는 있지만, vector를 $n \times 1$ 행렬로 보고 행렬 곱 규칙을 따르면 정의되는 것은 다음 둘뿐이다.

- outer product: $ab^\top \in \mathbb{R}^{n \times n}$
- inner product(dot product): $a^\top b \in \mathbb{R}$

### Vector subspace

vector space $V = (\mathcal{V}, +, \cdot)$와 공집합이 아닌 부분집합 $\mathcal{U} \subseteq \mathcal{V}$가 있다고 하자. $V$의 두 연산을 $\mathcal{U} \times \mathcal{U}$와 $\mathbb{R} \times \mathcal{U}$로 제한했을 때 $U = (\mathcal{U}, +, \cdot)$가 그 자체로 vector space이면, $U$를 $V$의 vector subspace라 한다. 즉 subspace는 덧셈과 scalar 곱의 결과가 다시 $\mathcal{U}$ 안에 들어오도록 닫혀 있다.

달리 말하면 subspace는 vector space의 부분집합이므로, 표현할 수 있는 vector의 범위가 더 좁은 공간이라고 볼 수 있다. 큰 원 $\mathcal{V}$ 안에 작은 원 $\mathcal{U}$가 들어 있는 그림을 떠올리면 된다.

모든 vector space $V$에서 가장 자명한 subspace는 $V$ 자신과 $\{\mathbf{0}\}$이다.

책에는 $\mathbb{R}^2$의 부분집합 네 개를 놓고 어느 것이 subspace인지 묻는 그림이 있다. A는 원점을 포함하는 정사각형 영역, B는 원점을 지나지 않는 직선, C는 원점에서 만나는 두 직선 사이의 마주 보는 부채꼴 영역, D는 원점 하나다. 이 가운데 D만 subspace다.

- A는 vector를 더하면 결과가 영역 밖으로 나갈 수 있다.
- B는 영벡터를 포함하지 않는다.
- C도 vector를 더하면 영역 밖으로 나갈 수 있다.
- D는 영벡터를 포함하고 덧셈과 scalar 곱에 닫혀 있어 조건을 모두 만족한다.

연립선형방정식의 해집합과 연결하면 다음과 같다.

- homogeneous system $Ax = \mathbf{0}$의 해집합은 $\mathbb{R}^n$의 subspace다. 영벡터를 포함하고, 두 해 $x_1, x_2$의 합과 scalar 배가 다시 해가 되므로 closure도 만족한다.
- 역도 성립한다. $\mathbb{R}^n$의 모든 subspace는 어떤 homogeneous system $Ax = \mathbf{0}$의 solution space다.
- 반면 inhomogeneous system $Ax = b\ (b \neq \mathbf{0})$의 해집합은 영벡터를 포함하지 않으므로 $\mathbb{R}^n$의 subspace가 아니다. 이 해집합이 어떤 공간인지는 [3편](03-linear-mappings-and-affine-spaces.md)의 affine subspace에서 다룬다.

## 2.5 선형 독립

### Linear combination

vector들을 선형 결합하면 새로운 vector를 만들 수 있다. vector space $V$의 vector $x_1, \dots, x_k$에 대해 다음과 같은 $v$를 linear combination이라 한다.

$$
v = \lambda_1x_1 + \cdots + \lambda_kx_k = \sum_{i=1}^{k} \lambda_ix_i \in V, \quad \lambda_1, \dots, \lambda_k \in \mathbb{R}
$$

### Linear (in)dependence

vector space $V$의 vector $x_1, \dots, x_k \in V\ (k \in \mathbb{N})$를 생각하자.

- 적어도 하나의 $\lambda_i \neq 0$인 자명하지 않은 선형 결합으로 $\mathbf{0} = \sum_{i=1}^{k} \lambda_ix_i$를 만들 수 있으면, 이 vector들은 **linearly dependent**(선형 종속)다.
- $\lambda_1 = \cdots = \lambda_k = 0$인 자명한 해만 존재하면 **linearly independent**(선형 독립)다.

linear independence는 선형대수학에서 가장 중요한 개념 중 하나다. vector 집합이 선형 독립이라는 것은 그 vector들이 서로 중복되는 정보를 갖고 있지 않다는 뜻이다. 따라서 그중 하나라도 제거하면 무언가를 잃게 된다.

알아 둘 성질은 다음과 같다.

- vector 집합은 반드시 선형 독립이거나 선형 종속이다. 제3의 경우는 없다.
- 집합에 영벡터가 하나라도 있거나, 서로 같은 vector가 둘 있으면 반드시 선형 종속이다.
- 영벡터가 없는 $k \ge 2$개의 vector $x_1, \dots, x_k$가 선형 종속이라는 것은, 그중 적어도 하나가 나머지 vector들의 선형 결합으로 표현된다는 것과 같다. 특히 한 vector가 다른 vector의 scalar 배이면 그 집합은 선형 종속이다.

### Gaussian Elimination으로 판정하기

선형 독립을 판정하는 가장 실용적인 방법은 Gaussian Elimination이다. 모든 vector를 행렬의 열로 놓고 row-echelon form이 될 때까지 소거한다.

- pivot column에 해당하는 vector는 그 왼쪽 vector들과 선형 독립이다.
- non-pivot column은 그 왼쪽에 있는 pivot column들의 선형 결합으로 표현된다. 즉 선형 종속이다.

다시 말해, 열벡터로 만든 행렬에서 모든 열이 pivot column이면 그 vector들은 선형 독립이고, 반대로 선형 독립이면 모든 열이 pivot column이다. [앞 글](01-linear-systems-and-matrices.md)의 예제에서 non-pivot column을 pivot column으로 표현해 동차해를 구한 것이 바로 이 선형 종속 관계를 이용한 것이다.

## 2.6 기저와 랭크

vector space $V$ 안에서 특히 관심 있는 vector 집합 $\mathcal{A}$는, 그 원소들의 linear combination으로 모든 $v \in V$를 만들어 낼 수 있는 집합이다.

### Generating set과 span

vector space $V = (\mathcal{V}, +, \cdot)$와 vector 집합 $\mathcal{A} = \{x_1, \dots, x_k\} \subseteq \mathcal{V}$가 있다고 하자.

- 모든 $v \in \mathcal{V}$가 $x_1, \dots, x_k$의 linear combination으로 표현되면 $\mathcal{A}$를 $V$의 generating set이라 한다.
- $\mathcal{A}$에 속한 vector들의 모든 linear combination의 집합을 $\mathcal{A}$의 span이라 한다. $\mathcal{A}$가 $V$를 span하면 $V = \text{span}[\mathcal{A}]$ 또는 $V = \text{span}[x_1, \dots, x_k]$로 쓴다.

즉 generating set은 어떤 vector (sub)space를 span, 곧 선형 결합으로 생성한다.

### Basis

$V$의 generating set $\mathcal{A}$에 대해, $V$를 span하는 더 작은 집합 $\tilde{\mathcal{A}} \subsetneq \mathcal{A}$가 존재하지 않으면 $\mathcal{A}$를 minimal하다고 한다. 선형 독립인 generating set은 모두 minimal이며, 이를 $V$의 basis(기저)라 한다.

basis $\mathcal{B}$에 대해 다음은 모두 같은 말이다.

- $\mathcal{B}$는 $V$의 minimal generating set이다.
- $\mathcal{B}$는 $V$ 안에서 더 키울 수 없는 가장 큰 선형 독립 집합이다. 여기에 다른 vector를 하나라도 추가하면 선형 종속이 된다.
- 모든 $x \in V$는 $\mathcal{B}$의 vector들의 선형 결합으로 표현되고, 그 표현은 유일하다.

basis와 차원에 대해 정리한 내용은 다음과 같다.

- 모든 vector space는 basis를 가진다.
- basis는 유일하지 않다. 하지만 한 vector space의 모든 basis는 같은 수의 basis vector를 가진다.
- 이 basis vector의 수를 $V$의 차원이라 하고 $\dim(V)$로 쓴다. vector space의 차원은 그 공간에 있는 독립적인 방향의 수로 볼 수 있다.
- vector space의 차원이 vector의 성분 개수와 항상 같은 것은 아니다. 예를 들어 $\text{span}\left[\begin{bmatrix} 0 \\ 1 \end{bmatrix}\right]$은 성분이 두 개인 vector로 이루어져 있지만 1차원이다.
- subspace $U \subseteq V$에 대해 $\dim(U) \le \dim(V)$이고, $\dim(U) = \dim(V)$이면 $U = V$다. 다시 말해 자기 자신이 아닌 subspace의 차원은 항상 원래 공간의 차원보다 낮다.

**basis를 찾는 방법.** subspace $U = \text{span}[x_1, \dots, x_m]$의 basis는 다음과 같이 찾는다.

1. spanning vector들을 행렬 $A$의 열로 놓는다.
2. $A$를 row-echelon form으로 만든다.
3. pivot column 자리에 있던 원래의 spanning vector들이 $U$의 basis다.

### Rank

행렬 $A \in \mathbb{R}^{m \times n}$에서 선형 독립인 열의 수는 선형 독립인 행의 수와 같다. 이 수를 $A$의 rank라 하고 $\text{rk}(A)$로 쓴다.

rank의 중요한 성질은 다음과 같다.

1. $\text{rk}(A) = \text{rk}(A^\top)$이다. 즉 column rank와 row rank가 같다.
2. $A \in \mathbb{R}^{m \times n}$의 열들은 $\dim(U) = \text{rk}(A)$인 subspace $U \subseteq \mathbb{R}^m$를 span한다. $U$가 $\mathbb{R}^m$ 안에 있는 이유는 $n$개의 열벡터 각각이 성분 $m$개짜리 vector이기 때문이다. 이 subspace를 image 또는 range라 한다. $U$의 basis는 $A$에 Gaussian Elimination을 적용해 pivot column을 찾으면 얻을 수 있다.
3. $A \in \mathbb{R}^{m \times n}$의 행들은 $\dim(W) = \text{rk}(A)$인 subspace $W \subseteq \mathbb{R}^n$를 span한다. 행벡터의 성분이 $n$개이기 때문이다. $W$의 basis도 Gaussian Elimination으로 찾을 수 있다(이번에는 $A^\top$에 적용한다).
4. 정방행렬 $A \in \mathbb{R}^{n \times n}$가 invertible한 것과 $\text{rk}(A) = n$인 것은 동치다.
5. $A \in \mathbb{R}^{m \times n}$와 $b \in \mathbb{R}^m$에 대해, $Ax = b$가 풀리는 것과 $\text{rk}(A) = \text{rk}(A \mid b)$인 것은 동치다. $(A \mid b)$는 augmented matrix다.
6. $A \in \mathbb{R}^{m \times n}$에 대해 $Ax = \mathbf{0}$의 해가 이루는 subspace의 차원은 $n - \text{rk}(A)$다. 이 subspace를 kernel 또는 null space라 한다.
7. rank가 그 크기의 행렬이 가질 수 있는 최댓값이면 full rank라 한다. 이때 $\text{rk}(A) = \min(m, n)$이다. full rank가 아닌 행렬은 rank deficient라 한다.

4번에 대해서는 따로 생각을 정리해 두었다. $\text{rk}(A) = n$이라는 것은 모든 행과 열이 선형 독립이라는 뜻이고, 이 조건은 $\det(A) \neq 0$과 동치라고 볼 수 있다. 의미로 보면 $A$가 정보를 잃지 않는 완전한 변환(full rank)을 한다는 것이다. 행렬 $A$는 $\mathbb{R}^n \to \mathbb{R}^n$의 선형 사상이므로, 이 변환을 되돌릴 수 있으려면 서로 다른 입력 $x$가 서로 다른 출력으로 가야 한다. 즉 kernel이 영벡터만 가진 자명한 경우여야 하고, 이는 곧 $\text{rk}(A) = n$과 같은 조건이다. image와 kernel은 [다음 글](03-linear-mappings-and-affine-spaces.md)에서 선형 사상의 관점으로 다시 정의한다.

## 정리

- group에서 시작해 Abelian group, vector space, vector subspace 순으로 조건을 쌓아 올린다. subspace는 영벡터를 포함하고 덧셈과 scalar 곱에 닫혀 있어야 한다.
- 영벡터를 만드는 자명하지 않은 선형 결합이 있으면 선형 종속, 자명한 것뿐이면 선형 독립이다. row-echelon form에서 모든 열이 pivot column인지로 판정한다.
- basis는 선형 독립인 generating set이고, 그 vector의 수가 차원이다.
- rank는 선형 독립인 열(행)의 수다. $Ax = \mathbf{0}$의 해 공간의 차원은 $n - \text{rk}(A)$다.

## 참고 자료

- M. P. Deisenroth, A. A. Faisal, C. S. Ong, "Mathematics for Machine Learning", Cambridge University Press, 2020. 2.4 ~ 2.6절. <https://mml-book.github.io/>
