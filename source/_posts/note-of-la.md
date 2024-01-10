---
title: 线性代数（高等代数、抽象代数）学习笔记
date: 2023-12-28 00:38:01
mathjax: true
category:
    - 【学习笔记】数学
---

大一学的《线性代数》《高等线性代数选讲》，学习 AI 基础的时候零零散散接触的线性代数和矩阵操作，密码学、离散数学、组合数学里零零散散提到的抽象代数，以及博一下学期即将选的《应用近世代数》，这些东西感觉都在讨论很多很类似的东西，但我却一直没有时间把这些东西凝练一下。现在恰好有一点空，不如来把所有的东西做个整理，入门一下代数学。

<!-- more -->

$$
\newcommand{\lv}{\begin{bmatrix}}
\newcommand{\rv}{\end{bmatrix}}
\newcommand{\b}{\boldsymbol}
$$

# 向量与矩阵

## 基本定义与运算

我们先从我们最熟悉的空间开始认识线性代数，这一部分大多在高中就会涉及，所以只是稍微简单写写。

{% note success no-icon %}
**Definition 1.01 (Vector)** 我们记：

$$
\mathbb{R}^n := \left\{\lv x_1 \\ x_2 \\ \vdots \\ x_n \rv\middle| x_k \in \mathbb{R}, 1 \leq k \leq n\right\}
$$

$\mathbb{R}^n$ 中的元素 $\b x \in \mathbb{R}^n$ 称为 **$n$ 维实向量**。不引起混淆时，可以直接简称**向量**。
{% endnote %}

上述定义的向量一般称为**列向量**，当然与之对应的还可以定义**行向量**。不过这两个定义并没有本质区别，不难发现采用行还是列定义向量并不影响接下来提到的运算。

向量的运算定义是自然的，向量的加法与数乘均可方便地如下定义：

$$
\lv x_1 \\ x_2 \\ \vdots \\ x_n \rv + \lv y_1 \\ y_2 \\ \vdots \\ y_n \rv := \lv x_1 + y_1 \\ x_2 + y_2 \\ \vdots \\ x_n + y_n \rv, \lambda\lv x_1 \\ x_2 \\ \vdots \\ x_n \rv := \lv \lambda x_1 \\ \lambda x_2 \\ \vdots \\ \lambda x_n \rv
$$

在定义了加法和数乘的基础上，我们可以定义向量的线性组合。

{% note success no-icon %}
**Definition 1.02 (Linear Combination)** 给定 $n$ 维实向量 $\b x_1, \b x_2, \cdots, \b x_m$ 以及实数 $\lambda_1, \lambda_2, \cdots, \lambda_m$，我们称：

$$
\b x := \sum_{k = 1}^m \lambda_k \b x_k
$$

为向量 $\b x_1, \b x_2, \cdots, \b x_m$ 的一个**线性组合**。
{% endnote %}

为了衡量向量的长度、夹角等特性，我们需要定义向量的内积。

{% note success no-icon %}
**Definition 1.03 (Inner Product)** 给定 $n$ 维实向量 $\b x, \b y \in \mathbb{R}^n$，我们称：

$$
\b x \cdot \b y := \sum_{k = 1}^n x_k y_k
$$

为向量 $\b x, \b y$ 的**内积**。

定义 $\b x \cdot \b y = 0$ 时称 $\b x, \b y$ **正交**，记作 $\b x \bot \b y$。

不难发现，内积满足 $\b x \cdot \b x \geq 0$。在此基础上，定义 $\|\b x\| := \sqrt{\b x \cdot \b x}$ 为向量 $\b x$ 的**长度**。长度为 $1$ 的向量称为**单位向量**。
{% endnote %}

现在即可引入矩阵这一概念。

{% note success no-icon %}
**Definition 1.04 (Matrix)** 我们记：

$$
M_{m \times n}(\mathbb{R}) := \left\{\lv a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \rv\middle| a_{ij} \in \mathbb{R}, 1 \leq i \leq m, 1 \leq j \leq n\right\}
$$

$M_{m \times n}(\mathbb{R})$ 中的元素 $A \in M_{m \times n}(\mathbb{R})$ 称为 **$m \times n$ 的实矩阵**。不引起混淆时，可以直接简称**矩阵**。

特别地，记 $M_n(\mathbb{R}) := M_{n \times n}(\mathbb{R})$，其中的矩阵称为 **$n$ 阶实方阵**。不引起混淆时，可以直接简称**方阵**。

可以注意到 $\mathbb{R}^n = M_{n \times 1}(\mathbb{R})$。

为了方便讨论，记号 $A = (a_{ij})_{m \times n}$ 的含义为，$A$ 是一个 $m \times n$ 的实矩阵，且 $a_{ij}$ 是 $A$ 的第 $i$ 行第 $j$ 列的元素。
{% endnote %}

矩阵的加法可以简单定义，矩阵加法要求两个矩阵尺寸一致：

$$
\lv a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \rv + \lv b_{11} & b_{12} & \cdots & b_{1n} \\ b_{21} & b_{22} & \cdots & b_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ b_{m1} & b_{m2} & \cdots & b_{mn} \rv := \lv a_{11} + b_{11} & a_{12} + b_{12} & \cdots & a_{1n} + b_{1n} \\ a_{21} + b_{21} & a_{22} + b_{22} & \cdots & a_{2n} + b_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} + b_{m1} & a_{m2} + b_{m2} & \cdots & a_{mn} + b_{mn} \rv
$$

可以发现向量加法是矩阵加法的特例，并且不难验证矩阵加法满足结合律和交换律。

矩阵之间的乘法定义则相对复杂，矩阵乘法要求前面的矩阵的列数与后面的矩阵的行数相同：

$$
\lv a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \rv \lv b_{11} & b_{12} & \cdots & b_{1p} \\ b_{21} & b_{22} & \cdots & b_{2p} \\ \vdots & \vdots & \ddots & \vdots \\ b_{n1} & b_{n2} & \cdots & b_{np} \rv := \lv c_{11} & c_{12} & \cdots & c_{1p} \\ c_{21} & c_{22} & \cdots & c_{2p} \\ \vdots & \vdots & \ddots & \vdots \\ c_{m1} & c_{m2} & \cdots & c_{mp} \rv
$$

其中：

$$
c_{ij} := \sum_{k = 1}^n a_{ik} b_{kj}, 1 \leq i \leq m, 1 \leq j \leq p
$$

不难验证矩阵乘法满足结合律和对加法的分配律，但不满足交换律。

矩阵另有一个运算称为**转置**，其定义是直观的：

$$
\lv a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \rv^T := \lv a_{11} & a_{21} & \cdots & a_{m1} \\ a_{12} & a_{22} & \cdots & a_{m2} \\ \vdots & \vdots & \ddots & \vdots \\ a_{1n} & a_{2n} & \cdots & a_{mn} \rv
$$

不难验证矩阵的转置和乘法满足 $(AB)^T = B^TA^T$。

矩阵的转置引出了下述直观的定义。

{% note success no-icon %}
**Definition 1.05 (Symmetric Matrix)** 若方阵 $A \in M_n(\mathbb{R})$ 满足 $A^T = A$，我们称之为**对称矩阵**。若 $A^T = -A$，我们称之为**反对称矩阵**。
{% endnote %}

在定义了转置的基础上，我们很容易地发现内积实际上满足：

$$
\b x \cdot \b y = \b x^T \b y, \b x, \b y \in \mathbb{R}^n
$$

在此之后我们均使用 $\b x^T\b y$ 表示向量内积，而仅在必要的条件下使用 $\b x \cdot \b y$ 写法。

## 初等行变换与矩阵的秩

首先给出单位方阵的定义。

{% note success no-icon %}
**Definition 1.06 (Identity matrix)** 称 $I_n = (e_{ij})_{n \times n} \in M_n(\mathbb{R})$ 为**单位方阵**，若：

$$
e_{ij} = \begin{cases} 1 & i = j \\ 0 & i \neq j \end{cases}
$$
{% endnote %}

我们将下面三种操作称为**初等行变换**：

- 交换第 $i$ 行与第 $j$ 行
- 用 $a$ 乘第 $i$ 行，其中 $a \neq 0$
- 用 $a$ 乘第 $i$ 行并加到第 $j$ 行上

我们分别定义矩阵 $P_{ij}, D_i(a), E_{ij}(a) \in M_n(\mathbb{R})$ 为对单位方阵 $I_n$ 作上述三种初等行变换得到的矩阵。这些矩阵称为**初等方阵**。

不难验证，对矩阵 $A \in M_{n \times m}(\mathbb{R})$ 作上述三种初等行变换后得到的矩阵分别为 $P_{ij}A, D_i(a)A, E_{ij}(a)A$。

我们有下述定理。

{% note info no-icon %}
**Theorem 1.01** 在 $M_{n \times m}(\mathbb{R})$ 上定义二元关系 $\mathop{\sim}\limits^{\rm R}$，称为**行等价**。若 $A$ 能够通过有限次初等行变换得到 $B$，则称 $A \mathop{\sim}\limits^{\rm R} B$。行等价关系是 $M_{n \times m}(\mathbb{R})$ 上的等价关系。
{% endnote %}

定理的证明是简单的，简单验证其自反性、对称性、传递性即可。

在上述等价关系的基础上，我们即可考虑该等价关系的等价类以及等价类之中的代表元。我们作如下定义。

{% note success no-icon %}
**Definition 1.07 (Reduced Row Echelon Form)** 称矩阵 $A \in M_{m \times n}(\mathbb{R})$ 是**最简行阶梯形**，如果：

1. 全零行总位于有非零元素的下方
2. 每个非零行的第一个非零元素为 $1$，称为该行的**主元**
3. 位于上方的行的主元总比位于下方的行的主元靠左
4. 每个主元所在的列的其余元素均为 $0$

例如：

$$
\lv \b 1 & 2 & 0 & 3 & 0 \\ 0 & 0 & \b 1 & 2 & 0 \\ 0 & 0 & 0 & 0 & \b 1 \\ 0 & 0 & 0 & 0 & 0 \rv
$$

是一个 $4 \times 5$ 的最简行阶梯形。
{% endnote %}

在上述定义的基础上，我们有下述定理。

{% note info no-icon %}
**Theorem 1.02** 对矩阵 $A \in M_{m \times n}(\mathbb{R})$，存在唯一的最简行阶梯形 $B$ 使得 $A \mathop{\sim}\limits^{\rm R} B$。
{% endnote %}

存在性的证明可以通过构造化简算法证明。该化简算法的基本逻辑为：

1. 由最左的非零列开始，取该列中的一个非零元素
2. 通过对换变换将该元素所在行移动到第一行
3. 通过倍加变换将主元下面的元素变为 $0$
4. 将剩下的行看做一个子矩阵，在其上应用以上三个步骤，直到没有非零行为止
5. 由最右边的主元开始，把每个主元上方的各元素变为 $0$，并通过倍乘变换将该主元变成 $1$

上述算法被称为 **Gauss-Jordan 消元法**。一个例子如下：

$$
\lv 0 & 1 & 2 & 3 \\ \b 1 & 2 & 3 & 4 \\ 2 & 3 & 4 & 5 \rv \mathop{\sim}^{\rm R} \lv \b 1 & 2 & 3 & 4 \\ 0 & 1 & 2 & 3 \\ 2 & 3 & 4 & 5 \rv \mathop{\sim}^{\rm R} \lv 1 & 2 & 3 & 4 \\ 0 & \b 1 & 2 & 3 \\ 0 & -1 & -2 & -3 \rv \mathop{\sim}^{\rm R} \lv \b 1 & 2 & 3 & 4 \\ 0 & \b 1 & 2 & 3 \\ 0 & 0 & 0 & 0 \rv \mathop{\sim}^{\rm R} \lv \b 1 & 0 & -1 & -2 \\ 0 & \b 1 & 2 & 3 \\ 0 & 0 & 0 & 0 \rv
$$

接下来证明唯一性。如果矩阵 $A$ 能够通过两条不同的路径利用有限次初等行变换得到不同的最简行阶梯形 $B = (b_{ij})_{m \times n}, C = (c_{ij})_{m \times n}$，显然有 $B \mathop{\sim}\limits^{\rm R} C$，我们首先需要证明 $B$ 与 $C$ 的主元位置相同。不妨假设 $B, C$ 的行向量分别为 $\{\b b_k^T\}_{1 \leq k \leq m}, \{\b c_k^T\}_{1 \leq k \leq m}$。

我们不难发现，无论如何作初等行变换，得到的矩阵的行向量必然是原矩阵行向量的某个线性组合。也就是说，由于 $B \mathop{\sim}\limits^{\rm R} C$，所以任意 $\b c_p^T$ 均是 $\{\b b_k^T\}_{1 \leq k \leq m}$ 的线性组合。

取某个 $\b c_p^T$，其满足 $c_{pq} = 1$ 以及对任意 $j < q$ 有 $c_{pj} = 0$。由于 $\b c_p^T$ 是 $\{\b b_k^T\}_{1 \leq k \leq m}$ 的线性组合，所以存在 $\lambda_1, \lambda_2, \cdots, \lambda_m \in \mathbb{R}$ 使得：

$$
\sum_{k = 1}^m \lambda_k \b b_k^T = \b c_p^T \iff \sum_{k = 1}^m \lambda_k b_{kj} = c_{pj}
$$

使用反证法，如果 $q$ 不是 $B$ 的某个主元所在列的编号，记 $B$ 的各非零行主元所在列的编号从小至大为 $q_1, q_2, \cdots$，那么存在某个正整数 $r$ 使得则对于 $k < r$，有 $q_k < q$，而对于 $k \geq r$，有 $q_k > q$。

我们依次令 $j = q_1, q_2, \cdots, q_{r - 1}$，应当可以依次得到 $\lambda_1, \lambda_2, \cdots, \lambda_{r - 1} = 0$。这里我们仅取 $j = q_1$ 作分析，其余情况类似。此时因为 $b_{1q_1} = 1$（主元的要求）以及 $b_{kq_1} = 0, k > 1$（位于下面的行的主元在位于上面的行的主元的右侧），我们有：

$$
\sum_{k = 1}^m \lambda_k b_{kq_1} = \lambda_1 = 0
$$

现在我们考虑 $j = q$，此时 $b_{kq} = 0, k \geq r$（主元的左侧全为零元素），那么：

$$
\sum_{k = 1}^m \lambda_k b_{kq} = \sum_{k = r}^m \lambda_k b_{kq} = 0 \neq 1 = c_{pq}
$$

因此我们得到了矛盾。所以 $q$ 必然是 $B$ 的某个主元所在列的编号。由于 $p$ 的任意性以及 $B, C$ 的等价性，我们得到了 $B$ 与 $C$ 的主元位置相同，也就是说 $B, C$ 的主元所在列完全相同。

如果 $B, C$ 的某个对应行不同，不妨令 $\b b_p^T \neq \b c_p^T$。首先显然可知这一行必然是非零行（全零行自然相同），并且从左至右第一个不等的元素必然在主元之后（主元之前都是零元素），并且不在其他主元列上（其他主元列上全是零元素）。由于 $\b c_p^T$ 是 $\{\b b_k^T\}_{1 \leq k \leq m}$ 的线性组合，那么 $\b c_p^T - \b b_p^T$ 也是其线性组合。而 $\b c_p^T - \b b_p^T$ 显然是一个第一个非零元不在主元列上的行向量，沿用类似上述的证明方式即可说明得到矛盾。所以 $B, C$ 的所有行完全相同，因此证明完毕。$\blacksquare$

在上述定理的基础上，我们将矩阵 $A$ 通过有限次初等行变换得到的最简行阶梯形记为 ${\rm rref}(A)$，该定义是良定义。从而，我们给出矩阵秩的定义。

{% note success no-icon %}
**Definition 1.08 (Rank)** 若矩阵 $A$ 是最简行阶梯形，定义其**秩**为其主元的个数，记为 ${\rm rank}(A)$。若 $A$ 不是最简行阶梯形，定义其秩为 ${\rm rank}(A) := {\rm rank}({\rm rref}(A))$。
{% endnote %}

## 线性方程组的解集

对已知的矩阵 $A \in M_{m \times n}(\mathbb{R})$ 以及向量 $\b b \in \mathbb{R}^m$，我们称 $A\b x = \b b$ 为一个**线性方程组**。接下来我们尝试分析线性方程组解集的结构。

若我们已经获得了 $A\b x = \b b$ 的一个解 $\b x_0$，此时如果我们记 $A\b x = \b 0$ 的解集为 $N$，就可以发现：

$$
A\b x = \b b \iff A(\b x - \b x_0) = \b 0 \iff \b x - \b x_0 \in N
$$

这就说明了我们仅需要去研究 $A\b x = \b 0$ 的解集结构即可。这里，我们将 $A\b x = \b 0$ 称为 $A$ 的**齐次线性方程组**。显然任何齐次线性方程组都有平凡解 $\b x = \b 0$。

令 $R := {\rm rref}(A)$，则存在一列初等方阵 $E_1, E_2, \cdots, E_n$ 满足 $R = E_nE_{n - 1} \cdots E_1A$。将这一列初等方阵左乘于原线性方程组（这里第一步的等价性依赖于“所有初等行变换都可以逆推回去”，严谨而言的原因是所有初等方阵均可逆，关于可逆性的定义和深入讨论我们后续进行）：

$$
A\b x = \b 0 \iff E_nE_{n - 1} \cdots E_1A\b x = \b 0 \iff R\b x = \b 0
$$

也就是说，我们只需要考虑最简行阶梯形的齐次线性方程组即可，而是容易的。举例而言，下述线性方程组：

$$
\lv 1 & 0 & -1 & -2 \\ 0 & 1 & 2 & 3 \\ 0 & 0 & 0 & 0 \rv\lv x_1 \\ x_2 \\ x_3 \\ x_4 \rv = \lv 0 \\ 0 \\ 0 \rv \iff \begin{cases}
x_1 - x_3 - 2x_4 = 0 \\
x_2 + 2x_3 + 3x_4 = 0 \\
\end{cases} \iff
\begin{cases}
x_1 = x_3 + 2x_4 \\
x_2 = -2x_3 - 3x_4 \\
\end{cases}
$$

我们不难发现，展开后每一个方程中至多包含一个主元所对应的分量（如 $x_1, x_2$）。因而我们发现，我们可以任取除了主元对应分量外的其他所有分量，并且据此确定主元对应分量的值。从而，我们已经求取出了 $R\b x = \b 0$ 也就是 $A\b x = \b 0$ 的解集。

下面我们分析解集的特征，不难得到如下的定理。

{% note info no-icon %}
**Theorem 1.03(1)** 记 $A \in M_{m \times n}(\mathbb{R})$ 的秩为 $r := {\rm rank}(A)$。

- $r < n$ 等价于 $A\b x = \b 0$ 除了平凡解 $\b x = \b 0$ 外其他的非平凡解
- $r = n$ 等价于 $A\b x = \b 0$ 仅有平凡解 $\b x = \b 0$，此时称矩阵 $A$ **列满秩**
{% endnote %}

该定理的证明可以通过简单分析 $R$ 是否有除了主元列之外的其他列说明。

现在回到一般的线性方程组 $A\b x = \b b$。依然记 $R = {\rm rref}(A) = E_nE_{n - 1} \cdots E_1A$，不难得到原方程组等价于：

$$
R\b x = E_nE_{n - 1} \cdots E_1\b b
$$

事实上我们不难得到下述定理。

{% note info no-icon %}
**Theorem 1.03(2)** 记 $A \in M_{m \times n}(\mathbb{R})$ 的秩为 $r := {\rm rank}(A)$。

- $r < m$ 等价于存在 $\b b$ 使得 $A\b x = \b b$ 无解
- $r = m$ 等价于 $A\b x = \b b$ 总有解，此时称矩阵 $A$ **行满秩**
{% endnote %}

该定理的证明可以通过简单分析 $R$ 是否有全零行说明。

对于方阵，其满足行满秩时自然满足列满秩，此时我们称该方阵**满秩**。

综合上述定理，我们其实可以发现，$r$ 与 $m$ 的关系控制了线性方程组是否有无解的可能，而 $r$ 与 $n$ 的关系则控制了线性方程组是否有无穷组解。

## 矩阵可逆性

矩阵的可逆性如下定义。

{% note success no-icon %}
**Definition 1.09 (Inverse Matrix)** 对于给定的矩阵 $A \in M_{m \times n}(\mathbb{R})$。如果存在矩阵 $B \in M_{n \times m}(\mathbb{R})$ 满足 $BA = I_n$，则称 $A$ 有**左逆** $B$。如果存在矩阵 $C \in M_{n \times m}(\mathbb{R})$ 满足 $AC = I_m$，则称 $A$ 有**右逆** $C$。

对于方阵 $A \in M_n(\mathbb{R})$，在左逆和右逆的基础上，另定义，若存在方阵 $B$ 使得 $AB = BA = I_n$，则称 $A$ **可逆**，记 $B = A^{-1}$ 为 $A$ 的**逆矩阵**。
{% endnote %}

对于方阵，$A^{-1}$ 符号良定义的原因在于，如果方阵 $A$ 可逆，则逆矩阵唯一。这可以通过反证法说明，如果可逆方阵 $A$ 存在两个逆矩阵 $B, C$，则：

$$
B = BI_n = B(AC) = (BA)C = I_nC = C
$$

现在我们讨论一般矩阵的可逆性，我们有下述定理。

{% note info no-icon %}
**Theorem 1.04** 矩阵 $A$ 存在右逆等价于其行满秩，存在左逆等价于其列满秩。特别地，方阵 $A$ 可逆等价于其满秩。
{% endnote %}

接下来我们给出证明。

分析右逆的存在性可以利用线性方程组的解集性质。若矩阵 $A$ 存在右逆 $C$，那么对于任意 $\b b \in \mathbb{R}^m$，都有 $A(C\b b) = \b b$。也就是说，$A\b x = \b b$ 总有解，由 Theorem 1.03(2) 可得 $A$ 行满秩。

另一方面，若 $A$ 行满秩，那么 $A\b x = \b b$ 总有解。取 $I_m$ 的各列向量 $\b e_1, \b e_2, \cdots, \b e_m$，令 $\b c_k$ 为 $A\b x = \b e_k$ 的一个解，则 $C = (\b c_1, \b c_2, \cdots, \b c_m)$ 满足 $AC = I_m$，即 $A$ 存在右逆 $C$。

分析右逆的存在性也可以利用线性方程组的解集性质。如果矩阵 $A$ 存在左逆 $B$，对齐次线性方程组 $A\b x = \b 0$ 左乘 $B$，即可得到该方程组仅有平凡解 $\b x = \b 0$，由 Theorem 1.03(1) 可得 $A$ 列满秩。

另一方面，若 $A$ 列满秩，为了证明 $A$ 存在左逆，我们首先证明引理。

{% note warning no-icon %}
**Lemma 1.01** $A^TA\b x = \b 0 \iff A\b x = \b 0$
{% endnote %}

$\Leftarrow$ 是显然的，而 $\Rightarrow$ 可以通过 $\b x^TA^TA\b x = 0 \iff (A\b x)^T(A\b x) = 0 \iff A\b x = \b 0$ 证明。

在引理基础上，由于 $A$ 列满秩，所以 $A\b x = \b 0$ 仅有平凡解，从而 $A^TA\b x = \b 0$ 也仅有平凡解，即 $A^TA$ 列满秩。然而 $A^TA$ 是方阵，所以同时行满秩，存在右逆 $C$ 使得 $(A^TA)C = I_n$。两边取转置可得 $(C^TA^T)A = I_n$，可见 $A$ 存在左逆 $B = C^TA^T$。$\blacksquare$

如果观察上述证明，可以发现明明看似对称的左逆和右逆，我们的证明却显得缺乏对称性。这是因为，我们采用了列向量的定义，从而让齐次线性方程组的未知数自然地位于矩阵右侧。也正因此，我们也自然地采用了行变换来化简矩阵，而非列变换。

上述定理还可以得到一个自然的推论，即方阵存在单侧逆等价于其可逆。

# 线性空间

## 矩阵诱导的线性空间

我们首先定义线性空间。

{% note success no-icon %}
**Definition 1.10 (Linear Space)** 在集合 $S$ 上定义加法 $+: S^2 \to S$ 与数乘 $\cdot: \mathbb{R} \times S \to S$，如果：

- （加法交换律）对于任意 $\b x, \b y \in S$，有 $\b x + \b y = \b y + \b x$
- （加法结合律）对于任意 $\b x, \b y, \b z \in S$，有 $(\b x + \b y) + \b z = \b x + (\b y + \b z)$
- （加法单位元）存在 $\b 0 \in S$ 使得对于任意 $\b x \in S$，有 $\b x + \b 0 = \b x$
- （加法逆元）对于任意 $\b x \in S$，存在 $\b y \in S$ 使得 $\b x + \b y = \b 0$
- （数乘单位元）对于任意 $\b x \in S$，有 $1 \cdot \b x = \b x$
- （数乘结合律）对于任意 $\b x, \b y \in S$，有 $a(b\b x) = (ab)\b x$
- （数乘对实数加法分配）对于任意 $\b x, \b y \in S$，有 $(a + b)\b x = a\b x + b\b x$
- （数乘对向量加法分配）对于任意 $\b x \in S$，有 $a(\b x + \b y) = a\b x + a\b y$

则称 $S$ 是一个**线性空间**。

此外，对于线性空间 $S$ 的一个子集 $U \subseteq S$，如果 $U$ 对向量加法和数乘封闭，则 $U$ 称为 $S$ 的**子空间**。不难发现对任意线性空间 $S$，其子集 $\{\b 0\}, S$ 都是其子空间，这两个子空间称为 $S$ 的**平凡子空间**。
{% endnote %}

此外，我们可以在线性空间 $S$ 上定义**张成运算**，不难说明线性空间 $S$ 上若干个向量所张成的空间为 $S$ 的子空间：

$$
{\rm Span}(\b x_1, \b x_2, \cdots, \b x_n) := \left\{\sum_{i = 1}^n \lambda_i\b x_i \middle| \lambda_i \in \mathbb{R}, 1 \leq i \leq n\right\}, x_i \in S, 1 \leq i \leq n
$$

在此基础上，我们定义与矩阵相关线性空间。

{% note success no-icon %}
**Definition 1.11 (Column Space & Row Space)** 记矩阵 $A \in M_{m \times n}(\mathbb{R})$ 的列向量为 $\b a_1, \b a_2, \cdots, \b a_n$，则定义 $A$ 的**列空间**为：

$$
{\rm Col}(A) := {\rm Span}(\b a_1, \b a_2, \cdots, \b a_n)
$$

可见矩阵 $A$ 的列空间是 $\mathbb{R}^m$ 的子空间。

类似地，定义 $A$ 的**行空间**为 $A$ 的行向量张成的线性空间，即 ${\rm Row}(A) := {\rm Col}(A^T)$。可见矩阵 $A$ 的列空间是 $\mathbb{R}^n$ 的子空间。
{% endnote %}

我们先前仅仅讨论了齐次线性方程组解集的一些简单性质，事实上我们可以注意到这个解集必然是一个线性空间。这是因为 $A \in M_{m \times n}(\mathbb{R})$ 时 $A\b x = \b 0$ 的解集必然是线性空间 $\mathbb{R}^n$ 的子集，并且若 $\b x, \b y$ 是该解集的元素，不难验证 $\b x + \b y, \lambda\b x$ 也都是解集的元素，封闭性成立，所以该解集是 $\mathbb{R}^n$ 的子空间。

在此基础上，给出定义。

{% note success no-icon %}
**Definition 1.12 (Null Space)** 已知矩阵 $A \in M_{m \times n}(\mathbb{R})$，记 $A\b x = \b 0$ 的解集为 $N(A)$，由于 $N(A)$ 为 $\mathbb{R}^n$ 的子空间，故定义其为 $A$ 的**右零空间**或直接简称为**零空间**。

类似地，定义 $A^T\b x = \b 0$ 的解集为 $N(A^T)$，称为 $A$ 的**左零空间**，其为 $\mathbb{R}^m$ 的子空间。
{% endnote %}