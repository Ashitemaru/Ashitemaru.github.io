---
title: 《量子计算研讨课》学习笔记
date: 2021-08-16 01:11:21
mathjax: true
category:
    - 计算机科学
---

这门课是今年新开的一门课，根据目前下发的课件来看，应该是一门以数学为主的课。有句古话说得好，一切都是虚张声势的线性代数。

<!-- more -->

$$
\renewcommand{\vec}[1]{
    \left| #1 \right\rangle
}
$$

$$
\newcommand{\pro}[2]{
    \left\langle #1 \middle| #2 \right\rangle
}
$$

$$
\newcommand{\span}{
    \mathop{\rm span}
}
$$

上面出现的迷之空行是因为我在使用 `\newcommand` 命令修改 `mathjax` 。这里也顺便提一下，这个笔记的向量将会用竖线加尖括号的方式写出，如 $\vec{\varphi}$ 。内积则长成 $\pro{\varphi}{\varphi}$ 。共轭复数则在右上角加上星号表示。

# 线性代数基础

## 向量空间与内积空间

我们定义**复向量空间（ Complex vector space ）**。考虑一个非空的集合 $\mathcal{H}$ ，其上定义了两个运算：

- 向量加法， $+: \mathcal{H} \times \mathcal{H} \to \mathcal{H}$ 。

- 标量乘法， $\cdot: \mathbb{C} \times \mathcal{H} \to \mathcal{H}$ 。

这两个运算满足以下条件：

- 加法交换律， $\vec{\varphi} + \vec{\psi} = \vec{\psi} + \vec{\varphi}$ 。

- 加法结合律， $\vec{\varphi} + (\vec{\psi} + \vec{\chi}) = (\vec{\varphi} + \vec{\psi}) + \vec{\chi}$ 。

- 在集合 $\mathcal{H}$ 之中存在元素 $0$ 满足 $0 + \vec{\varphi} = \vec{\varphi}$ 。

- 对任何 $\vec{\varphi} \in \mathcal{H}$ ，都存在 $-\vec{\varphi} \in \mathcal{H}$ 满足 $\vec{\varphi} + (-\vec{\varphi}) = 0$ 。

- 对于复数 $1$ ，满足 $1 \cdot \vec{\varphi} = \vec{\varphi}$ 。

- 对于复数 $\lambda, \mu$ ，满足 $\lambda \cdot (\mu \cdot \vec{\varphi}) = \lambda\mu \cdot \vec{\varphi}$ 。

- 对于复数 $\lambda, \mu$ ，满足 $(\lambda + \mu) \cdot \vec{\varphi} = \lambda \cdot \vec{\varphi} + \mu \cdot \vec{\varphi}$ 。

- 对于复数 $\lambda, \mu$ ，满足 $\lambda \cdot (\vec{\varphi} + \vec{\psi}) = \lambda \cdot \vec{\varphi} + \lambda \cdot \vec{\psi}$ 。

一个定义了内积运算的向量空间称为**向量空间（ Inner product space ）**。内积 $\pro{\cdot}{\cdot}: \mathcal{H} \times \mathcal{H} \to \mathbb{C}$ 应当满足以下条件：

- 对于任何 $\vec{\varphi} \in \mathcal{H}$ ，都有 $\pro{\varphi}{\varphi} \geq 0$ ，且等号成立当且仅当 $\vec{\varphi} = 0$ 。

- 对于任何 $\vec{\varphi}, \vec{\psi} \in \mathcal{H}$ ，都有 $\pro{\varphi}{\psi} = \pro{\psi}{\varphi}^*$ 。

- 对于复数 $\lambda_1, \lambda_2$ 和向量 $\varphi, \psi_1, \psi_2$ 有 $\pro{\varphi}{\lambda_1\psi_1 + \lambda_2\psi_2} = \lambda_1\pro{\varphi}{\psi_1} + \lambda_2\pro{\varphi}{\psi_2}$ 。

基于内积，我们可以给出更多的定义。

两个向量 $\vec{\varphi}, \vec{\psi}$ 如果有 $\pro{\varphi}{\psi} = 0$ ，就称 $\vec{\varphi}, \vec{\psi}$ **正交（ Orthogonal ）**，记作 $\vec{\varphi} \bot \vec{\psi}$ 。

我们定义一个向量 $\vec{\varphi} \in \mathcal{H}$ 的**长度（ Length ）**或者**范数（ Norm ）**为：

$$
\\| \varphi \\| = \sqrt{\pro{\varphi}{\varphi}}
$$

定义长度为 $1$ 的向量为**单位向量（ Unit vector ）**。

## Cauchy 极限和 Hilbert 空间

记 $\\{ \vec{\varphi_n} \\}$ 是一个在 $\mathcal{H}$ 上的向量序列，且 $\vec{\varphi}$ 是 $\mathcal{H}$ 上的一个向量。

如果说对于任何的 $\varepsilon > 0$ 都存在一个正整数 $N$ 满足对于任何的 $m, n > N$ 都有 $\\| \varphi_m - \varphi_n \\| < \varepsilon$ 。就称 $\\{ \vec{\varphi_n} \\}$ 是一个 **Cauchy 序列**。

如果说对于任何的 $\varepsilon > 0$ 都存在一个正整数 $N$ 满足对于任何的 $n > N$ 都有 $\\| \varphi_n - \varphi \\| < \varepsilon$ 。就称 $\vec{\varphi}$ 是序列 $\\{ \vec{\varphi_n} \\}$ 的极限，记作：

$$
\lim_{n \to \infty} \vec{\varphi_n} = \vec{\varphi}
$$

如果一个内积空间满足任何的 Cauchy 序列都存在极限，那么这个内积空间就称作 **Hilbert 空间**。

## 向量空间的基

一个有穷的或者可数无穷的在 $\mathcal{H}$ 上的单位向量序列 $\\{ \vec{\varphi_n} \\}$ 是 $\mathcal{H}$ 的**正交基（ Orthonormal basis ）**，如果：

- 序列 $\\{ \vec{\varphi_n} \\}$ 中的向量两两正交。即对于任何的 $i \neq j$ 都有 $\vec{\varphi_i} \bot \vec{\varphi_j}$ 。

- 任何 $\vec{\varphi} \in \mathcal{H}$ 都满足存在一个复数序列 $\\{ \lambda_n \\}$ 满足：

$$
\vec{\varphi} = \sum_j \lambda_j\vec{\varphi_j}
$$

我们不加证明地认为，同一个向量空间的两个正交基具有相同数量的向量，一个正交基之中向量的数量称为向量空间的**维数（ Dimension ）**，记作 $\dim\mathcal{H}$ 。

对于维数有限的向量空间，记 $\dim\mathcal{H} = n$ 。取其一组正交基 $\\{ \vec{\varphi_1}, \vec{\varphi_2}, \cdots, \vec{\varphi_n} \\}$ ，我们认为对于任何的 $\vec{\varphi} \in \mathcal{H}$ ，其都存在唯一的复数组 $\\{ \lambda_1, \lambda_2, \cdots, \lambda_n \\}$ 满足 $\vec{\varphi} = \sum_j \lambda_j\vec{\varphi_j}$ 。从而我们可以将 $\mathcal{H}$ 之中的向量用 $\mathbb{C}^n$ 之中的向量表示：

$$
\\left[
    \\begin{matrix}
    \lambda_1 \\\\ \lambda_2 \\\\ \vdots \\\\ \lambda_n
    \\end{matrix}    
\\right]
$$

## 闭子空间

对于一个 Hilbert 空间 $\mathcal{H}$ ，称 $X \subset \mathcal{H}$ 为 $\mathcal{H}$ 的一个**子空间（ Subspace ）**，如果对任何的 $\vec{\varphi}, \vec{\psi} \in X$ 和任何复数 $\lambda$ 都有 $\vec{\varphi} + \vec{\psi} \in X$ 以及 $\lambda\vec{\varphi} \in X$ 。

对于 Hilbert 空间 $\mathcal{H}$ 的子空间 $X$ 定义其**闭包（ Closure ）**为：

$$
\overline X = \left\\{ \vec{\varphi}: \exists \\{ \vec{\varphi_n} \\} \in X, \lim_{n \to \infty} \vec{\varphi_n} = \vec{\varphi} \right\\}
$$

子空间的闭包也就是这个子空间之中所有向量序列的极限构成的集合。如果一个子空间的闭包就是其本身，那么这个空间是**闭子空间（ Closed subspace ）**。

另外，定义由子空间 $X$ 的**张成空间（ Spanned space ）**为：

$$
\span(X) = \left\\{ \sum_{j = 1}^n \lambda_j\vec{\varphi_j}: n \geq 0, \lambda_j \in \mathbb{C}, \vec{\varphi_j} \in X \right\\}
$$

而子空间 $X$ 的**闭张成空间（ Closed spanned space ）**定义为 $\overline{\span(X)}$ 。

## 正交性

对一个向量空间 $\mathcal{H}$ 的两个子空间 $X, Y$ 。我们称 $X, Y$ 是正交的，如果对于任何的 $\vec{\varphi} \in X, \vec{\psi} \in Y$ 都有 $\vec{\varphi} \bot \vec{\psi}$ 。子空间的正交记作 $X \bot Y$ 。

另外，我们定义子空间 $X$ 的**正交补（ Orthocomplement ）**为：

$$
X^\bot = \\{ \vec{\varphi} \in \mathcal{H}: \vec{\varphi} \bot X \\}
$$

我们不加证明地给出，一个子空间的正交补必然是闭子空间，且一个子空间的正交补的正交补是自身（即 $(X^\bot)^\bot = X$ ）。

最后，我们给出两个子空间**直和**的定义：

$$
X \oplus Y = \\{ \vec{\varphi} + \vec{\psi}: \vec{\varphi} \in X, \vec{\psi} \in Y \\}
$$

# 量子机制假设

我们在研究量子计算之前，首先做这样的假定。一个闭的（比如说孤立的）量子系统的状态空间可以使用 Hilbert 空间描述，此时状态等价于向量，而这个系统中的**纯状态（ Pure state ）**可以使用单位向量进行表述。对于若干个状态 $\vec{\varphi_1}, \cdots, \vec{\varphi_n}$ ，其**叠加（ Superposition ）**定义为向量的线性组合：

$$
\vec{\varphi} = \sum_{j = 1}^n \lambda_j\vec{\varphi_j}
$$

这里复参数 $\lambda_1, \cdots, \lambda_n$ 称为**几率幅度（ Probability amplitudes ）**。