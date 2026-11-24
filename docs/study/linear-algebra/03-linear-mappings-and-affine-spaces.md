---
title: "MML 2.7 ~ 2.8: 선형 사상과 아핀 공간"
date: 2025-06-12
last_modified_at: 2025-06-12
category: study
tags: [선형대수, 선형 사상, 기저 변환, 커널, 아핀 공간, MML]
---

# MML 2.7 ~ 2.8: 선형 사상과 아핀 공간

"Mathematics for Machine Learning"(이하 MML) 2장의 마지막 부분인 2.7 Linear Mappings와 2.8 Affine Spaces를 읽고 정리한 글이다. [앞 글](02-vector-spaces-and-basis.md)까지는 vector space 하나의 구조를 봤다면, 여기서는 vector space 사이의 mapping을 다룬다. 선형 사상이 행렬로 표현되는 과정, basis를 바꿀 때 그 행렬이 어떻게 변하는지, image와 kernel, 그리고 원점을 지나지 않는 공간인 affine space까지 이어진다.

## 2.7 선형 사상

vector는 서로 더하거나 scalar를 곱해도 여전히 vector인 객체다. mapping을 적용한 뒤에도 이 구조가 유지되기를 바란다. vector space $V, W$ 사이의 mapping $\Phi : V \to W$가 모든 $x, y \in V$와 $\lambda \in \mathbb{R}$에 대해 다음을 만족하면 vector space의 구조를 보존한다고 본다.

$$
\begin{aligned}
\Phi(x + y) &= \Phi(x) + \Phi(y) \\
\Phi(\lambda x) &= \lambda\Phi(x)
\end{aligned}
$$

두 조건을 하나로 합친 것이 linear mapping의 정의다. vector space $V, W$에 대해 mapping $\Phi : V \to W$가 다음을 만족하면 linear mapping(또는 vector space homomorphism, linear transformation)이라 한다.

$$
\forall x, y \in V\ \ \forall \lambda, \psi \in \mathbb{R} : \Phi(\lambda x + \psi y) = \lambda\Phi(x) + \psi\Phi(y)
$$

뒤에서 보듯 linear mapping은 행렬로 표현할 수 있다. 그래서 어떤 행렬을 보면 그 행렬이 무엇을 의미하는지를 잘 파악해야 한다.

### Injective, surjective, bijective

임의의 집합 $\mathcal{V}, \mathcal{W}$ 사이의 mapping $\Phi : \mathcal{V} \to \mathcal{W}$에 대해 다음과 같이 정의한다.

1. **Injective(단사)**: $\forall x, y \in \mathcal{V} : \Phi(x) = \Phi(y) \Longrightarrow x = y$. 서로 다른 입력은 서로 다른 출력으로 간다.
2. **Surjective(전사)**: $\Phi(\mathcal{V}) = \mathcal{W}$. $\mathcal{W}$의 모든 원소가 $\Phi$를 통해 $\mathcal{V}$의 어떤 원소로부터 도달된다.
3. **Bijective(전단사, 일대일대응)**: injective이면서 surjective다.

실수 함수의 그래프로 떠올리면, injective는 x축에 평행한 선을 어디에 그어도 그래프와 많아야 한 점에서 만난다는 것이고, surjective는 공역 안의 어느 높이에 x축과 평행한 선을 그어도 그래프와 만나는 점이 반드시 있다는 것이다.

$\Phi$가 bijective하면 완전히 되돌릴 수 있다. $\Psi \circ \Phi(x) = x$를 만족하는 mapping $\Psi : \mathcal{W} \to \mathcal{V}$가 존재하며, 이를 $\Phi$의 inverse mapping이라 하고 $\Phi^{-1}$로 쓴다.

### 선형 사상의 종류

1. **Isomorphism**: $\Phi : V \to W$가 linear이고 bijective하다. 두 vector space 사이를 완전히 되돌릴 수 있게 잇는 linear mapping이다.
2. **Endomorphism**: $\Phi : V \to V$가 linear하다. 같은 vector space 안에서 일어나는 모든 linear mapping을 포함한다.
3. **Automorphism**: $\Phi : V \to V$가 linear이고 bijective하다. endomorphism 중에서 되돌릴 수 있는 경우다.
4. **Identity mapping**: $\text{id}_V : V \to V,\ x \mapsto x$. identity automorphism이라고도 한다.

homomorphism은 더 일반적인 말로, 대수적 구조 사이에서 연산 구조를 보존하는 mapping을 뜻한다. vector space에서는 덧셈과 scalar 곱을 보존하는 linear mapping이 모두 homomorphism이다.

유한 차원 vector space $V, W$는 $\dim(V) = \dim(W)$일 때, 그리고 그때에만 isomorphic하다. 직관적으로는 차원이 같은 두 vector space는 사실상 같은 것이라고 볼 수 있다. 선형 변환으로 손실 없이 서로 옮겨 갈 수 있기 때문이다.

linear mapping의 성질을 몇 가지 더 적어 둔다.

- $\Phi : V \to W$와 $\Psi : W \to X$가 linear하면 합성 $\Psi \circ \Phi : V \to X$도 linear하다.
- $\Phi : V \to W$가 isomorphism이면 $\Phi^{-1} : W \to V$도 isomorphism이다.
- $\Phi : V \to W$와 $\Psi : V \to W$가 linear하면 $\Phi + \Psi$와 $\lambda\Phi\ (\lambda \in \mathbb{R})$도 linear하다. 두 mapping의 정의역과 공역이 같아서 결과가 모두 같은 공간 $W$ 안에 있고, 그 안에서 vector 덧셈과 scalar 곱이 닫혀 있기 때문이라고 이해했다.

### 2.7.1 선형 사상의 행렬 표현

모든 $n$차원 vector space는 $\mathbb{R}^n$과 isomorphic하다. $n$차원 vector space $V$의 basis를 순서까지 고정해 $B = (b_1, \dots, b_n)$이라 하자(ordered basis).

**Coordinates.** ordered basis $B$를 쓰면 모든 $x \in V$를 유일하게 표현할 수 있다.

$$
x = \alpha_1b_1 + \cdots + \alpha_nb_n
$$

이때 $\alpha_1, \dots, \alpha_n$을 $B$에 대한 $x$의 좌표(coordinates)라 하고, 이를 모은 $\alpha = [\alpha_1, \dots, \alpha_n]^\top \in \mathbb{R}^n$을 coordinate vector라 한다. 이처럼 basis는 좌표계를 정의하는 역할을 한다. $\mathbb{R}^n$의 standard basis $(e_1, \dots, e_n)$에 대해 $\Phi(e_i) = b_i$로 정한 mapping $\Phi : \mathbb{R}^n \to V$는 linear하며, 이것이 $V$와 $\mathbb{R}^n$을 잇는다.

이 아이디어를 바탕으로 linear mapping과 행렬을 직접 연결할 수 있다.

**Transformation matrix.** vector space $V, W$와 각각의 ordered basis $B = (b_1, \dots, b_n)$, $C = (c_1, \dots, c_m)$, 그리고 linear mapping $\Phi : V \to W$가 있다고 하자. 각 $j \in \{1, \dots, n\}$에 대해 $\Phi(b_j)$는 $C$로 유일하게 표현된다.

$$
\Phi(b_j) = \alpha_{1j}c_1 + \cdots + \alpha_{mj}c_m = \sum_{i=1}^{m} \alpha_{ij}c_i
$$

이 계수를 모은 $m \times n$ 행렬 $A_\Phi$를 $\Phi$의 transformation matrix라 한다.

$$
A_\Phi(i, j) = \alpha_{ij}
$$

정리하면 linear mapping $\Phi : V \to W$는 basis를 정하면 행렬 하나로 표현된다. $B$의 vector가 $n$개, $C$의 vector가 $m$개이므로 크기는 $m \times n$이다.

- $C$에 대한 $\Phi(b_j)$의 좌표가 $A_\Phi$의 $j$번째 열이다.
- $\hat{x}$가 $B$에 대한 $x \in V$의 coordinate vector이고, $\hat{y}$가 $C$에 대한 $y = \Phi(x) \in W$의 coordinate vector이면 다음이 성립한다.

$$
\hat{y} = A_\Phi\hat{x}
$$

즉 transformation matrix는 한 vector space에서 다른 vector space로 linear mapping을 할 때, 한쪽 basis에 대한 좌표를 다른 쪽 basis에 대한 좌표로 옮기는 데 쓰인다.

### 2.7.2 기저 변환

linear mapping $\Phi : V \to W$의 transformation matrix가 $V$와 $W$의 basis를 바꿀 때 어떻게 달라지는지 본다. $V$의 ordered basis 두 개와 $W$의 ordered basis 두 개를 생각하자.

$$
B = (b_1, \dots, b_n), \quad \tilde{B} = (\tilde{b}_1, \dots, \tilde{b}_n)
$$

$$
C = (c_1, \dots, c_m), \quad \tilde{C} = (\tilde{c}_1, \dots, \tilde{c}_m)
$$

$A_\Phi \in \mathbb{R}^{m \times n}$는 $B, C$에 대한 $\Phi$의 transformation matrix이고, $\tilde{A}_\Phi \in \mathbb{R}^{m \times n}$는 $\tilde{B}, \tilde{C}$에 대한 transformation matrix다. 두 행렬은 다음 두 행렬을 통해 연결된다.

- $S \in \mathbb{R}^{n \times n}$: $\tilde{B}$에 대한 좌표를 $B$에 대한 좌표로 바꾸는, $\text{id}_V$의 transformation matrix
- $T \in \mathbb{R}^{m \times m}$: $\tilde{C}$에 대한 좌표를 $C$에 대한 좌표로 바꾸는, $\text{id}_W$의 transformation matrix

$$
\tilde{A}_\Phi = T^{-1}A_\Phi S
$$

즉 $A_\Phi$와 양쪽 basis의 관계를 알면, 새 basis에서의 transformation matrix $\tilde{A}_\Phi$를 $A_\Phi$로 표현할 수 있다. 책의 그림은 이 식을 경로로 보여 준다. $\tilde{B}$의 좌표에서 출발해 $\tilde{C}$의 좌표로 바로 가는 대신, $B$와 $C$를 거쳐 돌아가는 것이다.

$$
\tilde{B} \ \xrightarrow{\ S\ } \ B \ \xrightarrow{\ A_\Phi\ } \ C \ \xrightarrow{\ T^{-1}\ } \ \tilde{C}
$$

mapping의 합성으로 쓰면 다음과 같다. $\Phi_{CB}$는 $B$의 좌표를 $C$의 좌표로 보내는 mapping이고, $\Psi_{B\tilde{B}}$와 $\Xi_{C\tilde{C}}$는 각각 $S$와 $T$에 해당한다.

$$
\Phi_{\tilde{C}\tilde{B}} = \Xi_{\tilde{C}C} \circ \Phi_{CB} \circ \Psi_{B\tilde{B}} = \Xi_{C\tilde{C}}^{-1} \circ \Phi_{CB} \circ \Psi_{B\tilde{B}}
$$

이 관계에서 두 가지 용어가 나온다.

- **Equivalence**: $\tilde{A} = T^{-1}AS$를 만족하는 regular matrix $S \in \mathbb{R}^{n \times n}$, $T \in \mathbb{R}^{m \times m}$가 존재하면 $A, \tilde{A} \in \mathbb{R}^{m \times n}$는 equivalent하다.
- **Similarity**: $\tilde{A} = S^{-1}AS$를 만족하는 regular matrix $S \in \mathbb{R}^{n \times n}$가 존재하면 $A, \tilde{A} \in \mathbb{R}^{n \times n}$는 similar하다.

similar한 행렬은 항상 equivalent하다. 하지만 equivalent한 행렬이 항상 similar한 것은 아니다.

### 2.7.3 Image와 kernel

linear mapping $\Phi : V \to W$에서 $V$는 정의역, $W$는 공역이고, $\mathbf{0}_V, \mathbf{0}_W$는 각 공간의 영벡터다.

- **Kernel(null space)**: $V$의 vector 중 $\Phi$를 거치면 $\mathbf{0}_W$로 가는 vector들의 집합이다. 선형 사상을 거치면서 완전히 소멸되는 방향들의 집합이라고 볼 수 있다.
- **Image(range)**: $\Phi$를 통해 실제로 도달할 수 있는 $W$의 vector들의 집합이다.

$$
\ker(\Phi) := \{v \in V : \Phi(v) = \mathbf{0}_W\}, \qquad
\text{Im}(\Phi) := \{w \in W \mid \exists v \in V : \Phi(v) = w\}
$$

책의 그림은 $V$ 안의 $\ker(\Phi)$ 전체가 $W$의 한 점 $\mathbf{0}_W$로 모이고, $V$ 전체는 $W$의 일부인 $\text{Im}(\Phi)$로 옮겨지는 모습을 보여 준다.

- $\Phi(\mathbf{0}_V) = \mathbf{0}_W$는 항상 성립하므로 $\mathbf{0}_V \in \ker(\Phi)$다. 즉 null space는 비어 있는 일이 없다.
- $\text{Im}(\Phi) \subseteq W$는 $W$의 subspace이고, $\ker(\Phi) \subseteq V$는 $V$의 subspace다.
- $\Phi$가 injective한 것과 $\ker(\Phi) = \{\mathbf{0}\}$인 것은 동치다.

**Null space와 column space.** 행렬 $A \in \mathbb{R}^{m \times n}$로 주어지는 linear mapping $\Phi : \mathbb{R}^n \to \mathbb{R}^m,\ x \mapsto Ax$를 생각하자. $A = [a_1, \dots, a_n]$이고 $a_i$가 $A$의 열이면 다음이 성립한다.

$$
\text{Im}(\Phi) = \{Ax : x \in \mathbb{R}^n\}
= \left\{ \sum_{i=1}^{n} x_ia_i : x_1, \dots, x_n \in \mathbb{R} \right\}
= \text{span}[a_1, \dots, a_n] \subseteq \mathbb{R}^m
$$

처음 읽을 때는 "$n$개의 vector가 $m$차원 vector가 된다는 뜻인가?" 하는 의문이 들었다. 풀어 보면 이렇다. $A$의 열은 $n$개이고 각 열은 성분이 $m$개인 vector다. image는 이 열들의 span이므로, column space(image)는 $\mathbb{R}^m$의 subspace다. 여기서 $m$은 행렬의 높이(행의 수)다.

- $\text{rk}(A) = \dim(\text{Im}(\Phi))$다.
- $\ker(\Phi)$는 동차 연립선형방정식 $Ax = \mathbf{0}$의 일반해다. $A$의 열들을 선형 결합해 $\mathbf{0}$을 만드는 계수 vector $x \in \mathbb{R}^n$를 모두 모은 것이다.
- kernel은 $\mathbb{R}^n$의 subspace이고, 여기서 $n$은 행렬의 너비(열의 수)다.

kernel은 열들 사이의 관계에 주목한다. kernel을 알면 한 열을 다른 열들로 어떻게 표현할 수 있는지를 알 수 있다. [1편](01-linear-systems-and-matrices.md)에서 $c_3 = 8c_1 + 2c_2$ 같은 관계로 동차해를 구한 것이 이 내용이다.

### Rank-nullity theorem

vector space $V, W$와 linear mapping $\Phi : V \to W$에 대해 다음이 성립한다.

$$
\dim(\ker(\Phi)) + \dim(\text{Im}(\Phi)) = \dim(V)
$$

fundamental theorem of linear mappings라고도 부른다. 이 식에서 바로 나오는 결과는 다음과 같다.

- $\dim(\text{Im}(\Phi)) < \dim(V)$이면 $\ker(\Phi)$는 자명하지 않다. kernel이 영벡터 말고 다른 vector도 포함하며 $\dim(\ker(\Phi)) \ge 1$이다.
- 이때 transformation matrix $A_\Phi$에 대해 $A_\Phi x = \mathbf{0}$은 무한히 많은 해를 가진다. rank가 $n$보다 작다는 것과 같은 말이다.
- $\dim(V) = \dim(W)$이면 다음 세 가지가 동치다. $\text{Im}(\Phi) \subseteq W$이기 때문이다.

$$
\Phi \text{ is injective} \iff \Phi \text{ is surjective} \iff \Phi \text{ is bijective}
$$

## 2.8 아핀 공간

affine space는 원점에서 떨어져 있는 공간으로, 일반적으로 vector subspace가 아니다. affine space 사이의 mapping은 linear mapping과 매우 비슷하다. 쉽게 생각하면, 어느 점이 원점이었는지 잊어버린 채 남겨진 vector space가 affine space다.

### 2.8.1 Affine subspace

vector space $V$, 점 $x_0 \in V$, subspace $U \subseteq V$가 있을 때 다음 집합을 $V$의 affine subspace 또는 linear manifold라 한다.

$$
L = x_0 + U := \{x_0 + u : u \in U\} = \{v \in V \mid \exists u \in U : v = x_0 + u\} \subseteq V
$$

$U$는 direction 또는 direction space, $x_0$는 support point라 한다. $x_0 \notin U$이면 affine subspace는 $\mathbf{0}$을 포함하지 않는다. $L$은 $U$를 $x_0$만큼 평행이동한 것인데, $x_0$가 $U$ 안에 있지 않으면 평행이동한 결과가 원점에서 벗어나기 때문이다. 이 경우 $L$은 vector subspace가 아니다.

두 affine subspace $L = x_0 + U$와 $\tilde{L} = \tilde{x}_0 + \tilde{U}$가 $V$ 안에 있을 때, $L \subseteq \tilde{L}$인 것은 $U \subseteq \tilde{U}$이고 $x_0 - \tilde{x}_0 \in \tilde{U}$인 것과 동치다.

affine subspace는 parameter로 표현할 수 있다. $k$차원 affine subspace $L = x_0 + U$에서 $(b_1, \dots, b_k)$가 $U$의 ordered basis이면, 모든 $x \in L$은 다음과 같이 유일하게 표현된다. 이를 parametric equation이라 한다.

$$
x = x_0 + \lambda_1b_1 + \cdots + \lambda_kb_k, \quad \lambda_1, \dots, \lambda_k \in \mathbb{R}
$$

읽으면서 스스로 정리한 이미지는 다음과 같다.

- subspace는 ($\{\mathbf{0}\}$을 제외하면) 닫힌 구역일 수 없고 반드시 무한히 뻗어 나가는 영역이다.
- affine subspace도 마찬가지다. 특정한 닫힌 구역이 아니라 무한히 뻗어 나가는 영역이고, 그 차원은 direction space $U$의 차원과 같다.
- 다만 그 위치가 상위 차원 공간 속에 놓인, 원점을 지나지 않을 수도 있는 저차원 공간일 뿐이다.

차원별로 이름이 붙어 있다.

- **Line**: 1차원 affine subspace. $y = x_0 + \lambda b_1$로 쓰며 $\lambda \in \mathbb{R}$, $U = \text{span}[b_1] \subseteq \mathbb{R}^n$는 1차원 subspace다. 즉 line은 support point $x_0$와 방향을 정하는 vector $b_1$으로 정의된다.
- **Plane**: 2차원 affine subspace. $y = x_0 + \lambda_1b_1 + \lambda_2b_2$로 쓰며 $\lambda_1, \lambda_2 \in \mathbb{R}$, $U = \text{span}[b_1, b_2] \subseteq \mathbb{R}^n$이다. 즉 plane은 support point $x_0$와, direction space를 span하는 선형 독립인 두 vector $b_1, b_2$로 정의된다.
- **Hyperplane**: $\mathbb{R}^n$의 $(n-1)$차원 affine subspace. $y = x_0 + \sum_{i=1}^{n-1} \lambda_ib_i$로 쓰며 $b_1, \dots, b_{n-1}$은 $\mathbb{R}^n$의 $(n-1)$차원 subspace $U$의 basis다. 즉 hyperplane은 support point $x_0$와, direction space를 span하는 선형 독립인 $n-1$개의 vector로 정의된다. $\mathbb{R}^2$에서는 line이, $\mathbb{R}^3$에서는 plane이 hyperplane이다.

### 연립선형방정식의 해집합과 affine subspace

[2편](02-vector-spaces-and-basis.md)에서 비동차 시스템의 해집합은 subspace가 아니라고 했다. 그 해집합이 바로 affine subspace다.

- $A \in \mathbb{R}^{m \times n}$, $x \in \mathbb{R}^m$일 때 비동차 시스템 $A\lambda = x$의 해집합은 공집합이거나, 차원이 $n - \text{rk}(A)$인 $\mathbb{R}^n$의 affine subspace다.
- 특히 방정식이 하나뿐인 경우, 즉 계수가 모두 0은 아닌 선형 방정식 $a_1\lambda_1 + \cdots + a_n\lambda_n = x$의 해집합은 $\mathbb{R}^n$의 hyperplane이다.
- 반대 방향도 성립한다. $\mathbb{R}^n$의 모든 $k$차원 affine subspace는 어떤 연립선형방정식 $Ax = b$ ($A \in \mathbb{R}^{m \times n}$, $b \in \mathbb{R}^m$)의 해집합이다. 예를 들어 3차원 공간에서 원점을 지나지 않는 평면은 $Ax = b$ 꼴의 해집합이다.

중요한 것은 이 해집합의 차원 $k$, 즉 free variable의 수가 다음 조건으로 정해진다는 점이다.

$$
\text{rk}(A) = n - k \quad\Longleftrightarrow\quad n = \text{rk}(A) + k
$$

- $n$: 전체 변수의 수
- $\text{rk}(A)$: 방정식들이 독립적으로 제약을 거는 차원의 수
- $k = n - \text{rk}(A)$: 제약을 받지 않고 자유롭게 고를 수 있는 나머지. 해집합의 차원이자 affine subspace의 차원이다.

선형 방정식이 rank만큼 제약을 걸면 나머지만큼은 자유롭게 움직일 수 있다는 뜻이다. rank가 작아질수록 자유도가 커지고 해집합의 차원이 높아진다. 앞에서 본 rank-nullity theorem의 "변수의 수 $n$ = rank + nullity"와 같은 식이다.

반대로 동차 시스템 $Ax = \mathbf{0}$이면 해집합이 항상 $\mathbf{0}$을 포함하므로 vector space가 된다. vector subspace도 affine subspace의 일종이며, 원점을 지나는 특별한 경우일 뿐이다.

### 2.8.2 Affine mapping

vector space 사이의 linear mapping과 비슷하게, 두 affine space 사이의 affine mapping을 정의할 수 있다. vector space $V, W$와 linear mapping $\Phi : V \to W$, 그리고 $a \in W$가 있을 때 다음 mapping을 $V$에서 $W$로의 affine mapping이라 한다.

$$
\begin{aligned}
\phi : V &\to W \\
x &\mapsto a + \Phi(x)
\end{aligned}
$$

vector $a$를 $\phi$의 translation vector라 한다.

- 모든 affine mapping $\phi : V \to W$는 linear mapping $\Phi : V \to W$와 $W$ 안에서의 평행이동 $\tau : W \to W$의 합성 $\phi = \tau \circ \Phi$로 쓸 수 있다.
- affine mapping $\phi : V \to W$와 $\phi' : W \to X$를 이어서 적용한 합성 $\phi' \circ \phi : V \to X$도 affine mapping이다.
- affine mapping이 bijective하면 차원이나 평행 관계 같은 기존의 기하학적 구조를 그대로 유지한다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. 차원이 같은 vector space끼리는 선형 변환으로 손실 없이 옮겨 갈 수 있다는 것을, 신경망에 activation function이 필요한 이유로 볼 수 있는가?**

직접적인 이유는 그 대목이 아니라 같은 절에 있는 다른 성질이다. Linear mapping의 합성은 다시 linear mapping이다. Activation이 없는 층은 affine mapping $x \mapsto Wx + b$이고, 두 층을 이으면

$$
W_2(W_1 x + b_1) + b_2 = (W_2 W_1)\,x + (W_2 b_1 + b_2)
$$

로 다시 affine mapping 하나가 된다. 2.8.2에서 본 "affine mapping의 합성은 affine mapping"이 이것이다. 층을 몇 개 쌓든 표현할 수 있는 함수는 층 하나짜리와 같으므로, 깊이가 의미를 가지려면 층 사이에 비선형 함수가 있어야 한다. 차원이 같은 공간이 isomorphic하다는 대목은 이 이야기를 다른 쪽에서 보여 준다. 되돌릴 수 있는 선형 층은 같은 정보를 다른 basis의 좌표로 다시 적는 것일 뿐이어서 점들의 배치를 구부리거나 접지 못한다. 한 직선으로 나눌 수 없는 데이터는 선형 변환을 몇 번 거쳐도 여전히 한 직선으로 나눌 수 없다.

**Q. free variable의 수가 왜 해집합의 차원인가?**

Free variable 하나마다 만든 동차해들이 null space의 basis가 되기 때문이다. 실마리로 적어 둔 내용이 그대로 증명이 된다. $A \in \mathbb{R}^{m \times n}$를 reduced row echelon form으로 바꾸면 pivot 열이 $r = \text{rk}(A)$개, free variable이 $n - r$개다. Free variable 하나를 1로, 나머지 free variable을 0으로 두고 pivot 변수를 풀면 동차해가 하나 나온다. 이렇게 만든 $n - r$개의 해를 $s_1, \dots, s_{n-r}$이라 하자.

- **선형 독립이다.** Free variable에 해당하는 좌표만 보면 $s_j$는 $j$번째 자리가 1이고 나머지는 0이다. 그 좌표들만 모으면 단위 행렬이 되므로, 선형 결합이 $\mathbf{0}$이 되려면 계수가 모두 0이어야 한다.
- **해 전체를 span한다.** 임의의 해 $x$에 대해 $x' = \sum_j x_{f_j} s_j$를 만든다($x_{f_j}$는 $x$의 $j$번째 free variable 값). $x - x'$도 $Ax = \mathbf{0}$의 해이고 free variable 좌표가 모두 0이다. Pivot 변수는 free variable의 값으로 완전히 정해지므로 $x - x' = \mathbf{0}$, 곧 $x = x'$다.

따라서 $\{s_1, \dots, s_{n-r}\}$은 $\ker(A)$의 basis이고 $\dim(\ker(A)) = n - r$이다. 이것은 rank-nullity theorem을 행렬에 대해 증명한 것이기도 하다. 비동차 시스템 $Ax = b$에 해가 있으면 해집합은 특수해 하나에 $\ker(A)$를 더한 $x_p + \ker(A)$다. Direction space가 $\ker(A)$인 affine subspace이므로 차원은 마찬가지로 $n - r$, 곧 free variable의 수다.

## 참고 자료

- M. P. Deisenroth, A. A. Faisal, C. S. Ong, "Mathematics for Machine Learning", Cambridge University Press, 2020. 2.7 ~ 2.8절. <https://mml-book.github.io/>
