---
title: 原创数学题集
date: 2022-07-12 15:46:42
category:
    - 【杂谈】数学
mathjax: true
---

一些奇妙的数学想法而已，可能是初中、高中、大学题，反正是弄着玩的。

<!-- more -->

$$
\newcommand{\i}{\mathop{\rm i}}
$$

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

# Problem 3

(Credit to PKU 零之审判)

边长为 $2$ 的正方形 $ABCD$ 内有四条长度为 $1$ 的线段 $l_{1, 2, 3, 4}$，这四条线段满足：

- $l_i$ 与 $l_{i + 1}$ 相交但其内部不相交（即交点为 $l_i$ 或 $l_{i + 1}$ 的端点）
- $l_1, l_3$ 没有公共点且 $l_2, l_4$ 没有公共点
- $l_i$ 垂直于 $l_{i + 1}$

上述陈述中 $i \in \{1, 2, 3, 4\}$，并且定义 $l_5 := l_1$。

记 $S = l_1 \cup l_2 \cup l_3 \cup l_4$，$V$ 为正方形 $ABCD$ 四边中点构成的集合，定义：

$$
d := \max_{v \in V, s \in S} |v - s|
$$

求 $d$ 的最小值。

## Solution

考虑如下图所示的 $l_{1, 2, 3, 4}$：

<img src="/uploads/original-problems/1.png" height="20%" width="20%" />

这里 $l_{1, 2, 3, 4}$ 构成边长为 $1$ 的正方形，该正方形与正方形 $ABCD$ 有共同中心且 $l_1 \parallel AD$。不难看出若令 $M$ 为 $CD$ 中点，$N$ 为 $l_1, l_2$ 交点，则 $d = MN = \dfrac{\sqrt{10}}{2}$。

下面我们证明 $d \geq \dfrac{\sqrt{10}}{2}$。为了证明该结论，我们考虑下述圆 $P$：

<img src="/uploads/original-problems/3.png" height="20%" width="20%" />

圆 $P$ 以正方形 $ABCD$ 的中心 $O$ 为圆心，半径为 $\dfrac{\sqrt{2}}{2}$。

我们首先证明下述引理，即如果 $l_{1, 2, 3, 4}$ 上有点 $X$ 在圆 $P$ 外或者在圆 $P$ 边界上，那么 $d \geq \dfrac{\sqrt{10}}{2}$。

我们考虑下述图形：

<img src="/uploads/original-problems/4.png" height="35%" width="35%" />

这里的四个虚线圆为以正方形 $ABCD$ 各边中点为圆心，以 $\dfrac{\sqrt{10}}{2}$ 为半径所作的。注意到由于这四个圆的内部（不包含边界）的交集完全位于圆 $P$ 的内部，这说明 $X$ 必然在这四个虚线圆中的某个之外或者这四个虚线圆中的某个的边界上。也就是说必然存在某个 $M \in V$ 使得 $MX \geq \dfrac{\sqrt{10}}{2}$，从而：

$$
d \geq MX \geq \dfrac{\sqrt{10}}{2}
$$

引理证明完毕，回到原题。

根据引理我们得知，如果 $l_{1, 2, 3, 4}$ 上有点在圆 $P$ 外，则欲证命题成立，所以现在只需要考虑 $S \subseteq P$。

根据题设对四条线段的约束，我们可以得知这四条线段仅有下述两种可能：

<img src="/uploads/original-problems/2.png" height="50%" width="50%" />

上述两种情况的旋转、镜面对称等视为同种情况。

首先考虑左边的情况，这里记 $A'D' = B'C' =: a, A'B' = C'D' =: b$，我们有 $0 < a, b \leq 1$。根据 $S \subseteq P$ 此时必然有下述两条虚线段长度不大于 $\sqrt{2}$：

<img src="/uploads/original-problems/5.png" height="25%" width="25%" />

从而有下述约束：

$$
\begin{cases}
a^2 + (2 - b)^2 \leq 2 \Rightarrow a^2 + b^2 - 4b + 2 \leq 0 \\
b^2 + (2 - a)^2 \leq 2 \Rightarrow a^2 + b^2 - 4a + 2 \leq 0 \\
\end{cases}
$$

两式相加并整理得到：

$$
(a - 1)^2 + (b - 1)^2 \leq 0
$$

可知 $a = b = 1$，此时 $A', B', C', D'$ 构成边长为 $1$ 的正方形。考虑到边长为 $1$ 的正方形的外接圆半径为 $\dfrac{\sqrt{2}}{2}$，所以为了 $S \subseteq P$，只能有该正方形内接于圆 $P$。

之后考虑右边的情况，根据 $S \subseteq P$ 此时必然有下述两条虚线段长度不大于 $\sqrt{2}$。

这里记 $A'B' = C'D' =: a, A'A'' =: b, D'D'' =: c$，我们有 $0 < a \leq 1$ 以及 $0 \leq b, c \leq 1 - a$：

<img src="/uploads/original-problems/6.png" height="20%" width="20%" />

从而有下述约束：

$$
\begin{cases}
D''C' + B'B'' \leq 1 \Rightarrow b - c + 1 \leq 1 \\
A''B' + C'C'' \leq 1 \Rightarrow c - b + 1 \leq 1 \\
\end{cases}
$$

可知 $b = c$，此时 $A'', B'', C'', D''$ 构成边长为 $1$ 的正方形。考虑到边长为 $1$ 的正方形的外接圆半径为 $\dfrac{\sqrt{2}}{2}$，所以为了 $S \subseteq P$，只能有该正方形内接于圆 $P$。

综上所述，此时无论如何均存在四个 $S$ 内的点构成内接于圆 $P$ 的正方形，至少说明 $S$ 中存在某一个点 $X$ 在圆 $P$ 边界上。根据引理，原命题证明完毕。

综上所述，$d$ 的最小值为 $\dfrac{\sqrt{10}}{2}$。

# Problem 4

记 $\theta_{n, k} := \dfrac{(2k + 1)\pi}{2^n}$，证明：

$$
\sum_{k = 0}^{2^n - 1} \frac{1}{1 - \cos\theta_{n, k}} = 2^{2n - 1}
$$

## Solution

我们注意到下述恒等式：

$$
\frac{4}{1 - \cos 2\theta} = \frac{1}{1 - \cos\theta} + \frac{1}{1 + \cos\theta}
$$

证明如下：

$$
\frac{1}{1 - \cos\theta} + \frac{1}{1 + \cos\theta} = \frac{2}{1 - \cos^2\theta} = \frac{4}{1 - (2\cos^2\theta - 1)} = \frac{4}{1 - \cos 2\theta}
$$

基于上述恒等式，我们令：

$$
S_n := \sum_{k = 0}^{2^n - 1} \frac{1}{1 - \cos\theta_{n, k}}
$$

我们即可得到：

$$
S_{n + 1} = \sum_{k = 0}^{2^{n + 1} - 1} \frac{1}{1 - \cos\theta_{n + 1, k}} = \sum_{k = 0}^{2^n - 1} \left(\frac{1}{1 - \cos\theta_{n + 1, k}} + \frac{1}{1 - \cos\theta_{n + 1, 2^n + k}}\right) \\
$$

这里注意到：

$$
\cos\theta_{n + 1, 2^n + k} = \cos\dfrac{[2(2^n + k) + 1]\pi}{2^{n + 1}} = \cos\left(1 + \frac{2k + 1}{2^{n + 1}}\right)\pi = -\cos\theta_{n + 1, k}
$$

另外注意到：

$$
\theta_{n, k} = \dfrac{(2k + 1)\pi}{2^n} = 2\theta_{n + 1, k}
$$

那么：

$$
\begin{aligned}
S_{n + 1} &= \sum_{k = 0}^{2^n - 1} \left(\frac{1}{1 - \cos\theta_{n + 1, k}} + \frac{1}{1 - \cos\theta_{n + 1, 2^n + k}}\right) \\
&= \sum_{k = 0}^{2^n - 1} \left(\frac{1}{1 - \cos\theta_{n + 1, k}} + \frac{1}{1 + \cos\theta_{n + 1, k}}\right) \\
&= \sum_{k = 0}^{2^n - 1} \frac{4}{1 - \cos2\theta_{n + 1, k}} \\
&= 4\sum_{k = 0}^{2^n - 1} \frac{1}{1 - \cos\theta_{n, k}} \\
&= 4S_n
\end{aligned}
$$

而显然有 $S_1 = 2$，从而命题证明完毕。

# Problem 5

已知正整数 $n$ 与实数 $x$，证明：

$$
\sum_{k = 0}^n C_n^k\sin kx = 2^n\cos^n\frac{x}{2}\sin\frac{nx}{2}
$$

## Solution

考虑 $(\cos x + \i\sin x + 1)^n \in \mathbb{C}$ 的虚部。

一方面：

$$
\begin{aligned}
(\cos x + \i\sin x + 1)^n &= \left[\left(2\cos^2\frac{x}{2} - 1\right) + 2\i\sin\frac{x}{2}\cos\frac{x}{2} + 1\right]^n \\
&= 2^n\cos^n\frac{x}{2}\left(\cos\frac{x}{2} + \i\sin\frac{x}{2}\right)^n \\
&= 2^n\cos^n\frac{x}{2}\cos\frac{nx}{2} + \i2^n\cos^n\frac{x}{2}\sin\frac{nx}{2}
\end{aligned}
$$

可知该复数的虚部即为欲证等式的右侧。

另一方面：

$$
\begin{aligned}
(\cos x + \i\sin x + 1)^n &= \sum_{k = 0}^n C_n^k(\cos x + \i\sin x)^k \\
&= \sum_{k = 0}^n C_n^k(\cos kx + \i\sin kx) \\
&= \sum_{k = 0}^n C_n^k\cos kx + \i\sum_{k = 0}^n C_n^k\sin kx
\end{aligned}
$$

可知该复数的虚部即为欲证等式的左侧。

综上所述，证明完毕。

# Problem 6

已知数列 $\{a_n\}_{n \geq 0}$ 满足 $a_0 = 1, a_1 = 0$ 且 $a_{n + 2} = (n + 1)(a_{n + 1} + a_n)$。证明：

$$
\sum_{k = 0}^n C_n^ka_k = n!
$$

## Solution

定义命题 $A_n$ 表示下述等式成立：

$$
\sum_{k = 0}^{n + 1} C_{n + 1}^ka_k = (n + 1)\sum_{k = 0}^n C_n^ka_k
$$

命题 $B_n$ 表示下述等式成立：

$$
\sum_{k = 0}^n C_n^ka_{k + 1} = n\sum_{k = 0}^n C_n^ka_k
$$

首先我们证明由 $B_n$ 能推出 $A_n$。现在在 $B_n$ 成立的基础上，考虑下述计算：

$$
\begin{aligned}
&(n + 1)\sum_{k = 0}^n C_n^ka_k = n\sum_{k = 0}^n C_n^ka_k + \sum_{k = 0}^n C_n^ka_k = \sum_{k = 0}^n C_n^ka_{k + 1} + \sum_{k = 0}^n C_n^ka_k \\
=& C_n^na_{n + 1} + \left(\sum_{k = 1}^n C_n^{k - 1}a_k + \sum_{k = 1}^n C_n^ka_k\right) + C_n^0a_0 = C_n^na_{n + 1} + \sum_{k = 1}^n (C_n^{k - 1} + C_n^k)a_k + C_n^0a_0 \\
=& C_{n + 1}^{n + 1}a_{n + 1} + \sum_{k = 1}^n C_{n + 1}^ka_k + C_{n + 1}^0a_0 = \sum_{k = 0}^{n + 1} C_{n + 1}^ka_k
\end{aligned}
$$

这就说明了在 $B_n$ 成立的基础上 $A_n$ 成立，其中第二个等号的依据为 $B_n$ 成立。

之后我们证明 $A_n, B_n$ 能推出 $B_{n + 1}$。现在在 $A_n, B_n$ 均成立的基础上，考虑下述计算：

$$
\begin{aligned}
&\sum_{k = 0}^{n + 1} C_{n + 1}^ka_{k + 1} = C_{n + 1}^0a_1 + \sum_{k = 1}^{n + 1} C_{n + 1}^ka_{k + 1} = \sum_{k = 1}^{n + 1} \frac{n + 1}{k}C_{n}^{k - 1}a_{k + 1} \\
=& (n + 1)\sum_{k = 0}^{n} \frac{1}{k + 1}C_{n}^{k} \cdot (k + 1)(a_{k + 1} + a_k) = (n + 1)\left(\sum_{k = 0}^{n} C_{n}^{k}a_{k + 1} + \sum_{k = 0}^{n} C_{n}^{k}a_k\right) \\
=& (n + 1)\left(n\sum_{k = 0}^{n} C_{n}^{k}a_k + \sum_{k = 0}^{n} C_{n}^{k}a_k\right) = (n + 1)\left[(n + 1)\sum_{k = 0}^{n} C_{n}^{k}a_k\right] = (n + 1)\sum_{k = 0}^{n + 1} C_{n + 1}^ka_k\\
\end{aligned}
$$

这就说明了在 $A_n, B_n$ 成立的基础上 $B_{n + 1}$ 成立，其中倒数第二个等号的依据为 $B_n$ 成立，倒数第一个等号的依据为 $A_n$ 成立。

而我们显然可以计算出 $a_2 = 1$，从而得知命题 $B_1$ 成立，进而据归纳法得到对任何的正整数 $n$，都有命题 $A_n$ 成立。据命题 $A_n$ 成立即可得到本问题欲证命题成立。

# Problem 7

已知正数数列 $\{x_k\}_{0 \leq k \leq n}$ 满足：

$$
x_0 = \sum_{k = 1}^n x_k
$$

定义数列 $\{y_k\}_{1 \leq k \leq n}$ 为：

$$
y_k := \sqrt{\sum_{i = 0}^{k - 1} x_i} \sqrt{\sum_{i = k}^n x_i}
$$

证明：

$$
1 < \sum_{k = 1}^n \frac{x_k}{y_k} < \frac{\pi}{2}
$$

## Solution

根据均值不等式显然有：

$$
y_k = \sqrt{\sum_{i = 0}^{k - 1} x_i} \sqrt{\sum_{i = k}^n x_i} \leq \frac{1}{2}\left(\sum_{i = 0}^{k - 1} x_i + \sum_{i = k}^n x_i\right) = \frac{1}{2}\left(x_0 + \sum_{i = 1}^n x_i\right) = x_0
$$

并且由于：

$$
\sum_{i = 0}^{k - 1} x_i > x_0 = \sum_{i = 1}^n x_i > \sum_{i = k}^n x_i
$$

得知上述等号无法取到，那么：

$$
\sum_{k = 1}^n \frac{x_k}{y_k} > \sum_{k = 1}^n \frac{x_k}{x_0} = 1
$$

从而不等式左侧得证。

为了证明不等式右侧，我们考虑下述数列 $\{S_k\}_{0 \leq k \leq n}$，其在 $k = 0$ 的时候定义为 $S_0 := 0$，在 $k > 0$ 的时候定义为：

$$
S_k := \sum_{i = 1}^k \frac{x_k}{x_0}
$$

考虑到 $\{x_k\}_{0 \leq k \leq n}$ 是正数数列，可以得知 $\{S_k\}_{0 \leq k \leq n}$ 是递增数列。另一方面，我们可以得知 $S_n = 1$。综合上述条件我们可以得知 $\{S_k\}_{0 \leq k \leq n}$ 各项均落在区间 $[0, 1]$ 上。

那么即存在所有项均落在区间 $\left[0, \dfrac{\pi}{2}\right]$ 上的数列 $\{\theta_k\}_{0 \leq k \leq n}$ 使得 $\sin\theta_k = S_k$ 对所有 $0 \leq k \leq n$ 成立。此时 $\theta_0 = 0$ 并且 $\theta_n = \dfrac{\pi}{2}$。另一方面，据 $\{S_k\}_{0 \leq k \leq n}$ 是递增数列得知 $\{\theta_k\}_{0 \leq k \leq n}$ 是递增数列。

从而我们得知：

$$
\begin{aligned}
y_k &= \sqrt{\sum_{i = 0}^{k - 1} x_i} \sqrt{\sum_{i = k}^n x_i} = x_0 \sqrt{\sum_{i = 0}^{k - 1} \frac{x_i}{x_0}} \sqrt{\sum_{i = k}^n \frac{x_i}{x_0}} = x_0 \sqrt{1 + S_{k - 1}} \sqrt{S_n - S_{k - 1}} \\
&= x_0 \sqrt{1 - S^2_{k - 1}} = x_0 \sqrt{1 - \sin^2\theta_{k - 1}} = x_0\cos\theta_{k - 1}
\end{aligned}
$$

从而：

$$
\begin{aligned}
\sum_{k = 1}^n \frac{x_k}{y_k} &= \sum_{k = 1}^n \frac{x_k}{x_0\cos\theta_{k - 1}} = \sum_{k = 1}^n \frac{S_k - S_{k - 1}}{\cos\theta_{k - 1}} = \sum_{k = 1}^n \frac{\sin\theta_k - \sin\theta_{k - 1}}{\cos\theta_{k - 1}} \\
&= \sum_{k = 1}^n \frac{2\cos\dfrac{\theta_k + \theta_{k - 1}}{2}\sin\dfrac{\theta_k - \theta_{k - 1}}{2}}{\cos\theta_{k - 1}} < 2\sum_{k = 1}^n\sin\dfrac{\theta_k - \theta_{k - 1}}{2}
\end{aligned}
$$

这里的不等号考虑到 $\{\theta_k\}_{0 \leq k \leq n}$ 是递增数列，从而 $\theta_k > \theta_{k - 1}$。

应用不等式 $\sin x < x, x > 0$ 得到：

$$
\sum_{k = 1}^n \frac{x_k}{y_k} < 2\sum_{k = 1}^n\sin\dfrac{\theta_k - \theta_{k - 1}}{2} < \sum_{k = 1}^n \theta_k - \theta_{k - 1} = \theta_n - \theta_0 = \frac{\pi}{2}
$$

综上所述，证明完毕。

# Problem 8

已知函数：

$$
f(x) = \frac{1}{[x] - \{x\} + 1}
$$

定义数列 $a_1 = 1$ 以及 $a_{n + 1} = f(a_n), n \geq 1$。证明：所有正有理数在数列 $\{a_n\}_{n \geq 1}$ 中均出现且仅出现一次。

## Solution

若 $x \in \mathbb{N}^+$，显然：

$$
f(x) = \frac{1}{x + 1} \in \mathbb{Q}^+
$$

若 $x \in \mathbb{Q}^+ \backslash \mathbb{N}^+$，记 $n := [x], \dfrac{p}{q} := \{x\}$，这里 $p, q \in \mathbb{N}^+, p < q, \mathop{\rm gcd}(p, q) = 1$，那么：

$$
f(x) = \frac{q}{nq - p + q} \in \mathbb{Q}
$$

注意到 $nq - p + q = (n + 1)q - p \geq q - p > 0$，所以 $f(x) \in \mathbb{Q}^+$。

所以若 $x \in \mathbb{Q}^+$ 则 $f(x) \in \mathbb{Q}^+$，据归纳法得知 $\{a_n\}_{n \geq 1}$ 中项均是正有理数。

下面说明数列 $\{a_n\}_{n \geq 1}$ 中没有重复项，即不存在 $m \neq n, m, n \in \mathbb{N}^+$ 使得 $a_m = a_n$。

首先说明 $f$ 是单射，即不存在 $x \neq y$ 使得 $f(x) = f(y)$。使用反证法，如果存在 $x \neq y$ 使得 $f(x) = f(y)$，那么：

$$
\frac{1}{[x] - \{x\} + 1} = \frac{1}{[y] - \{y\} + 1} \iff [x] - [y] = \{x\} - \{y\}
$$

那么：

$$
\mathbb{Z} \ni |[x] - [y]| = |\{x\} - \{y\}| < 1
$$

从而 $[x] = [y]$，进一步 $\{x\} = \{y\}$，也就是说 $x = y$，矛盾。故 $f$ 是单射。

回到原先证明，使用反证法。如果存在 $m \neq n, m, n \in \mathbb{N}^+$ 使得 $a_m = a_n$，考虑到 $f(0) = 1$，记函数迭代符号为 $f^{(n)}$，可以得到 $f^{(m)}(0) = f^{(n)}(0)$。考虑到 $f$ 是单射，所以 $f^{(|m - n|)}(0) = 0$，即 $a_{|m - n|} = 0$。这与先前证明的该数列均为正有理数矛盾。故数列 $\{a_n\}_{n \geq 1}$ 中没有重复项。

现在我们仅需要说明任何正有理数均出现在该数列中即可。

我们令 $g(x) := x + 1, h(x) := \dfrac{x}{x + 1}$。我们证明 $g(a_n) = a_{2n + 1}, h(a_n) = a_{2n}$。

使用归纳法，$n = 1$ 时上述命题的成立是显然的，假设 $n = k$ 时上述命题成立，考虑 $n = k + 1$。此时：

$$
a_{2k + 2} = f(a_{2k + 1}) = f(g(a_k)) = f(a_k + 1) = \frac{1}{[a_k + 1] - \{a_k + 1\} + 1} = \frac{1}{[a_k] - \{a_k\} + 2}
$$

而：

$$
h(a_{k + 1}) = \frac{a_{k + 1}}{a_{k + 1} + 1} = \frac{1}{1 + a_{k + 1}^{-1}} = \frac{1}{1 + (f(a_k))^{-1}} = \frac{1}{1 + ([a_k] - \{a_k\} + 1)} = \frac{1}{[a_k] - \{a_k\} + 2}
$$

故 $h(a_{k + 1}) = a_{2k + 2}$，此时：

$$
a_{2k + 3} = f(a_{2k + 2}) = f(h(a_{k + 1})) = \frac{1}{[h(a_{k + 1})] - \{h(a_{k + 1})\} + 1}
$$

由于 $x > 0$ 时 $0 < h(x) < 1$，所以：

$$
a_{2k + 3} = \frac{1}{1 - h(a_{k + 1})} = \frac{1}{1 - \dfrac{a_{k + 1}}{a_{k + 1} + 1}} = a_{k + 1} + 1 = g(a_{k + 1})
$$

故 $g(a_{k + 1}) = a_{2k + 3}$。据归纳法，证明完毕。

定义函数：

$$
\varphi(x) := \begin{cases}
g^{-1}(x) & x \geq 1 \\
h^{-1}(x) & x < 1 \\
\end{cases} = \begin{cases}
x - 1 & x \geq 1 \\
\dfrac{x}{1 - x} & x < 1 \\
\end{cases}
$$

对于既约分数 $\dfrac{p}{q} \in \mathbb{Q}^+$，显然有：

$$
\varphi\left(\dfrac{p}{q}\right) = \begin{cases}
\dfrac{p - q}{q} & p \geq q \\
\dfrac{p}{q - p} & p < q \\
\end{cases}
$$

显然任何正既约分数经过 $\varphi$ 映射后依然既约且分子和分母的和严格减少（特别规定 $0$ 的分子分母和为 $0$），而 $\varphi(0) = 0$，分子和分母的和保持不变。

对任何正既约分数 $\dfrac{p}{q}$，考虑序列 $\dfrac{p}{q}, \varphi\left(\dfrac{p}{q}\right), \varphi^{(2)}\left(\dfrac{p}{q}\right), \cdots, \varphi^{(n)}\left(\dfrac{p}{q}\right), \cdots$，序列中分子和分母的和显然始终非负且不增，这意味着从某一项开始分子分母和始终不变，而此时序列的项仅有可能全零。考虑到 $\varphi(x) = 0$ 的解仅有 $x = 0$ 或者 $x = 1$，这意味着序列中必然有 $1$。

这说明对任何正既约分数 $\dfrac{p}{q}$ 存在某一个 $g^{-1}, h^{-1}$ 组成的有限序列使得，这里 $\circ$ 是映射复合符号：

$$
g^{-1} \circ h^{-1} \circ h^{-1} \circ \cdots \circ g^{-1}\left(\dfrac{p}{q}\right) = 1
$$

也就是存在某一个 $g, h$ 组成的有限序列使得：

$$
g \circ \cdots \circ h \circ h \circ g(1) = \dfrac{p}{q}
$$

据之前的证明，$g \circ \cdots \circ h \circ h \circ g(1)$ 显然在数列 $\{a_n\}_{n \geq 1}$ 内，证明完毕。

# Problem 9

(Credit to 2023 某四省联考数学试卷)

椭圆曲线可以用于区块链等技术中的加密环节。

定义椭圆曲线 $C$：

$$
C := \{(x, y): y^2 = x^3 + ax + b, 4a^3 + 27b^2 \neq 0\}
$$

定义两个运算：

- 运算 $\tilde{\cdot}: C \to C$。令 $\tilde{P} \in C$ 是 $P \in C$ 关于 $x$ 轴的对称点
- 运算 $\oplus: C^2 \to C$。对 $P, Q \in C$：
    - 若直线 $PQ$ 与 $C$ 有第三个交点 $R \in C$，令 $P \oplus Q = \tilde{R}$
    - 若直线 $PQ$ 与 $C$ 相切且切点为 $P$，定义 $P \oplus Q = \tilde{P}$
    - 定义 $P \oplus \tilde{P} = 0^*$ 以及 $P \oplus 0^* = 0^* \oplus P = 0^*$

1. 已知 $\oplus$ 满足交换律、结合律，对于与曲线 $C$ 相切于 $P$ 的直线 $PQ$，其中 $P, Q \in C$，证明 $P \oplus P = \tilde{Q}$
2. 已知 $P(x_1, y_1), Q(x_2, y_2) \in C$，且 $PQ$ 与 $C$ 有第三个交点，求 $P \oplus Q$ 坐标

## Solution

Subproblem 1.

我们考虑式子 $P \oplus P \oplus Q \oplus \tilde{Q}$，据结合律：

$$
\begin{aligned}
P \oplus P \oplus Q \oplus \tilde{Q} &= (P \oplus P) \oplus (Q \oplus \tilde{Q}) = (P \oplus P) \oplus 0^* = P \oplus P
\end{aligned}
$$

另据结合律：

$$
\begin{aligned}
P \oplus P \oplus Q \oplus \tilde{Q} &= P \oplus (P \oplus Q) \oplus \tilde{Q}
\end{aligned}
$$

又 $PQ$ 与 $C$ 相切于 $P$，故 $P \oplus Q = \tilde{P}$，所以：

$$
\begin{aligned}
P \oplus P \oplus Q \oplus \tilde{Q} &= P \oplus \tilde{P} \oplus \tilde{Q} = 0^* \oplus \tilde{Q} = \tilde{Q}
\end{aligned}
$$

Subproblem 2.

设 $PQ$ 与曲线 $C$ 的第三个交点 $R$ 坐标为 $(x_3, y_3)$，那么 $P \oplus Q$ 的坐标为 $(x_3, -y_3)$。

从而 $y_i^2 = x_i^3 + ax_i + b, i \in \{1, 2, 3\}$。作差：

$$
\begin{cases}
y_3^2 - y_2^2 = (x_3^3 - x_2^3) + a(x_3 - x_2) \\
y_3^2 - y_1^2 = (x_3^3 - x_1^3) + a(x_3 - x_1) \\
\end{cases}
$$

若令 $PQ$ 的斜率为 $k$（斜率必然存在，否则 $R$ 不存在），则有：

$$
\begin{cases}
k(y_3 + y_2) = x_3^2 + x_2x_3 + x_2^2 + a \\
k(y_3 + y_1) = x_3^2 + x_1x_3 + x_1^2 + a \\
\end{cases}
$$

再作差：

$$
k(y_2 - y_1) = x_3(x_2 - x_1) + (x_2^2 - x_1^2) \Rightarrow k^2 = x_1 + x_2 + x_3
$$

从而就可以得到 $x_3$，基于此得到 $y_3$ 的计算则很简单了。

## Comment

这题我还是有些话想说的，我感觉这道题出得很新，但我并不觉得很好。因为很多学生会对这个运算是否是良定义产生疑问，进而在解题的时候对一些地方畏手畏脚，尤其是优等生会产生这种现象。

而且基于一个很新的背景出运算题，说实话，没什么意思。

# Problem 10

定义一个非负整数 $n$ 的二进制表示 $(a_ka_{k - 1} \cdots a_0)_2$，其需要满足：

- 对于所有 $0 \leq i \leq k$，都有 $a_i \in \{0, 1\}$
- 等式 $\sum_{i = 0}^k 2^ia_i = n$ 成立
- $0$ 的二进制表示为 $(0)_2$，并且 $n \neq 0$ 时有 $a_k = 1$

例如，$9$ 的二进制表示为 $(1001)_2$，二进制表示 $(101)_2$ 所代表的非负整数为 $5$。

1. 证明对于任意 $n \in \mathbb{N}$，其二进制表示存在且唯一
2. 对非负整数定义满足交换律与结合律的运算 $\oplus$ 如下：

- 若 $x, y \in \{0, 1\}$，若 $x = y$，那么 $x \oplus y = 0$，否则 $x \oplus y = 1$
- 若 $x, y$ 中有一个不属于 $\{0, 1\}$，记 $x$ 的二进制表示为 $(x_px_{p - 1} \cdots x_0)_2$，$y$ 的二进制表示为 $(y_qy_{q - 1} \cdots y_0)_2$。不妨 $p \geq q$，并额外约定 $i > q$ 时 $y_i = 0$，定义：

$$
x \oplus y = \sum_{i = 0}^p 2^i(x_i \oplus y_i)
$$

计算 $0 \oplus 1 \oplus \cdots \oplus n (n \in \mathbb{N})$ 的值

## Solution

Subproblem 1.

首先说明存在性，我们使用归纳法说明所有非负整数都存在二进制表示。首先容易看出 $0$ 的二进制表示唯一为 $(0)_2$，$1$ 的二进制表示唯一为 $(1)_2$。

假设任意 $n \leq 2^k - 1, k \in \mathbb{N}$ 的 $n$ 都具有二进制表示，下面证明所有 $n \leq 2^{k + 1} - 1$ 的 $n$ 都具有二进制表示。

显然得知 $n = 2^k$ 有二进制表示 $(100 \cdots 0)_2$。取 $2^k < n \leq 2^{k + 1} - 1$，显然有 $n - 2^k \leq 2^k - 1$，所以根据归纳假设 $n - 2^k$ 具有二进制表示 $(a_pa_{p - 1} \cdots a_0)_2$，根据定义有 $a_p = 1$。显然 $p < k$，否则：

$$
n - 2^k = \sum_{i = 0}^p 2^ia_i \geq 2^p \geq 2^k
$$

矛盾，故 $p < k$。

作二进制表示 $(b_kb_{k - 1} \cdots b_0)_2$，其中 $b_k = 1$，并且 $i \leq p$ 时 $b_i = a_i$，$p < i < k$ 时 $b_i = 0$。显然其满足二进制表示的第一和第三个条件，第二个条件验证如下：

$$
\sum_{i = 0}^k 2^ib_i = 2^k + \sum_{i = 0}^p 2^ia_i = 2^k + (n - 2^k) = n
$$

成立。据归纳法得知所有非负整数都有二进制表示。

之后说明唯一性，我们使用归纳法说明所有非负整数二进制表示唯一。首先容易看出 $0$ 的二进制表示唯一为 $(0)_2$，$1$ 的二进制表示唯一为 $(1)_2$。

假设任意 $n \leq 2^k - 1, k \in \mathbb{N}$ 的 $n$ 二进制表示唯一，下面证明所有 $n \leq 2^{k + 1} - 1$ 的 $n$ 二进制表示唯一。

对于 $2^k \leq n \leq 2^{k + 1} - 1$，假设其具有两个不同的二进制表示 $(a_pa_{p - 1} \cdots a_0)_2, (b_qb_{q - 1} \cdots b_0)_2$，根据定义 $a_p = b_q = 1$。这里不妨令 $p \geq q$。

如果 $p \neq q$，那么我们有：

$$
n = \sum_{i = 0}^q 2^ib_i \leq \sum_{i = 0}^q 2^i = 2^{q + 1} - 1 < 2^p \leq \sum_{i = 0}^p 2^ia_i = n
$$

矛盾，故 $p = q$。

另外，如果 $p > k$，则有：

$$
n = \sum_{i = 0}^p 2^ia_i \geq 2^p \geq 2^{k + 1}
$$

如果 $p < k$，则有：

$$
n = \sum_{i = 0}^p 2^ia_i \leq \sum_{i = 0}^p 2^i = 2^{p + 1} - 1 \leq 2^k - 1
$$

均矛盾，所以 $p = q = k$。

那么 $(a_{k - 1}a_{k - 2} \cdots a_0)_2, (b_{k - 1}b_{k - 2} \cdots b_0)_2$ 应当构成了 $n - 2^k$ 的两个不同的二进制表示，然而 $n - 2^k \leq 2^k - 1$，这与归纳假设矛盾。

据归纳法得知所有非负整数二进制表示唯一。

Subproblem 2.

我们归纳地证明：

$$
0 \oplus 1 \oplus \cdots \oplus n = \begin{cases}
n & n = 4k \\
1 & n = 4k + 1 \\
n + 1 & n = 4k + 2 \\
0 & n = 4k + 3 \\
\end{cases} \ (k \in \mathbb{N})
$$

在此之前，我们需要指出该运算具有以下性质：

$$
0 \oplus n = n, n \oplus n = 0, 2n \oplus 1 = 2n + 1, n \in \mathbb{N}^+
$$

不妨记 $n$ 的二进制表示为 $(a_ka_{k - 1} \cdots a_0)_2$，那么：

$$
\begin{aligned}
0 \oplus n &= \sum_{i = 0}^k 2^k(0 \oplus a_i) = \sum_{i = 0}^k 2^ka_i = n \\
n \oplus n &= \sum_{i = 0}^k 2^k(a_i \oplus a_i) = 0 \\
\end{aligned}
$$

另外考虑二进制表示 $(a_ka_{k - 1} \cdots a_00)_2$，其所代表的正整数为：

$$
\sum_{i = 1}^{k + 1} 2^ia_{i - 1} = 2\sum_{i = 0}^k 2^ia_i = 2n
$$

所以：

$$
2n \oplus 1 = (0 \oplus 1) + \sum_{i = 1}^{k + 1} 2^i(a_{i - 1} \oplus 0) = 1 + 2\sum_{i = 0}^k 2^ia_i = 2n + 1
$$

回到原题，我们首先计算出：

$$
\begin{aligned}
0 &= 0 \\
0 \oplus 1 &= 1 \\
0 \oplus 1 \oplus 2 &= 1 \oplus 2 = (1)_2 \oplus (10)_2 = (11)_2 = 3 \\
0 \oplus 1 \oplus 2 \oplus 3 &= 3 \oplus 3 = (11)_2 \oplus (11)_2 = 0 \\
\end{aligned}
$$

为简化记号，定义 $f(n) := 0 \oplus 1 \oplus \cdots \oplus n$。

假设 $n = 4(k - 1), 4(k - 1) + 1, 4(k - 1) + 2, 4(k - 1) + 3 (k \in \mathbb{N}^+)$ 时结论成立。

下证明 $n = 4k, 4k + 1, 4k + 2, 4k + 3$ 时结论成立。

不难根据先前得到的运算性质运算出：

$$
\begin{aligned}
f(4k) &= 0 \oplus 4k = 4k \\
f(4k + 1) &= 4k \oplus (4k + 1) = 4k \oplus (4k \oplus 1) = (4k \oplus 4k) \oplus 1 = 0 \oplus 1 = 1 \\
f(4k + 2) &= 1 \oplus (4k + 2) = 4k + 3 \\
f(4k + 3) &= (4k + 3) \oplus (4k + 3) = 0 \\
\end{aligned}
$$

