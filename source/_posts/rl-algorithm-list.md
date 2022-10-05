---
title: 强化学习算法与 demo 代码
date: 2022-10-01 02:45:34
category:
    - 【学习笔记】计算机
mathjax: true
---


本文基本基于原先的强化学习入门笔记，但是增加了不少东西，代码也从 Tensorflow 改成 Pytorch。

这篇文章大概会忽略掉不少基础部分的讲解，会更为侧重于说明各个算法的 motivation 和 implementation，可能会涉及到部分数学推理。

<!-- more -->

$$
\newcommand{\Pe}{\mathbb{P}_{\mathcal{E}}}
\newcommand{\P}{\mathbb{P}}
\newcommand{\E}{\mathbb{E}}
\newcommand{\R}{\mathbb{R}}
\newcommand{\S}{\mathcal{S}}
\newcommand{\A}{\mathcal{A}}
\newcommand{\Eop}{\mathop{\mathbb{E}}}
\newcommand{\b}{\boldsymbol}
\newcommand{\argmax}{\mathop{\rm argmax}}
$$

# 强化学习的基本概念与基本优化理论

## 环境与策略

强化学习的基本框架为一个智能体和环境交互，环境对智能体的决策给出反馈，即收益信号并转移到下一状态，智能体需要学会在不同的状态下应该做出何种决策以收获最高总收益。

这里环境常常建模为 Markov 决策过程，即 MDP $(\S, \A, \Pe, r, \gamma)$。所谓 Markov 过程，指的是状态转移和历史状态无关，仅仅和当前状态有关，可以简单描述为 $\P(s_{t + 1} \mid s_t) = \P(s_{t + 1} \mid s_t, s_{t - 1}, \cdots, s_1)$。MDP 中各项指的是：

- 状态空间 $\S$。即环境可以表现出的所有状态构成的集合。
- 决策空间 $\A$。即环境允许智能体做出的所有决策构成的集合。
- 状态转移概率函数 $\Pe$。$\Pe(s' \mid s, a)$ 表示环境在状态 $s \in \S$ 且智能体做出决策 $a \in \A$ 的时候环境下一步转移到状态 $s' \in \S$ 的概率。这里下标 $\mathcal{E}$ 表示该概率仅与环境 $\mathcal{E}$ 本身有关。
- 收益函数 $r$。收益函数为从 $\S \times \A \times \S$ 到 $\R$ 的映射。$r(s, a, s')$ 表示环境在状态 $s \in \S$ 且智能体做出决策 $a \in \A$ 并转移到新状态 $s' \in \S$ 的时候环境给予智能体的收益。
- 收益衰减系数 $\gamma$。这里 $\gamma \in [0, 1]$。该参数代表收益随着时间步衰减的系数，具体含义将会在后续定义累计收益的时候展开。

而智能体内部所使用的策略一般表现为 $\pi_{\b\theta}(a \mid s)$。这里策略 $\pi_{\b\theta}$ 的下标 $\b\theta$ 表示该策略由参数 $\b\theta \in \R^n$ 建模。另外 $\pi_{\b\theta}(a \mid s)$ 表示了智能体在该策略条件下，在状态 $s$ 时做出决策 $a$ 的概率。

上述的策略往往称为**非确定性策略**，因为其对于某个状态给出的是做出某一种决策的概率，是不确定的。而另外一类策略，即**确定性策略**，会对于某个状态给出具体的某一种决策。确定性策略的记号，为了和非确定性策略的 $\pi_{\b\theta}$ 区别，往往使用 $\mu_{\b\theta}$。而 $a := \mu_{\b\theta}(s)$ 就表示了智能体在该策略条件下，在状态 $s$ 时会做出决策 $a$。

一条**轨迹**，或者 **trajectory**，指的是智能体和环境不断交互的过程的记录。具体而言，环境首先位于状态 $s_0$，智能体做出决策 $a_0$，环境返回收益 $r_1$ 后转入新状态 $s_1$。以此类推形成 $s_0, a_0, r_1; s_1, a_1, r_2; \cdots$。轨迹常常使用记号 $\tau$ 表示。

显然根据 Markov 的特性，我们可以得到在策略 $\pi_{\b\theta}$ 的条件下某一条轨迹 $\tau = (s_0, a_0, r_1; s_1, a_1, r_2; \cdots)$ 出现的概率为：

$$
\P_{\b\theta}(\tau) = \Pe(s_0)\prod_{t = 0}^{+\infty} \pi_{\b\theta}(a_t \mid s_t)\Pe(s_{t + 1} \mid s_t, a_t)
$$

这里 $\Pe(s_0)$ 表示环境初始状态为 $s_0$ 的概率。

我们表示轨迹 $\tau$ 服从上述概率分布的时候，可以写成多种符号，包括 $\tau \sim \P_{\b\theta}(\tau)$ 或者 $\tau \sim \pi_{\b\theta}$ 等等，本文会使用第二种表达。

## 累计收益与价值函数

对一条轨迹，**累计收益**的定义为：

$$
R_t(\tau) := \sum_{k = 0}^{+\infty} \gamma^k r_{t + k + 1}
$$

这里注意到我们累加收益的时候需要不断使用衰减因子进行衰减，这一操作主要是为了拟合现实中距离现在越远的行为对当前决策的影响越小的特征。

之后，我们进一步定义**价值函数**。首先需要定义**状态行为价值函数**：

$$
Q^{\pi_{\b\theta}}(s, a) := \Eop_{\tau \sim \pi_{\b\theta}}[R_t(\tau) \mid s_t = s, a_t = a]
$$

可以注意到上述期望中 $t$ 的分布并没有指定，这里一般认为是等概率地选择一个自然数即可。

进一步即可有**状态价值函数**：

$$
V^{\pi_{\b\theta}}(s) := \Eop_{\tau \sim \pi_{\b\theta}}[R_t(\tau) \mid s_t = s]
$$

我们可以发现状态行为价值函数描述了在状态 $s$ 下做出决策 $a$ 后期望的累计收益，也就是说该价值函数评价了给定状态下的某个决策的期望收益，所以我们常常使用状态行为价值函数辅助决策。而状态价值函数则和具体的决策无关，是衡量从某一个状态出发，能够获得的期望收益。

显然这两个价值函数之间存在相互推出关系，而这种关系描述如下：

{% note info no-icon %}
**Theorem 1.01** 对于任何 MDP 与其上的策略 $\pi$，该策略的状态价值函数与状态行为价值函数满足：

$$
\begin{aligned}
{\color{red} V^\pi(s)} &= \sum_{a \in \mathcal{A}} \pi(a \mid s){\color{red} Q^\pi(s, a)} \\
{\color{red} Q^\pi(s, a)} &= \sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V^\pi(s')}] \\
\end{aligned}
$$
{% endnote %}

第一个等式是由 $Q^\pi$ 推出 $V^\pi$ 的路径，证明也是简单的：

$$
\begin{aligned}
V^\pi(s) &= \Eop_{\tau \sim \pi}[R_t(\tau) \mid s_t = s] \\
&= \sum_{\tau} \P_{\b\theta}(\tau \mid s_t = s)R_t(\tau) \\
&= \sum_{\tau} \left(\sum_{a \in \mathcal{A}} \P_{\b\theta}(\tau, a_t = a \mid s_t = s)\right)R_t(\tau) \\
&= \sum_{\tau} \left(\sum_{a \in \mathcal{A}} \P_{\b\theta}(\tau \mid s_t = s, a_t = a)\P_{\b\theta}(a_t = a \mid s_t = s)\right)R_t(\tau) \\
&= \sum_{a \in \mathcal{A}} \P_{\b\theta}(a_t = a \mid s_t = s) \left(\sum_{\tau} \P_{\b\theta}(\tau \mid s_t = s, a_t = a) R_t(\tau)\right) \\
&= \sum_{a \in \mathcal{A}} \pi(a \mid s)\Eop_{\tau \sim \pi}[R_t(\tau) \mid s_t = s, a_t = a] \\
&= \sum_{a \in \mathcal{A}} \pi(a \mid s)Q^\pi(s, a) \\
\end{aligned}
$$

第二个等式是由 $V^\pi$ 推出 $Q^\pi$ 的路径，其涉及到了后续状态，从而实际上描述出了价值函数之间的递推关系。

我们考虑其证明，首先我们显然有这个等式：

$$
r(s, a, s') = \Eop_{\tau \sim \pi}[r_{t + 1} \mid s_t = s, a_t = a, s_{t + 1} = s']
$$

之后考虑对状态行为价值函数的单步展开：

$$
\begin{aligned}
Q^\pi(s) &= \Eop_{\tau \sim \pi}[R_t(\tau) \mid s_t = s, a_t = a] \\
&= \Eop_{\tau \sim \pi}\left[\sum_{k = 0}^{+\infty} \gamma^k r_{t + k + 1} \middle| s_t = s, a_t = a\right] \\
&= \Eop_{\tau \sim \pi}\left[r_{t + 1} + \sum_{k = 1}^{+\infty} \gamma^k r_{t + k + 1} \middle| s_t = s, a_t = a\right] \\
&= \Eop_{\tau \sim \pi}\left[r_{t + 1} + \gamma\sum_{k = 0}^{+\infty} \gamma^k r_{t + k + 2} \middle| s_t = s, a_t = a\right] \\
&= \Eop_{\tau \sim \pi}[r_{t + 1} \mid s_t = s, a_t = a] + \Eop_{\tau \sim \pi}\left[\gamma\sum_{k = 0}^{+\infty} \gamma^k r_{(t + 1) + k + 1} \middle| s_t = s, a_t = a\right] \\
&= \Eop_{\tau \sim \pi}[r_{t + 1} \mid s_t = s, a_t = a] + \gamma\Eop_{\tau \sim \pi}[R_{t + 1}(\tau) \mid s_t = s, a_t = a] \\
\end{aligned}
$$

之后我们使用全期望公式处理条件期望中的条件：

$$
\begin{aligned}
\Eop_{\tau \sim \pi}[r_{t + 1} \mid s_t = s, a_t = a] &= \sum_{s' \in \S} \Pe(s' \mid s, a) \Eop_{\tau \sim \pi}[r_{t + 1} \mid s_t = s, a_t = a, s_{t + 1} = s'] \\
&= \sum_{s' \in \S} \Pe(s' \mid s, a)r(s, a, s') \\
\Eop_{\tau \sim \pi}[R_{t + 1}(\tau) \mid s_t = s, a_t = a] &= \sum_{s' \in \S} \Pe(s' \mid s, a) \Eop_{\tau \sim \pi}[R_{t + 1}(\tau) \mid s_t = s, a_t = a, s_{t + 1} = s'] \\
&= \sum_{s' \in \S} \Pe(s' \mid s, a) \Eop_{\tau \sim \pi}[R_{t + 1}(\tau) \mid s_{t + 1} = s'] \\
&= \sum_{s' \in \S} \Pe(s' \mid s, a)V^\pi(s') \\
\end{aligned}
$$

这里注意到处理第二个条件期望的时候，直接删去了 $s_t = s$ 和 $a_t = a$ 两个条件，这是因为 $R_{t + 1}(\tau)$ 与 $s_t, a_t$ 无关，可以直接删去而不影响最后的期望。

将此代入原有展开式即可证明完毕。

事实上，基于这两个价值函数的互相推出，我们可以得到 $Q^\pi$ 推出 $Q^\pi$ 的公式：

$$
{\color{red} Q^\pi(s, a)} = \sum_{s' \in \S} \Pe(s' \mid s, a) \left[r(s, a, s') + \gamma\sum_{a' \in \A} \pi(a' \mid s'){\color{red} Q^\pi(s', a')}\right]
$$

以及 $V^\pi$ 推出 $V^\pi$ 的公式：

$$
{\color{red} V^\pi(s)} = \sum_{a \in \A} \pi(a \mid s) \sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V^\pi(s')}]
$$

这两个公式直接表现出了价值函数自身具有的迭代结构，也就是当前强化学习中最为重要的方程之一，目前大部分强化学习方法的核心就是拟合该方程。该方程即 **Bellman 方程（Bellman Equation）**。

## 基本优化理论

现在我们讨论如何获取一个最有价值的策略。

首先，我们定义**最优状态行为价值函数**和**最优状态价值函数**：

$$
\begin{aligned}
Q^\star(s, a) &:= \max_\pi Q^\pi(s, a) \\
V^\star(s) &:= \max_\pi V^\pi(s) \\
\end{aligned}
$$

基于最优性，我们可以定义策略之间的序。我们称策略 $\pi$ 优于 $\pi'$，记作 $\pi \geq \pi'$，当且仅当对于所有 $s \in \S$ 都满足 $\pi$ 的状态函数优于 $\pi'$，即 $V^\pi(s) \geq V^{\pi'}(s)$。

我们有下述基本优化理论：

{% note info no-icon %}
**Theorem 1.02** 对于任何 MDP，都存在一个最优策略 $\pi^\star$ 满足对于任何 $\pi$ 都有 $\pi^\star \geq \pi$。并且该最优策略 $\pi^\star$ 在在任何状态与任何决策下均实现了最优价值，即对于任何 $s \in \S, a \in \A$ 都有 $V^{\pi^\star}(s) = V^\star(s), Q^{\pi^\star}(s, a) = Q^\star(s, a)$。
{% endnote %}

本文不加证明地认为该理论成立。另外，这里给出最优策略的构造。可见最优策略是确定性策略：

$$
\pi^\star(a \mid s) := \begin{cases}
1 & a = \argmax_{a \in \A} Q^\star(s, a) \\
0 & \text{otherwise}
\end{cases}
$$

基于该定理我们能够得到两个最优价值函数之间存在的关系：

{% note info no-icon %}
**Theorem 1.03** 对于任何 MDP，其最优状态价值函数与最优状态行为价值函数满足：

$$
\begin{aligned}
{\color{red} V^\star(s)} &= \max_{a \in \mathcal{A}} {\color{red} Q^\star(s, a)} \\
{\color{red} Q^\star(s, a)} &= \sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V^\star(s')}] \\
\end{aligned}
$$
{% endnote %}

第一个等式是简单的：

$$
V^\star(s) = V^{\pi^\star}(s) = \sum_{a \in \A} \pi^\star(a \mid s)Q^{\pi^\star}(s, a) = \max_{a \in \A} Q^{\pi^\star}(s, a) = \max_{a \in \A} Q^\star(s, a)
$$

第二个等式直接对 $\pi^\star$ 应用一般的 Bellman 方程即可得到。

从而还有下述**最优 Bellman 方程（Bellman Optimality Equation）**：

$$
{\color{red} Q^\star(s, a)} = \sum_{s' \in \S} \Pe(s' \mid s, a) \left[r(s, a, s') + \gamma\max_{a' \in \A} {\color{red} Q^\star(s', a')}\right]
$$

以及：

$$
{\color{red} V^\star(s)} = \max_{a \in \A} \left[\sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V^\star(s')}]\right]
$$

理论上我们可以求解最优 Bellman 方程得到 $V^\star$，从而就能够反推出 $\pi^\star$。然而求解最优 Bellman 方程是计算困难的，现行的强化学习方法就是在尝试近似求解最优 Bellman 方程。

# Dynamic Programming (DP)

## Policy Iteration (PI)

如果我们已经对环境有了完整的建模，我们完全可以使用迭代的方式求解最优 Bellman 方程。

具体的迭代过程分为两步，分别为**策略评估（Policy Evaluation）**和**策略提升（Policy Improvement）**，这两步会交替进行。具体而言，策略评估是在已知 $\pi$ 的条件下迭代计算其状态价值函数 $V^\pi$。而策略提升为在得知状态价值函数的基础上优化策略 $\pi$。

首先阐述策略评估的迭代过程。

我们记 $V_k$ 是第 $k$ 轮迭代的时候的状态价值函数，那么我们在初始化 $V_0$ 之后不断进行下述迭代直到状态价值函数收敛：

$$
V_{k + 1}(s) \leftarrow \sum_{a \in \A} \pi(a \mid s) \sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma V_k(s')]
$$

收敛后，应当有 $V_{+\infty} = V^\pi$，此时我们就得到了策略 $\pi$ 所对应的价值函数。

之后阐述策略提升的方法。

我们在得知了策略 $\pi$ 的状态价值函数 $V^\pi$ 之后，我们可以贪心地构造一个确定性策略 $\pi'$，保证策略更优。保证这一点能够成立的是下述定理：

{% note info no-icon %}
**Theorem 2.01 (Policy Improvement Theorem)** 对于已知的策略 $\pi$，定义确定性策略 $\pi'$：

$$
\pi'(a \mid s) := \begin{cases}
1 & a = \argmax_{a \in \A} Q^\pi(s, a) \\
0 & \text{otherwise}
\end{cases}
$$

那么 $\pi' \geq \pi$，并且等号成立当且仅当 $\pi = \pi' = \pi^\star$。
{% endnote %}

证明是简单的，首先我们可以注意到：

$$
Q^\pi(s, \pi'(s)) = \max_{a \in \A} Q^\pi(s, a) \geq \sum_{a \in \A} \pi(a \mid s)Q^\pi(s, a) = V^\pi(s)
$$

我们从而能得到下述不等式。这里事实上进行了单步展开，进一步就可以尝试利用迭代结构构造不等式链，具体细节则在后续证明中说明：

$$
\begin{aligned}
V^\pi(s) &\leq Q^\pi(s, \pi'(s)) \\
&= \sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))[r(s, \pi'(s), s') + \gamma V^\pi(s')] \\
&= \sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))r(s, \pi'(s), s') + \gamma\sum_{s' \in \S} \Pe(s' \mid s, \pi'(s)) V^\pi(s') \\
\end{aligned}
$$

另外，我们可以证明：

$$
\begin{aligned}
&\sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))r(s, \pi'(s), s') \\
=& \sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))\Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s, a_t = \pi'(s), s_{t + 1} = s'] \\
=& \sum_{a \in \A} \pi'(a \mid s)\sum_{s' \in \S} \Pe(s' \mid s, a)\Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s, a_t = a, s_{t + 1} = s'] \\
=& \Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s]
\end{aligned}
$$

即得到：

$$
V^\pi(s) \leq \Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s] + \gamma\sum_{s' \in \S} \Pe(s' \mid s, \pi'(s)) V^\pi(s')
$$

对这个不等式迭代展开：

$$
\begin{aligned}
V^\pi(s) &\leq \Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s] + \gamma\sum_{s' \in \S} \Pe(s' \mid s, \pi'(s)) V^\pi(s') \\
&\leq \Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s] + \gamma\sum_{s' \in \S} \Pe(s' \mid s, \pi'(s)) \left[\Eop_{\tau \sim \pi'}[r_{t + 2} \mid s_{t + 1} = s'] + \gamma\sum_{s'' \in \S} \Pe(s'' \mid s', \pi'(s')) V^\pi(s'')\right] \\
&= \Eop_{\tau \sim \pi'}[r_{t + 1} \mid s_t = s] + \gamma{\color{red} \sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))\Eop_{\tau \sim \pi'}[r_{t + 2} \mid s_{t + 1} = s']} + \gamma^2\sum_{s' \in \S}\sum_{s'' \in \S}\Pe(s'' \mid s', \pi'(s')) V^\pi(s'') \\
\end{aligned}
$$

对于红色部分，我们容易发现 $s_t = s$ 对于 $r_{t + 2}$ 的期望是无效条件，所以：

$$
\sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))\Eop_{\tau \sim \pi'}[r_{t + 2} \mid s_{t + 1} = s'] = \sum_{s' \in \S} \Pe(s' \mid s, \pi'(s))\Eop_{\tau \sim \pi'}[r_{t + 2} \mid s_t = s, s_{t + 1} = s'] = \Eop_{\tau \sim \pi'}[r_{t + 2} \mid s_t = s]
$$

所以：

$$
V^\pi(s) \leq \Eop_{\tau \sim \pi'}[{\color{red} r_{t + 1} + \gamma r^{t + 2}} \mid s_t = s] + {\color{green} \gamma^2\sum_{s' \in \S}\sum_{s'' \in \S}\Pe(s'' \mid s', \pi'(s')) V^\pi(s'')}
$$

我们接下来展开绿色部分的时候，可以注意到我们需要为 $r_{t + 3}$ 的条件期望补上 $s_t = s$ 和 $s_{t + 1} = s'$ 两个无效条件，然后两个求和号通过全期望公式消去 $s_{t + 1} = s'$ 以及 $s_{t + 2} = s''$ 两个条件，故剩余的条件仅有 $s_t = s$，从而可以进一步合并到红色部分。以此类推，我们就能够说明该不等式的迭代展开是可行的，从而得到：

$$
V^\pi(s) \leq \Eop_{\tau \sim \pi'}[{\color{red} r_{t + 1} + \gamma r^{t + 2} + \gamma^2r_{t + 3} + \cdots} \mid s_t = s] = V^{\pi'}(s)
$$

从而欲证明的不等式成立。

而根据证明过程，该不等式取等当且仅当满足 $V^\pi(s) = Q^\pi(s, \pi'(s)) = \max_{a \in \A}Q^\pi(s, a)$，而这正是最优 Bellman 方程。也就是说我们使得 $V^\pi = V^\star$。此外，$\pi$ 目前也必须是一个确定性策略，从而也就保证了 $\pi = \pi^\star$。

综合策略评估和策略提升，交替进行两者我们就可以保证能够获取到最优策略。然而这个方法并不现实，因为理论上我们需要对环境有完全把握，至少我们需要得到整个环境状态转移矩阵才能进行。

## Value Iteration (VI)



# Q-learning

Q-learning 是 off-policy 的、value-based 的、