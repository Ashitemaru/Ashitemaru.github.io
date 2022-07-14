---
title: 原创数学题集
date: 2022-07-12 15:46:42
category:
    - 【杂谈】数学
mathjax: true
---

一些奇妙的数学想法而已，可能是初中、高中、大学题，反正是弄着玩的。

<!-- more -->

# Problem 1

已知函数 $f(x) = [x](x - [x]), x \geq 0$，这里 $[x]$ 表示不大于 $x$ 的最大整数。

另外定义函数迭代符号 $f^{(n)}$ 如下：

$$
\begin{cases}
f^{(1)}(x) = f(x) \\
f^{(k + 1)}(x) = f(f^{(k)}(x)), k \in \mathbb{N}^*
\end{cases}
$$

证明：对于任何 $x \geq 0$，都有：

$$
\lim_{n \to \infty} f^{(n)}(x) = 0
$$

## Solution

首先证明，$x \geq 1$ 时 $f(x) \leq x - 1$。

我们不妨记 $\{x\} := x - [x]$，显然有 $0 \leq \{x\} < 1$。进一步，根据 $x \geq 1$ 得知 $[x] \geq 1$，那么显然：

$$
([x] - 1)(\{x\} - 1) \leq 0
$$

展开得到：

$$
f(x) = [x]\{x\} \leq [x] + \{x\} - 1 = x - 1
$$

引理证明完毕，回到原题。

此外，我们需要了解，若 $x \in [0, 1)$，则有 $[x] = 0$，即 $f(x) = 0$。

那么我们考虑下述数列，我们即需要证明下述数列极限为零：

$$
x, f(x), f^{(2)}(x), \cdots, f^{(n)}(x), \cdots
$$

若 $x \in [0, 1)$，显然 $f(x) = 0$，且根据 $f(0) = 0$ 得到 $\forall n \in \mathbb{N}^*$ 有 $f^{(n)}(x) = 0$，上述数列极限显然为零。

若 $x \geq 1$，我们只需要证明必然存在 $k \in \mathbb{N}^*$ 使得 $f^{(k)}(x) < 1$ 即可，因为只要该结论成立，就说明 $f^{(k + 1)}(x) = 0$，从而之后的所有项均为零，结论成立。

使用反证法，如果结论不成立，即 $\forall k \in \mathbb{N}^*$ 有 $f^{(k)}(x) \geq 1$。那么根据引理：

$$
f^{(k + 1)}(x) \leq f^{(k)}(x) - 1, k \in \mathbb{N}^*
$$

所以：

$$
f^{([x])}(x) \leq f^{([x] - 1)}(x) - 1 \leq \cdots \leq f^{(0)}(x) - [x] = x - [x] \in [0, 1)
$$

和假设矛盾，故原结论成立。

至此，结论证明完毕。

# Problem 2

已知凸多边形 $\mathcal{A}, \mathcal{B}$ 没有公共点，且 $\mathcal{A}$ 的任何一边不平行于 $\mathcal{B}$ 的任何一边。

已知点 $A \in \mathcal{A}, B \in \mathcal{B}$。给出已知向量 $\boldsymbol t$，且已知 $\vec{AB} \parallel \boldsymbol{t}$。

证明：当 $\vec{AB}$ 的长度最小的时候，点 $A, B$ 中必有一个为其所在凸多边形的顶点。

## Solution


