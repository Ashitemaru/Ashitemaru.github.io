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

使用反证法。假设当 $\|\vec{AB}\|_2$ 取到最小值的时候 $A, B$ 中至少有一点位于其所在多边形的内部（即不位于其所在多边形的边界），不妨令 $A$ 位于 $\mathcal{A}$ 的内部。由于 $\mathcal{A} \cap \mathcal{B} = \varnothing$ 与 $B \in \mathcal{B}$，可以得知 $B$ 位于多边形 $\mathcal{A}$ 的外部。那么线段 $AB$ 显然与 $\mathcal{A}$ 的边界 $\partial\mathcal{A}$ 有交点，不妨记为 $A'$。那么我们显然得到 $\|\vec{A'B}\|_2 < \|\vec{AB}\|_2$，这与最小性假设矛盾，假设不成立。故当 $\|\vec{AB}\|_2$ 取到最小值的时候 $A, B$ 均应当位于其所在多边形的边界上。

继续使用反证法。假设当 $\|\vec{AB}\|_2$ 取到最小值的时候 $A, B$ 均不位于其所在多边形的顶点。此时不妨假设 $A$ 所在的 $\mathcal{A}$ 的边为线段 $a$，$B$ 所在的 $\mathcal{B}$ 的边为线段 $b$。由 $\mathcal{A}$ 的任何一条边不平行于 $\mathcal{B}$ 的任何一条边知 $a$ 不平行于 $b$，且据假设知 $A$ 不位于 $a$ 的顶点，$B$ 不位于 $b$ 的顶点。

由于 $a$ 不平行于 $b$，记 $a$ 所在直线和 $b$ 所在直线的交点为 $C$，令点 $A'$ 为线段 $AC$ 上一点，过 $A'$ 作 $A'B' \parallel AB$ 与 $b$ 所在直线相交于 $B'$。显然无论 $A'$ 如何取，均能够满足 $\|\vec{A'B'}\|_2 < \|\vec{AB}\|_2$。

由于 $A, B$ 均不是其所在线段的顶点，那么存在一个 $\delta_A > 0$ 满足 $\|\vec{AA'}\|_2 < \delta_A$ 时即有 $A' \in a$，即 $A' \in \mathcal{A}$。同理也存在类似的 $\delta_B > 0$ 满足 $\|\vec{BB'}\|_2 < \delta_B$ 时即有 $B' \in \mathcal{B}$。

考虑到 $\triangle CAB \backsim \triangle CA'B'$，我们有下述关系：

$$
\frac{\|\vec{AA'}\|_2}{\|\vec{BB'}\|_2} = \frac{\|\vec{CA}\|_2}{\|\vec{CB}\|_2} \iff\|\vec{AA'}\|_2 = \frac{\|\vec{CA}\|_2 \cdot \|\vec{BB'}\|_2}{\|\vec{CB}\|_2}
$$

故我们只需要令：

$$
0 < \|\vec{AA'}\|_2 < \min\left\{\delta_A, \frac{\|\vec{CA}\|_2 \cdot \delta_B}{\|\vec{CB}\|_2}\right\}
$$

即可保证 $\|\vec{AA'}\|_2 < \delta_A$ 且 $\|\vec{BB'}\|_2 < \delta_B$，这就保证了 $A' \in \mathcal{A}, B' \in \mathcal{B}$，并且有 $\|\vec{A'B'}\|_2 < \|\vec{AB}\|_2$。这样就与最小性假设矛盾，假设不成立。故当 $\|\vec{AB}\|_2$ 取到最小值的时候 $A, B$ 中至少有一个点为其所在多边形的顶点。
