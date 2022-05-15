---
title: 《Foundations of Probabilistic Proofs》学习笔记
date: 2022-05-13 00:29:19
category:
    - 科研杂谈
mathjax: true
---

大概是打算去做量子计算方向需要学习的一些东西，这门课是 Berkeley 的 CS294 课程，是理论计算机的一个重要课程，和量子计算关系还是挺大的。

但这门课也太难了，我就听了 Lecture 1 就学到了一堆不会的东西，听两分钟查半小时维基百科和各路资料，太折磨了。

<!-- more -->

$$
\newcommand{\s}{\mathsf}
\newcommand{\la}{\langle}
\newcommand{\ra}{\rangle}
\newcommand{\pr}{\mathop{\mathrm{Pr}}}
$$

# Lecture 1 Graph Non-isomorphism & IP Upper Bound

## Classical Mathematical Proofs

首先回忆一下 NP 语言的定义：

We call language $L$ an $\s{NP}$ if and only if $\exists$ polynomial decider $D$ s.t.

- $\forall$ instance $x \in L$, $\exists$ witness $w$ s.t. $D(x, w) = 1$.

- $\forall$ instance $x \notin L$, $\forall$ witness $w$ s.t. $D(x, w) = 0$.

NP 语言指的是能够在多项式时间内验证的语言，其全称应当为 **N**on-deterministic **P**olynomial，即非确定性多项式时间，而非 **N**on-**P**olynomial，即非多项式时间。

一个 NP 语言的例子为 $L = \#\s{SAT}$。

对于 $x \in L$，$x$ 均为 $n$ 元布尔表达式 $x = \phi(x_1, x_2, x_3, \cdots, x_n)$。这里我们取 witness 为一个赋值 $(a_1, a_2, a_3, \cdots, a_n) \in \{0, 1\}^n$，而 decider 判定 $\phi(a_1, a_2, a_3, \cdots, a_n)$ 是否为真。

---

我们改动上述定义中的名称，用 verifier $V$ 代替 decider $D$，用 theorem $x$ 代替 instance $x$，用 proof $\pi$ 代替 witness $w$。那么 NP 语言的两条约束就是说明：

- (Completeness) $\forall$ theorem $x \in L$, $\exists$ proof $\pi$ s.t. $V(x, \pi) = 1$, which means every true theorem has a proof.

- (Soundness) $\forall$ theorem $x \notin L$, $\forall$ proof $\pi$ s.t. $V(x, \pi) = 0$, which means every false theorem has no proof to convince the verifier.

这就说明当下的 NP 语言实际上描述了 classical mathematical proofs。

经典的数学证明器包含一个 prover $P$ 和一个 verifier $V$，prover 接受 theorem $x$，生成一个 proof $\pi$ 传递给 verifier，而 verifier 根据 $\pi$ 输出一个 bit 表示其是否认同该证明。

而本课程需要做的，是为这个系统引入下述新特征：

The verifier $V$ may

- use randomness

- interact with prover $P$

## Interactive Proofs

交互证明中的 prover 和 verifier 可以交互，即两者之间可以进行若干次的通信，相互交换信息。同时，verifier 可能会使用 randomness 来决定最后的输出。

交互式证明的定义为：

An interactive proof for language $L$ is a pair $(P, V)$, where $P$ is a prover and $V$ is an honest probilistic polynomial verifier, s.t.

- (Completeness) $\forall$ theorem $x \in L$, $\pr_r[\la P(x), V(x; r) \ra = 1] = 1$, which means the probability that every true theorem has a proof (there exists an honst prover) to convince the verifier is $1$.

- (Soundness) $\forall$ theorem $x \notin L$, $\forall \tilde{P}$, $\pr_r[\la \tilde{P}, V(x; r) \ra = 1] \leq \dfrac 12$, which means for every possible prover, the probability that the proof it gives for a false theorem can convince the verifier will not exceed half.

注意，这里我们不对 honest prover 引入 randomness。

---

这里我们引入了两个新概念，即 interaction 和 randomness。我们关注下表：

|  | With interaction | Without interaction |
| :-: | :-: | :-: |
| With randomness | $\s{IP}$ (Interaction Proof) | $\s{MA}$ (Merlin Arthur Protocol) |
| Without randomness | $\s{NP}$ | $\s{NP}$ |


如果 interaction 和 randomness 都没有引入 NP，那么 NP 还是 NP。

如果仅仅引入 interaction，得到的系统依然等价于 NP（证明留做习题）。

如果仅仅引入 randomness，我们得到了 MA 协议。The MA Protocol is believed to be equal to $\s{NP}$.

如果两者都引入，就得到了交互证明 IP。

## IP for Graph Non-isomorphism

首先给出图同构的定义。对于图 $G_0 = (V, E_0)$ 以及 $G_1 = (V, E_1)$，称两图同构（isomorphism）当且仅当存在 $V$ 上的置换 $\pi: V \to V$ 令 $(u, v) \in E_0$ 等价于 $(\pi(u), \pi(v)) \in E_1$。此时记 $G_1 = \pi(G_0)$。

两图同构的记号为 $G_0 \equiv G_1$。

那么定义语言：

$$
\begin{aligned}
\s{GI} &:= \{(G_0, G_1) : G_0 \equiv G_1\} \\
\s{GNI} &:= \{(G_0, G_1) : G_0 \not\equiv G_1\} \\
\end{aligned}
$$

显然对同构的两个图只要给出 $V$ 上的置换 $\pi$ 作为 proof 即可判定图是否同构，故我们有 $\s{GI} \in \s{NP}$，那么 $\s{GNI} \in \s{coNP}$。这里：

$$
\s{coNP} := \{L^c : L \in \s{NP}\}
$$

---

下面我们证明 $\s{GNI} \in \s{IP}$。

事实上我们可以给出这样的 prover 和 verifier，其工作流程为：

- verifier 随机令 $b \leftarrow \{0, 1\}$
- verifier 随机获取 $V$ 上的一个置换 $\pi: V \to V$
- verifier 令 $H := \pi(G_b)$，并将 $H$ 传递给 prover
- prover 计算 $\tilde{b}$，令 $G_{\tilde{b}} \equiv H$，并将 $\tilde{b}$ 传递给 verifier
- verifier 判定 $\tilde{b}$ 是否等于 $b$，若相等，则输出 $1$，否则输出 $0$

我们证明这个系统满足 Completeness 和 Soundness 的要求。

考虑到图同构关系为等价关系，所以可以构建图同构关系对应的等价类集合。

首先如果 $(G_0, G_1) \in \s{GNI}$，则说明 $G_0, G_1$ 位于不同的等价类 $P_0, P_1$。verifier 通过随机获取置换的方式，可以得到一个与 $G_b$ 位于同一个等价类的图 $H$。而 prover 总是能够分辨 $H$ 和 $G_0, G_1$ 中的哪个位于同一等价类，所以总是能够使得 $\tilde{b} = b$，Completeness 得证。

其次如果 $(G_0, G_1) \notin \s{GNI}$，则说明 $G_0, G_1$ 位于同一个等价类，并且 $H$ 也位于这个等价类。此时考虑到 $H, b$ 相互独立，得到 prover 令 $\tilde{b} := b$ 和 $\tilde{b} := 1 - b$ 的概率等同，所以错判概率不超过一半，Soundness 得证。

从而得知 $\s{GNI} \in \s{IP}$。

{% note info %}
现在我们暂且忽略 prover 的计算复杂度。
{% endnote %}

这个证明的核心思想在于**图同构关系为等价关系**。

## An Upper Bound on IP

我们下面证明 $\s{IP} \subseteq \s{PSPACE}$。