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
\newcommand{\opro}[2]{
    \left| #1 \right\rangle\left\langle #2 \right|
}
$$

$$
\newcommand{\iipro}[3]{
    \left\langle #1 \middle| #2 \middle| #3 \right\rangle
}
$$

$$
\newcommand{\span}{
    \mathop{\rm span}
}
$$

$$
\newcommand{\spec}{
    \mathop{\rm spec}
}
$$

$$
\newcommand{\lv}{
    \left[\\begin{matrix}
}
$$

$$
\newcommand{\rv}{
    \\end{matrix}\right]
}
$$

上面出现的迷之空行是因为我在使用 `\newcommand` 命令修改 `mathjax` 。这里也顺便提一下，这个笔记的向量将会用竖线加尖括号的方式写出，如 $\vec{\varphi}$ 。内积则长成 $\pro{\varphi}{\varphi}$ 。共轭复数则在右上角加上星号表示。

# 向量空间基础

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

# 线性算子基础

对于两个 Hilbert 空间 $\mathcal{H}, \mathcal{K}$ 。给定映射 $A: \mathcal{H} \to \mathcal{K}$ ，称其为一个**线性算子（ Linear operator ）**，如果其满足以下条件：

- $A(\vec{\varphi} + \vec{\psi}) = A\vec{\varphi} + A\vec{\psi}$

- $A(\lambda \vec{\varphi}) = \lambda A\vec{\varphi}$

有两个常用的线性算子。**恒等算子（ Identity operator ）**将所有向量映射到自身，其记号为 $I_{\mathcal{H}}$ 。**零算子（ Zero operator ）**将所有向量映射到 $0$ ，其记号为 $0_{\mathcal{H}}$ 。

## 外积

此时可以定义两个向量的**外积（ Outer product ）**，其结果是一个 $\mathcal{H}$ 上的线性算子。对于 $\vec{\varphi}, \vec{\psi} \in \mathcal{H}$ ，外积算子 $\opro{\varphi}{\psi}$ 的定义为：

$$
(\opro{\varphi}{\psi}) \vec{\chi} = \pro{\psi}{\chi}\vec{\varphi}
$$

## 投影

对于 Hilbert 空间 $\mathcal{H}$ 的一个闭子空间 $X$ ，我们不加证明地给出，对于 $\mathcal{H}$ 上的任意向量 $\vec{\varphi}$ ，均唯一存在 $\vec{\varphi_1} \in X, \vec{\varphi_2} \in X^\bot$ 满足 $\vec{\varphi} = \vec{\varphi_1} + \vec{\varphi_2}$ 。

这里 $\vec{\varphi_1}$ 是 $\vec{\varphi}$ 到子空间 $X$ 上的**投影（ Projection ）**。我们不加证明地给出，存在一个线性算子 $P_X: \mathcal{H} \to X$ 满足 $P_X \vec{\varphi} = \vec{\varphi_1}$ ，这个算子称为**投影子（ Projector ）**。

## 有界算子和算子范数

我们称线性算子 $A$ **有界（ Bounded ）**，如果存在 $C \geq 0$ 满足对任何的 $\vec{\varphi} \in \mathcal{H}$ 都有 $\\| A\vec{\varphi} \\| \leq C\\| \varphi \\|$ 。空间 $\mathcal{H}$ 上的所有有界算子构成的集合记作 $\mathcal{L}(\mathcal{H})$ 。

同时我们定义算子的**范数（ Norm ）**为：

$$
\\| A \\| = \inf \\{ C \geq 0: \forall \vec{\varphi} \in \mathcal{H}, \\| A\vec{\varphi} \\| \leq C\\| \varphi \\| \\}
$$

给定算子范数的同时，我们给出算子之间的距离：

$$
d(A, B) = \sup_{\vec{\varphi}} \\| A\vec{\varphi} - B\vec{\varphi} \\|
$$

## 正算子与 Löwner 偏序

对于有界算子 $A$ ，我们称其为**正的（ Positive ）**，如果对于任何的 $\vec{\varphi} \in \mathcal{H}$ 都有：

$$
\iipro{\varphi}{A}{\varphi} := \pro{\varphi}{A\varphi} \geq 0
$$

而基于此，我们给出算子之间的一个偏序关系，也就是 **Löwner 偏序（Löwner order）**。我们记 $A \sqsubseteq B$ 当且仅当算子 $B - A$ 是正的。

## 线性算子的矩阵表示

我们可以用矩阵表示一个线性算子。这里 $A$ 是 $n$ 维 Hilbert 空间 $\mathcal{H}$ 上的线性算子， $\\{ \vec{\varphi_1}, \cdots, \vec{\varphi_n} \\}$ 是 $\mathcal{H}$ 的一组正交基：

$$
A = (a\_{ij})\_{n \times n} = \lv a_{11} & \cdots & a_{1n} \\\\ \vdots & \ddots & \vdots \\\\ a_{n1} & \cdots & a_{nn} \rv
$$

矩阵之中的元素定义为：

$$
a_{ij} = \iipro{\varphi_i}{A}{\varphi_j}
$$

## 酉算子

我们首先需要引入**伴随算子（ Adjoint operator ）**的概念。对于 $\mathcal{H}$ 上的线性算子 $A$ ，存在唯一的线性算子 $A^\dagger$ 满足对于任何的 $\vec{\varphi}, \vec{\psi} \in \mathcal{H}$ 都有（这里使用 $(\cdot, \cdot)$ 表示内积，防止歧义）：

$$
(A\vec{\varphi}, \psi) = (\varphi, A^\dagger \vec{\psi})
$$

可以证明 $A^\dagger$ 的矩阵是 $A$ 的矩阵的**共轭转置**。

我们称 $\mathcal{H}$ 上的线性算子 $U$ 是**酉算子（ Unitary operator ）**，如果：

$$
UU^\dagger = U^\dagger U = I_{\mathcal{H}}
$$

酉算子的特性是保内积，也就是对于任何的 $\vec{\varphi}, \vec{\psi} \in \mathcal{H}$ ，都有：

$$
\pro{\varphi}{\psi} = (U\vec{\varphi}, U\vec{\psi}) 
$$

## Hermit 算子

我们称有界算子 $M$ 是 **Hermit 算子（ Hermitian operator ）**，如果这个算子是自伴随的，即 $M^\dagger = M$ 。在量子力学中，一个 Hermit 算子往往被称为 **可观察量（ Observable ）**。

不难证明，投影子 $P$ 是 Hermit 算子，且 $P^2 = P$ 。事实上，如果一个 Hermit 算子 $P'$ 满足 $P'^2 = P'$ ，那么 $P'$ 是投影子。

# 特征空间基础

## 特征值和特征向量

已知 $A$ 是向量空间 $\mathcal{H}$ 上的线性算子，称 $\vec{\psi}$ 是其**特征向量（ Eigenvector ）**，如果存在复数 $\lambda$ 满足 $A\vec{\psi} = \lambda\vec{\psi}$ 。这里 $\lambda$ 则是**特征值（ Eigenvalue ）**。

线性算子 $A$ 的特征值构成的集合称为**谱（ Spectrum ）**，一般记作 $\spec(A)$ 。

对于算子 $A$ 的某个给定的特征值 $\lambda$ ，称集合：

$$
\\{ \vec{\psi} \in \mathcal{H}: A\vec{\psi} = \lambda\vec{\psi} \\}
$$

为算子 $A$ 属于特征值 $\lambda$ 的**特征空间（Eigenspace）**。可以说明特征空间是一个闭子空间。

## 谱分解

对于任何一个线性算子 $M$ ，我们不加证明地指出可以将其分解为：

$$
M = \sum_{\lambda \in \spec(M)} \lambda P_\lambda
$$

这里 $P_\lambda$ 是到属于特征值 $\lambda$ 的特征空间上的投影子。

# 量子力学假设

我们在研究量子计算之前，首先做一些假定。

- **第一假设：**一个闭的（比如说孤立的）量子系统的状态空间可以使用 Hilbert 空间描述，此时状态等价于向量，而这个系统中的**纯状态（ Pure state ）**可以使用单位向量进行表述。
 
对于若干个状态 $\vec{\varphi_1}, \cdots, \vec{\varphi_n}$ ，其**叠加（ Superposition ）**定义为向量的线性组合：

$$
\vec{\varphi} = \sum_{j = 1}^n \lambda_j\vec{\varphi_j}
$$

这里复参数 $\lambda_1, \cdots, \lambda_n$ 称为**几率幅度（ Probability amplitudes ）**。

- **第二假设：**一个闭的量子系统在时刻 $t_0$ 和 $t$ 的状态分别为 $\vec{\psi_0}$ 和 $\vec{\psi}$ ，那么存在一个仅和 $t_0, t$ 有关的酉算子 $U$ 满足 $\vec{\psi} = U\vec{\psi_0}$ 。

- **第三假设：**对一个闭的量子系统的测量可以被描述为一系列的有界算子 $\\{ M_m \\} \subseteq \mathcal{L}(\mathcal{H})$ （称为**测量算子**），其中 $\mathcal{H}$ 是这个量子系统的状态空间，且 $\\{ M_m \\}$ 满足归一化条件：

$$
\sum_m M_m^\dagger M_m = I_{\mathcal{H}}
$$

这里每一个测量算子都代表了一个可能的测量结果。

假定测量前的一瞬间系统的状态为 $\vec{\varphi}$ ，那么测量出第 $j$ 个结果的概率为：

$$
p(j) = \\| M_j\vec{\varphi} \\|^2 = \iipro{\varphi}{M_j^\dagger M_j}{\varphi} 
$$

而当测量出第 $j$ 个结果的时候，测量完毕后的系统状态为：

$$
\vec{\varphi_j} = \frac{M_j\vec{\varphi}}{\sqrt{p(j)}}
$$

{% note info no-icon %}
有一种较为常用的测量，称为**投影测量（ Projection measurement ）**。首先我们有可观测量 $M$ ，定义测量算子为到其各个特征空间上的投影子，也就是 $\\{ P_\lambda: \lambda \in \spec(M) \\}$ 。当测量一个状态为 $\vec{\psi}$ 的系统的时候，得到结果 $\lambda$ 的可能为（这里用特征值作为各个结果的标号）：

$$
p(\lambda) = \iipro{\psi}{P_\lambda^\dagger P_\lambda}{\psi} = \iipro{\psi}{P_\lambda}{\psi}
$$

得到这个结果后的系统状态为：

$$
\vec{\psi_\lambda} = \frac{P_\lambda\vec{\psi}}{\sqrt{p(\lambda)}}
$$

而测量结果的期望为：

$$
\mathbb{E}(M) = \sum_{\lambda \in \spec(M)} p(\lambda) \cdot \lambda = \sum_{\lambda \in \spec(M)} \iipro{\psi}{P_\lambda}{\psi} \cdot \lambda = \iipro{\psi}{M}{\psi}
$$

最后一步使用了谱分解的性质。
{% endnote %}

## 量子比特

考虑下列 Hilbert 空间：

$$
\mathcal{H}_2 = \mathbb{C}^2 = \\{ \alpha\vec{0} + \beta\vec{1}: \alpha, \beta \in \mathbb{C} \\}
$$

其内积的定义为：

$$
(\alpha\vec{0} + \beta\vec{1}, \alpha'\vec{0} + \beta'\vec{1}) = \alpha^\*\alpha' + \beta^\*\beta'
$$

不难得知 $\\{ \vec{0}, \vec{1} \\}$ 是 $\mathcal{H}_2$ 的一组正交基。

---

一般意义的比特具有 `0, 1` 两种稳定的状态，而**量子比特（ Qubit ）**则处于两种状态的叠加态上。我们可以用 $\vec{0}$ 表述一般的 `0` 态，用 $\vec{1}$ 表述一般的 `1` 态，那么 $\mathcal{H}_2$ 实际上就是量子计算所需要讨论的状态空间。

单个量子比特的状态我们认为是纯的，所以其状态表述为 $\vec{\psi} = \alpha\vec{0} + \beta\vec{1}$ ，这里 $|\alpha|^2 + |\beta|^2 = 1$ 。

另外，我们引入两个记号：

$$
\vec{+} := \frac{\vec{0} + \vec{1}}{\sqrt{2}} = \frac{1}{\sqrt{2}} \lv 1 \\\\ 1 \rv, \vec{-} := \frac{\vec{0} - \vec{1}}{\sqrt{2}} = \frac{1}{\sqrt{2}} \lv 1 \\\\ -1 \rv
$$

利用这两个记号，我们可以引入 Hadamard 变换，其算子的矩阵为（显然这是个酉算子）：

$$
H = \frac{1}{\sqrt{2}} \lv 1 & 1 \\\\ 1 & -1 \rv 
$$

这个算子满足 $H\vec{0} = \vec{+}, H\vec{1} = \vec{-}$ 。

{% note info no-icon %}
事实上对于可能状态更多的状态空间，比如说有可数无穷多个状态的状态空间，其稳定状态可以用 $\cdots, \vec{-1}, \vec{0}, \vec{1}, \cdots, \vec{n}, \cdots$ 表述，而其状态空间则标记为：

$$
\mathcal{H}\_\infty = \left\\{ \sum\_{j = -\infty}^{+\infty}\alpha\_j\vec{j}: \alpha\_j \in \mathbb{C}, \sum\_{j = -\infty}^{+\infty} |\alpha\_j|^2 < \infty \right\\}
$$

内积的定义则是：

$$
\left( \sum\_{j = -\infty}^{+\infty}\alpha\_j\vec{j}, \sum\_{j = -\infty}^{+\infty}\alpha'\_j\vec{j} \right) = \sum\_{j = -\infty}^{+\infty} \alpha_j^*\alpha_j'
$$

此时 $\cdots, \vec{-1}, \vec{0}, \vec{1}, \cdots, \vec{n}, \cdots$ 是一组正交基。

另外，在 $\mathcal{H}_\infty$ 上也存在着一个常用的酉算子 $T_k$ ，其一般被称作**翻译（ Translation ）**。其性质为 $T_k\vec{n} = \vec{n + k}$ ，这个算子的作用类似将一个粒子平移 $k$ 个状态。
{% endnote %}