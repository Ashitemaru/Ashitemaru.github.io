---
title: 《应用近世代数》学习笔记
date: 2024-03-06 01:04:35
category:
    - 【学习笔记】数学
mathjax: true
---

自学笔记，那就是自学笔记，只记一些比较主干的定理和证明，或许时不时穿插点自己的感想。

<!-- more -->

# 引言与预备知识

## 集合与映射

集合所采用的符号和先前并无太大差距，除了余集（补集）本书采用的符号为：

$$
A' = \overline{A} := U\backslash A
$$

对称差采用的符号为：

$$
A\triangle B := (A\backslash B) \cup(B\backslash A)
$$

集合的部分运算性质 trivial，略去。

{% note info no-icon %}
**Theorem 1.1.1 (Inclusion & Exclusion Principle)** 容斥原理表述为：

$$
\begin{aligned}
\left|\bigcup_{i = 1}^n A_i\right| &= \sum_{k = 1}^n (-1)^{k-1}\sum_{1\leq i_1 < i_2 < \cdots < i_k\leq n}\left|\bigcap_{j = 1}^k A_{i_j}\right| \\
&= \sum_{i = 1}^n |A_i| - \sum_{1\leq i < j\leq n}|A_i\cap A_j| + \sum_{1\leq i < j < k\leq n}|A_i\cap A_j\cap A_k| - \cdots + (-1)^{n-1}\left|\bigcap_{i = 1}^n A_i\right|
\end{aligned}
$$
{% endnote %}

证明据对 $n$ 的归纳法平凡，略去。

映射相关的基本定义和概念与先前一致。

{% note success no-icon %}
**Definition 1.1.1** 若集合 $A, B$ 间存在一个双射，则称 $A, B$ **等势**。与自然数集 $\mathbb{N}^+$ 等势的无限集称为**可数集**，否则称为**不可数集**。
{% endnote %}

{% note info no-icon %}
**Theorem 1.1.2** 已知映射 $f: A \to B$，我们有：

- $f$ 有左逆等价于 $f$ 是单射
- $f$ 有右逆等价于 $f$ 是满射
- $f$ 有逆等价于 $f$ 是双射
{% endnote %}

证明从略。

## 二元关系

集合 $A, B$ 上的一个二元关系 $R$ 是笛卡尔积 $A \times B$ 的子集，$a \in A, b \in B$ 具有关系 $R$ 定义为 $(a, b) \in R$。

等价关系、等价类、代表元的定义从略，不难有任何集合上的等价关系与集合的划分一一对应。

{% note success no-icon %}
**Definition 1.1.2** 已知集合 $A$ 上的等价关系 $\sim$，定义 $A$ 对 $\sim$ 的商集为等价类构成的集合：

$$
A/\sim := \{\overline{a} : a \in A\}
$$
{% endnote %}

{% note success no-icon %}
**Definition 1.1.3 (Partially & Totally Ordered Set)** 如果集合 $S$ 上存在一个二元关系 $\leq$ 满足：

- 对任意 $x \in S$ 都有 $x \leq x$
- 对任意 $x, y \in S$，只要 $x \leq y$ 且 $y \leq x$ 就有 $x = y$
- 对任意 $x, y, z \in S$，只要 $x \leq y$ 且 $y \leq z$ 就有 $x \leq z$

则称 $\leq$ 是 $S$ 上的一个**偏序**，$(S, \leq)$ 为**偏序集**。

如果偏序集 $(S, \leq)$ 还满足：

- 对任意 $x, y \in S$ 都有 $x \leq y$ 或 $y \leq x$

则称 $\leq$ 是 $S$ 上的一个**全序**，$(S, \leq)$ 为**全序集**。
{% endnote %}

一个偏序集总是可以用 Hasse 图表示，Hasse 图是一个无向图，其顶点集为 $S$，边集为 $\{(x, y) : x < y, (\not\exists u)x < u < y\}$。

{% note success no-icon %}
**Definition 1.1.4** 在偏序集 $(S, \leq)$ 上定义：

- 若存在 $a \in S$ 满足任意 $x \in S$ 都有 $x \leq a$，则 $a$ 是 $S$ 的**最大元**，同理定义**最小元**
- 若存在 $a \in S$ 满足只要 $x \geq a$ 就有 $x = a$，则 $a$ 是 $S$ 的**极大元**，同理定义**极小元**
- 对 $T \subseteq S$，若存在 $a \in S$ 满足任意 $x \in T$ 都有 $x \leq a$，则 $a$ 是 $T$ 的**上界**，同理定义**下界**
- 对 $T \subseteq S$，若存在 $T$ 的某个上界 $a$，令 $T$ 的任意上界 $a'$ 都有 $a \leq a'$，则 $a$ 是 $T$ 的**最小上界**，同理定义**最大下界**
{% endnote %}

{% note success no-icon %}
**Definition 1.1.5 (Well Ordered Set)** 如果全序集 $(S, \leq)$ 满足对任意 $T \subseteq S, T \neq \varnothing$ 都有 $T$ 具有最小元，那么该全序集为**良序集**。
{% endnote %}

整数集 $\mathbb{Z}$ 在一般的整数序意义上不良序，例如取负整数集 $\mathbb{Z}^- \subseteq \mathbb{Z}$，而负整数集不具有最小元。

良序性保证了数学归纳法的有效性，或者说数学归纳法可以应用在任意良序集上：

{% note info no-icon %}
**Theorem 1.1.3** 已知 $M \subseteq \mathbb{Z}^+$，若有 $1 \in M$，并且只要有 $n - 1 \in M$ 就有 $n \in M$，那么 $M = \mathbb{Z}^+$。
{% endnote %}

【证明】利用反证法，据 $\mathbb{Z}^+$ 的良序性，取 $N = \mathbb{Z}^+\backslash M \neq\varnothing$，$N$ 具有最小元 $a$。由 $1 \in M$ 得到 $a \neq 1$，所以 $a - 1 \in \mathbb{Z}^+$。据 $a - 1 < a$ 和 $a$ 在 $N$ 中的最小性得到 $a - 1 \in M$，因而得到 $a \in M$，矛盾。$\blacksquare$

## 整数与同余方程

整数的带余除法、素因子分解这里不赘述。

{% note info no-icon %}
**Theorem 1.1.4 (Bezout Theorem)** 对不全为零的整数 $a, b$，其最大公因数为 $d$，那么存在 $p, q\in\mathbb{Z}$ 令 $pa + qb = d$。
{% endnote %}

证明方式为证明 $\min\{pa + qb \in \mathbb{Z}^+: p, q \in \mathbb{Z}\} = d$，这里略去。

我感觉本质就在 $a\mathbb{Z} + b\mathbb{Z}$ 依然是整数的子群，而 $d$ 是 $a\mathbb{Z} + b\mathbb{Z}$ 的最小正元素。

{% note success no-icon %}
**Definition 1.1.6 (Euler Function)** 对正整数 $n$，定义 $\varphi(n)$ 为小于 $n$ 且与 $n$ 互素的正整数的个数。不难据容斥原理计算得到：

$$
\varphi(n) = n\prod_{p\mid n}\left(1 - \frac{1}{p}\right)
$$
{% endnote %}

{% note info no-icon %}
**Theorem 1.1.5** 一次同余方程 $ax \equiv b (\mathop{\rm mod} m), m \nmid a$ 有解的充要条件为 $(a, m) \mid b$。
{% endnote %}

【证明】若方程有解 $x$，则存在 $y \in \mathbb{Z}$ 有 $ax + ym = b$，据 Bezout 定理得到 $(a, m) \mid b$。

若 $(a, m) \mid b$，分别记 $a = a_1(a, m), m = m_1(a, m), b = b_1(a, m)$，这里我们显然有 $(a_1, m_1) = 1$，从而据 Bezout 定理得到存在 $r, s \in \mathbb{Z}$ 有 $ra_1 + sm_1 = 1$，从而 $ra_1b_1 + sm_1b_1 = b_1$，即：

$$
ra_1b_1 \equiv b_1 (\mathop{\rm mod} m_1)
$$

考虑原方程，根据下述等价推理：

$$
\begin{aligned}
&ax \equiv b(\mathop{\rm mod} m) \\
\iff &a_1(a, m)x - b_1(a, m) = km_1(a, m), k \in \mathbb{Z} \\
\iff &a_1x - b_1 = km_1, k \in \mathbb{Z} \\
\iff &a_1x \equiv b_1(\mathop{\rm mod} m_1) \\
\end{aligned}
$$

从而我们已经得到了原方程的一个解 $x = rb_1$，从而我们可以得到原方程的所有解 $x = rb_1 + lm_1$，这里 $l \in \mathbb{Z}$。$\blacksquare$

{% note info no-icon %}
**Theorem 1.1.6 (Chinese Remainder Theorem)** 同余方程组

$$
\begin{cases}
x\equiv a_1(\mathop{\rm mod} m_1) \\
x\equiv a_2(\mathop{\rm mod} m_2) \\
\vdots \\
x\equiv a_n(\mathop{\rm mod} m_n)
\end{cases}
$$

满足 $m_i$ 两两互素，那么其解的形式为：

$$
x \equiv \sum_{i = 1}^n a_ic_iM_i(\mathop{\rm mod} M)
$$

其中 $M = m_1m_2\cdots m_n$，并且 $M_i = \dfrac{M}{m_i}$，并且 $c_i$ 是同余方程 $M_ix \equiv 1(\mathop{\rm mod} m_i)$ 的一个解。
{% endnote %}

【证明】首先对任意满足：

$$
x \equiv \sum_{i = 1}^n a_ic_iM_i(\mathop{\rm mod} M)
$$

的 $x$，我们逐一验证其满足原同余方程：

$$
x = kM + \sum_{i = 1}^n a_ic_iM_i = m_p\left(kM_p + \sum_{i \neq p}a_ic_i\frac{M_i}{m_p}\right) + a_pc_pM_p \equiv a_pc_pM_p(\mathop{\rm mod} m_p)
$$

据 $M_pc_p \equiv 1(\mathop{\rm mod} m_p)$ 即验证完毕。

之后证明同余方程组任意解都满足上述形式。取任意一个解 $y$，由于我们验证了所有满足上述形式的 $x$ 都是方程组的解，我们任取其中一个 $x$，总有 $x \equiv y (\mathop{\rm mod} m_p) \iff x - y \mid m_p$。据 $m_p$ 之间互素，不难得到 $x - y \mid M$，证明完毕。