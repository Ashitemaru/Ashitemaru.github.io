---
title: 《组合数学》复习笔记
date: 2024-01-09 02:37:24
mathjax: true
category:
    - 【学习笔记】数学
---

写一些复习《组合数学》的时候的一些想法，大概也没什么用，但总之总比什么都不留下好那么一点点。

<!-- more -->

# 加法乘法原理与简单计数

这部分大概是考不到什么复杂的计数问题的，其实主要就考一个会不会构造模型化归到基础问题上的能力，这里大概记一些基本模型。

{% note success no-icon %}
**Theorem 1** 不定方程 $x_1 + x_2 + \cdots + x_n = m$ 的非负整数解的个数为 $C_{m + n - 1}^m$。
{% endnote %}

{% note info no-icon %}
**Thought 1** “不相邻”这个约束条件往往有两种解决方式。例如从 $\{1, 2, \cdots, n\}$ 中不相邻地取 $m$ 个数。

- 记取出 $a_1 < a_2 < \cdots < a_m$，则 $a_1 < a_2 - 1 < \cdots < a_m - m + 1$ 是 $\{1, 2, \cdots, n - m + 1\}$ 的组合，从而方案数即为 $C_{n - m + 1}^m$
- 记取出 $a_1 < a_2 < \cdots < a_m$，用 $a_{k + 1} - a_k \geq 2$ 将之转化为不定方程解个数的问题（比较麻烦，不建议）
{% endnote %}

# 鸽巢原理

{% note success no-icon %}
**Theorem 2** 给定正整数 $a_1, a_2, \cdots, a_n$，有 $a_1 + a_2 + \cdots + a_n - n + 1$ 只鸽子进入 $n$ 个鸽巢中，那么必然存在一个 $k$ 使得第 $k$ 个鸽巢中至少有 $a_k$ 只鸽子。

令 $a_1 = a_2 = \cdots = a_n = 2$，则得到基本的鸽巢原理版本。即，有 $n + 1$ 只鸽子进入 $n$ 个鸽巢中，那么必然存在一个鸽巢中至少有 $2$ 只鸽子。
{% endnote %}

{% note info no-icon %}
**Thought 2** 前缀和构造大概是要好好记住的，遇到子序列和，前缀和几乎是唯一的构造方式。
{% endnote %}

# 母函数

考虑多重集合 $A = \{a_1, a_2, \cdots, a_n\}$，其中 $a_i$ 的个数为 $m_i$，则 $A$ 的母函数为：

$$
f(x) = \prod_{i = 1}^n (1 + x^{a_i} + x^{2a_i} + \cdots + x^{m_ia_i})
$$

母函数的作用在于回答“从 $A$ 中取出若干数令其和为 $N$ 有多少种取法”这一问题，这一问题的答案为 $f(x)$ 中 $x^N$ 的系数。证明是显然的，这里略去。

{% note info no-icon %}
**Thought 3** 好好算多项式长除法。
{% endnote %}

除了上述定义，母函数还有一种定义是应用在数列 $\{a_n\}$ 上，其母函数定义为其幂级数：

$$
f(x) = \sum_{n = 0}^\infty a_nx^n
$$

{% note info no-icon %}
**Thought 4** 你说多重集的母函数和数列的母函数到底是不是一个东西呢？我觉得不是，因为多重集的母函数充其量是一种把枚举法写得更数学一点的方式。
{% endnote %}

数列的母函数的一大作用是给定数列递推关系的时候可以利用递推关系得到其母函数的方程，从而得到母函数的表达式，从而得到数列的通项公式。

{% note info no-icon %}
**Thought 5** 数列求和、数乘就是母函数的加法和数乘：

$$
\begin{aligned}
f_{a_n + b_n}(x) &= f_{a_n}(x) + f_{b_n}(x) \\
f_{ka_n}(x) &= kf_{a_n}(x)
\end{aligned}
$$

数列下标加减就是母函数的乘法（这里 $a_n = 0, n < 0$）：

$$
f_{a_{n - k}}(x) = x^kf_{a_n}(x)
$$

数列前缀和数列的母函数为：

$$
f_{S_n}(x) = \frac{f_{a_n}(x)}{1 - x}
$$

母函数的导数和积分也会有其对应的序列：

$$
\begin{aligned}
f_{na_n}(x) &= xf_{a_n}'(x) \\
f_{\frac{a_n}{1 + n}}(x) &= \frac{1}{x}\int_0^x f_{a_n}(u){\rm d}u
\end{aligned}
$$

母函数相乘等于序列卷积：

$$
f_{(a * b)_n}(x) = f_{a_n}(x)f_{b_n}(x), (a * b)_n := \sum_{i = 0}^n a_ib_{n - i}
$$
{% endnote %}

得到母函数之后返还到幂级数的方式比较有限（毕竟母函数方法也不是万能的），基本都是想办法分离为部分分式（部分分式指的是分子为常数，分母为线性式的分式）的和形式。这个拆分方式就是对分母因式分解后待定系数法。例如：

$$
f(x) = \frac{x}{(1 - x)(1 - 2x)} = \frac{A}{1 - x} + \frac{B}{1 - 2x}
$$

然后部分分式返还到等比数列无穷求和的形式，就可以得到幂级数形式了。

{% note info no-icon %}
**Thought 5** Fibonacci 数列 $\{F_n\}$ 的平方和是可以简单表述的：

$$
\sum_{i = 1}^n F_i^2 = F_nF_{n + 1}
$$
{% endnote %}

用母函数很容易得到线性常系数递推关系存在一套通用解法，我相信大家高中都练习过也都知道这是什么东西，这里就不赘述了。只需要记住母函数和特征多项式存在下述关系就行：

{% note success no-icon %}
**Theorem 3** 数列 $\{a_n\}$ 是线性常系数递推的，其特征多项式为 $C(x)$，次数为 $n$，则该数列的母函数化简后，分母为 $x^nC\left(\dfrac1x\right)$，分子则取决于数列的初始项。
{% endnote %}

除了上述基本的线性形式的母函数，还有指数形式母函数。定义就是将 $x^n$ 换成 $\dfrac{x^n}{n!}$，之后使用系数的时候需要乘以 $n!$。做这种形式的母函数的化简就需要用到 $e^x$ 的 Taylor 展开，而非线性形式的等比数列求和。

{% note info no-icon %}
**Thought 6** 错排数 $D_n$ 的递推公式为：

$$
D_n = (n - 1)(D_{n - 1} + D_{n - 2})
$$

通项公式为：

$$
D_n = n!\sum_{i = 0}^n \frac{(-1)^i}{i!}
$$
{% endnote %}

有关指数形式母函数和 Stirling 数的一些东西后面再来补充。

## 容斥原理

{% note success no-icon %}
**Theorem 4** 设 $A_1, A_2, \cdots, A_n$ 是有限集合，$S = \bigcup_{i = 1}^n A_i$，则：

$$
\left|\bigcup_{i = 1}^n A_i\right| = \sum_{i = 1}^n |A_i| - \sum_{1 \leq i < j \leq n} |A_i \cap A_j| + \cdots + (-1)^{n - 1}|A_1 \cap A_2 \cap \cdots \cap A_n|
$$
{% endnote %}

{% note info no-icon %}
**Thought 7** Euler 函数 $\varphi(n)$ 表示小于等于 $n$ 且与 $n$ 互质的正整数的个数。那么有：

$$
\varphi(n) = n\prod_{p | n}\left(1 - \frac{1}{p}\right)
$$
{% endnote %}

## 群论与 Polya 定理

{% note success no-icon %}
**Theorem 5** Burnside 引理指的是，设 $G$ 是一个有限群，$X$ 是一个有限集合，$G$ 作用在 $X$ 上，对于 $g \in G$，记 $X^g$ 为 $X$ 中在 $g$ 作用下不变的元素的集合，则：

$$
|X / G| = \frac{1}{|G|}\sum_{g \in G}|X^g|
$$
{% endnote %}

{% note success no-icon %}
**Theorem 6** Polya 定理指的是，设 $G$ 是 $X$ 上的置换群，对于 $g \in G$，$c(g)$ 为 $g$ 的循环节个数，则若使用 $m$ 种颜色对 $X$ 的元素染色，染色方案数为：

$$
N = \frac{1}{|G|}\sum_{g \in G}m^{c(g)}
$$
{% endnote %}