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

可以发现向量数乘是矩阵乘法的特例，并且不难验证矩阵乘法满足结合律和对加法的分配律，但不满足交换律。

矩阵另有一个运算称为**转置**，其定义是直观的：

$$
\lv a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \rv^T := \lv a_{11} & a_{21} & \cdots & a_{m1} \\ a_{12} & a_{22} & \cdots & a_{m2} \\ \vdots & \vdots & \ddots & \vdots \\ a_{1n} & a_{2n} & \cdots & a_{mn} \rv
$$

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

一个例子如下：

$$
\lv 0 & 1 & 2 & 3 \\ \b 1 & 2 & 3 & 4 \\ 2 & 3 & 4 & 5 \rv \mathop{\sim}^{\rm R} \lv \b 1 & 2 & 3 & 4 \\ 0 & 1 & 2 & 3 \\ 2 & 3 & 4 & 5 \rv \mathop{\sim}^{\rm R} \lv 1 & 2 & 3 & 4 \\ 0 & \b 1 & 2 & 3 \\ 0 & -1 & -2 & -3 \rv \mathop{\sim}^{\rm R} \lv \b 1 & 2 & 3 & 4 \\ 0 & \b 1 & 2 & 3 \\ 0 & 0 & 0 & 0 \rv \mathop{\sim}^{\rm R} \lv \b 1 & 0 & -1 & -2 \\ 0 & \b 1 & 2 & 3 \\ 0 & 0 & 0 & 0 \rv
$$

接下来证明唯一性。如果矩阵 $A$ 能够通过两条不同的路径利用有限次初等行变换得到不同的最简行阶梯形 $B, C$，则 $B \mathop{\sim}\limits^{\rm R} C$，由定理 1 成立可知 $B = C$。

我们将矩阵 $A$ 通过有限次初等行变换得到的最简行阶梯形记为 ${\rm rref}(A)$。在此基础上，我们给出矩阵秩的定义。

{% note success no-icon %}
**Definition 1.07 (Rank)** 若矩阵 $A$ 是最简行阶梯形，定义其**秩**为其主元的个数，记为 ${\rm rank}(A)$。若 $A$ 不是最简行阶梯形，定义其秩为 ${\rm rank}(A) := {\rm rank}({\rm rref}(A))$。
{% endnote %}

## 方阵可逆性

方阵的可逆性如下定义。

{% note success no-icon %}
**Definition 1.06** 称 $I_n = (e_{ij})_{n \times n} \in M_n(\mathbb{R})$ 为**单位方阵**，若：

$$
e_{ij} = \begin{cases} 1 & i = j \\ 0 & i \neq j \end{cases}
$$

在此基础上，对于给定的矩阵 $A \in M_{m \times n}(\mathbb{R})$。如果存在矩阵 $B \in M_{n \times m}(\mathbb{R})$ 满足 $BA = I_n$，则称 $A$ 有**左逆** $B$。如果存在矩阵 $C \in M_{n \times m}(\mathbb{R})$ 满足 $AC = I_m$，则称 $A$ 有**右逆** $C$。

对于方阵 $A \in M_n(\mathbb{R})$，在左逆和右逆的基础上，另定义，若存在方阵 $B$ 使得 $AB = BA = I_n$，则称 $A$ **可逆**，记 $B = A^{-1}$ 为 $A$ 的**逆矩阵**。
{% endnote %}

上述定义中将方阵的左逆和右逆合并的合理性来源于下述定理。

{% note info no-icon %}
**Theorem 1.01** 对于方阵 $A \in M_n(\mathbb{R})$，下述命题等价：

1. $A$ 可逆
2. $A$ 有左逆
3. $A$ 有右逆
{% endnote %}

由命题 1 成立推出命题 2 与命题 3 成立是显然的。利用定义，我们可以从命题 2 成立推出命题 3 成立。

记方阵 $A = (a_{ij})_{n \times n}$ 的左逆为 $B = (b_{ij})_{n \times n}$，据左逆的定义：

$$
BA = I_n \iff \sum_{k = 1}^n b_{ik} a_{kj} = \begin{cases} 1 & i = j \\ 0 & i \neq j \end{cases}
$$

据此，我们计算乘积 $AB$，记方阵 $AB$ 第 $i$ 行第 $j$ 列的元素为 $c_{ij}$，则：

$$
c_{ij} = \sum_{k = 1}^n a_{ik} b_{kj} = \begin{cases} 1 & i = j \\ 0 & i \neq j \end{cases}
$$