---
title: 强化学习入门笔记
date: 2023-02-22 17:39:55
category:
    - 【学习笔记】计算机
mathjax: true
---

这学期选了一门深度强化学习，也正好趁此机会系统性质地把强化学习理论基础过一下，如果可以的话可能还打算过一下深度学习基础扩展一下视野之类的。

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
- 状态转移概率函数 $\Pe$。$\Pe(s' \mid s, a)$ 表示环境在状态 $s \in \S$ 且智能体做出决策 $a \in \A$ 的时候环境下一步转移到状态 $s' \in \S$ 的概率。这里下标 $\mathcal{E}$ 表示该概率仅与环境 $\mathcal{E}$ 本身有关。其一般称为 model 或者 dynamics。
- 收益函数 $r$。收益函数为从 $\S \times \A \times \S$ 到 $\R$ 的映射。$r(s, a, s')$ 表示环境在状态 $s \in \S$ 且智能体做出决策 $a \in \A$ 并转移到新状态 $s' \in \S$ 的时候环境给予智能体的收益。
- 收益衰减系数 $\gamma$。这里 $\gamma \in [0, 1]$。该参数代表收益随着时间步衰减的系数，具体含义将会在后续定义累计收益的时候展开。

另外需要说明的是，状态 $s \in \mathcal{S}$ 可能并不是完全可以观测的，如果 $s$ 不能完全观测，我们会将 $s$ 中能观测的部分记作 $o$，称为**观测**，即 observation。所有观测构成的集合记作 $\mathcal{O}$。这种部分可以观测的 MDP 称为 **P**artially **O**bservable MDP，缩写 POMDP。

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

对一条轨迹，**累计收益（Utility）**的定义为：

$$
R_t(\tau) := \sum_{k = 0}^{+\infty} \gamma^k r_{t + k + 1}
$$

这里注意到我们累加收益的时候需要不断使用衰减因子进行衰减，这一操作主要是为了拟合现实中距离现在越远的行为对当前决策的影响越小的特征，这一操作同时也保证了收敛性。

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

具体的迭代过程分为两步，分别为**策略评估（Policy Evaluation）**和**策略提升（Policy Improvement）**，这两步会交替进行。具体而言，策略评估是在已知 $\pi$ 的条件下计算其状态价值函数 $V^\pi$。而策略提升为在得知状态价值函数的基础上优化策略 $\pi$。

首先阐述策略评估的过程，这里举出一个迭代计算的方式。

我们记 $V_k$ 是第 $k$ 轮迭代的时候的状态价值函数，那么我们在初始化 $V_0$ 之后不断进行下述迭代直到状态价值函数收敛：

$$
V_{k + 1}(s) \leftarrow \sum_{a \in \A} \pi(a \mid s) \sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma V_k(s')]
$$

收敛后，应当有 $V_{+\infty} = V^\pi$，此时我们就得到了策略 $\pi$ 所对应的价值函数。

策略评估除了使用迭代的方式计算，也可以使用求解线性方程组等方式计算，这些方法我们不具体展开。

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

同样，在已经完全得知环境的条件下，除了策略迭代，我们还可以用值迭代求解最优策略。相较于策略迭代是迭代策略本身，值迭代则是在迭代求解状态价值函数。

考虑最优 Bellman 方程：

$$
{\color{red} V^\star(s)} = \max_{a \in \A} \left[\sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V^\star(s')}]\right]
$$

也就是说我们完全可以直接先随意初始化一个 $V_0 \equiv 0$，然后不断使用上述方程做迭代直到收敛，此时 $V_{+\infty} = V^\star$。迭代的流程描述为：

$$
{\color{red} V_{k + 1}(s)} \leftarrow \max_{a \in \A} \left[\sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V_k(s')}]\right]
$$

重复该迭代直到收敛，即可获得最优状态价值函数。基于最优状态价值函数，可以推知最优策略，从而算法求解完毕。

这里需要说明的一点是，使用值迭代求解的时候，策略是比价值函数先收敛的。这一点是显然的，因为通过价值函数获取对应的策略是贪心的。从而，在价值函数还未实际完全收敛的时候，各个状态的价值大小关系很有可能已经确定且不变化，从而从这一时间点开始，推知的策略都不会发生变化，理论上都是最优策略。

值迭代的时间复杂度为 $O(|\mathcal{S}|^2|\mathcal{A}|)$。

## 有关 PI 和 VI 的一些对比

事实上基于 Bellman 方程而言，VI 方法是最直观也是最容易想到的，因为通过迭代法求解有递归结构的方程确实是一种很常见的方法。然而 VI 的两个特点也是限制其运用的缺陷，即**策略早于价值函数收敛**和**复杂度较高**。

前者事实上是 VI 的一个致命缺陷，因为从根本上而言我们需要获取的是最优策略，具体的价值函数可能并不关注，或者至少不是最主要目标。另外，高复杂度也让 VI 的实际运算难度较高，而这也一定程度上催生了 PI 方法。

VI 在运算过程中有一步涉及到了 $\max_{a \in \A}$，这需要遍历行为空间却没有有效利用遍历的信息（仅取了最大值）。那么一个显然的性能换时间的操作就是我们取一个并非最优但足够优的策略 $\pi: \S \to \A$，直接用 $a := \pi(s)$ 代替掉 $a := \argmax_{a \in \A}$，这样复杂度直接就降低到 $O(|\S|^2)$。

为了保证 $\pi$ 能够足够优，那我们可以两路并进，一路优化 $\pi$，一路更新 $V^\pi$，从而我们就得到了 PI 方法。PI 方法在部分条件下会比 VI 方法收敛快很多，并且我们依然可以证明 PI 理论可以得到最优策略。

## 有关 Bellman 算子的拓展

如果仔细观察 Bellman 方程，其事实上就是一个线性方程：

$$
{\color{red} V^\pi(s)} = \sum_{a \in \A} \pi(a \mid s) \sum_{s' \in \S} \Pe(s' \mid s, a)[r(s, a, s') + \gamma {\color{red} V^\pi(s')}]
$$

首先作一些记号简化：

$$
\begin{aligned}
\P^\pi(s' \mid s) &:= \sum_{a \in \A} \pi(a \mid s)\Pe(s' \mid s, a) \\
r^\pi(s) &:= \sum_{a \in \A} \pi(a \mid s) \sum_{s' \in \S} \Pe(s' \mid s, a)r(s, a, s') \\
\end{aligned}
$$

得到：

$$
{\color{red} V^\pi(s)} = r^\pi(s) + \gamma\sum_{s' \in \S} \P^\pi(s' \mid s){\color{red} V^\pi(s')}
$$

如果记 $\S = \{s_1, s_2, \cdots, s_n\}, n = |\mathcal{S}|$，并定义矩阵：

$$
\begin{aligned}
\b{V}^\pi &:= (v^\pi_i)^T_{n} \in \mathbb{R}^n && v^\pi_i := V^\pi(s_i) \\
\b{R}^\pi &:= (r^\pi_i)^T_{n} \in \mathbb{R}^n && r^\pi_i := r^\pi(s_i) \\
\b{P}^\pi &:= (p^\pi_{ij})_{n \times n} \in \mathbb{R}^{n \times n} && p^\pi_{ij} := \P^\pi(s_j \mid s_i) \\
\end{aligned}
$$

那么 Bellman 方程等价于：

$$
\b{V}^\pi = \b{R}^\pi + \gamma\b{P}^\pi\b{V}^\pi \Rightarrow (I - \gamma\b{P}^\pi)\b{V}^\pi = \b{R}^\pi
$$

这里就可以引入 Bellman 算子：

$$
\mathcal{T}^\pi(\b{x}) := \b{R}^\pi + \gamma\b{P}^\pi\b{x}
$$

如果状态空间足够小，那么在 PI 的策略评估阶段，事实上完全可以通过求解上述线性方程组解的方式得到策略的价值函数。

除了 Bellman 算子，还可以从最优 Bellman 方程引入最优 Bellman 算子。首先依然是定义矩阵：

$$
\begin{aligned}
\b{V}^\star &:= (v^\star_i)^T_{n} \in \mathbb{R}^n && v^\star_i := V^\star(s_i) \\
\b{R}(a) &:= (r_i(a))^T_{n} \in \mathbb{R}^n && r_i(a) := \sum_{s_j \in \S} \Pe(s_j \mid s_i, a)r(s_i, a, s_j) \\
\b{P}(a) &:= (p_{ij}(a))_{n \times n} \in \mathbb{R}^{n \times n} && p_{ij}(a) := \Pe(s_j \mid s_i, a) \\
\end{aligned}
$$

那么最优 Bellman 算子就定义为：

$$
\mathcal{T}^\star(\b{x}) := \max_{a \in \A}(\b{R}(a) + \gamma\b{P}(a)\b{x})
$$

现在我们需要证明一个我们并没有严格说明的引理，即使用迭代法求解价值函数的合法性，无论是 PI 中策略评估还是 VI 求最优价值函数本身。事实上，这两者本质相同，其都在求解 Bellman 算子的不动点，只不过一个是一般 Bellman 算子，一个是最优 Bellman 算子。这里以最优 Bellman 算子为例进行证明。

证明的主要思想就是 Banach 空间不动点定理。

{% note info no-icon %}
**Theorem 2.02 (Banach Fixed-point Theorem)** 给定完备度量空间 $X$ 以及其范数 $d$，假设 $f: X \to X$ 是一个压缩映射，即存在 $\gamma \in [0, 1)$ 令 $\forall x_1, x_2 \in X$ 都有：

$$
d(f(x_1), f(x_2)) \leq \gamma d(x_1, x_2)
$$

那么 $f$ 在 $X$ 上存在唯一不动点 $x^*$。

该不动点满足，任取 $x_0 \in X$，定义序列 $\{x_n\}_{n \geq 0}$，其中 $x_{n + 1} = f(x_n), n \geq 0$，那么该序列必然收敛到 $x^*$。
{% endnote %}

证明是简单的。首先我们任取 $x_0 \in X$，考虑对 $m > n$：

$$
\begin{aligned}
d(f^{(m)}(x_0), f^{(n)}(x_0)) &\leq d(f^{(m)}(x_0), f^{(m - 1)}(x_0)) + d(f^{(m - 1)}(x_0), f^{(m - 2)}(x_0)) + \cdots + d(f^{(n + 1)}(x_0), f^{(n)}(x_0)) \\
&\leq \gamma^{m - 1}d(f(x_0), x_0) + \gamma^{m - 2}d(f(x_0), x_0) + \cdots + \gamma^nd(f(x_0), x_0) \\
&\leq \dfrac{\gamma^n}{1 - \gamma}d(f(x_0), x_0) \\
\end{aligned}
$$

也就是无论给多紧的界，我总能令 $n$ 充分大满足上界约束，从而该序列是 Cauchy 序列，在完备度量空间上其自然是收敛的。

假定这个序列收敛到 $x^*$，我们需要说明 $x^*$ 是 $f$ 的不动点以及其唯一性。其是不动点是容易的，考虑：

$$
0 \leq d(x_{n + 1}, f(x^*)) = d(f(x_n), f(x^*)) \leq \gamma d(x_n, x^*)
$$

由于 $n \to \infty$ 时 $d(x_n, x^*) \to 0$，这说明 $d(x_{n + 1}, f(x^*)) \to 0$，也就说明 $f(x^*)$ 也是序列 $\{x_n\}_{n \geq 0}$ 的极限，据极限的唯一性，不动点得证。

唯一性则据反证。若存在第二个不动点 $f(y) = y$，那么：

$$
d(x^*, y) = d(f(x^*), f(y)) \leq \gamma d(x^*, y) \Rightarrow (1 - \gamma)d(x^*, y) \leq 0
$$

上述约束仅有 $y = x^*$ 时成立，唯一性得证。

---

基于该引理，我们只需要说明最优 Bellman 算子也满足引理所述条件即可。在空间 $\mathcal{S}$ 上取其 $L^{\infty}$ 范数（即各分量最大值）。我们证明最优 Bellman 算子是压缩的：

$$
\begin{aligned}
\|\mathcal{T}^\star(\b{V}_1) - \mathcal{T}^\star(\b{V}_2)\|_\infty &= \left\|\max_{a \in \A}(\b{R}(a) + \gamma\b{P}(a)\b{V}_1) - \max_{a \in \A}(\b{R}(a) + \gamma\b{P}(a)\b{V}_2)\right\|_\infty \\
&\leq \left\|\max_{a \in \A}(\b{R}(a) + \gamma\b{P}(a)\b{V}_1 - \b{R}(a) - \gamma\b{P}(a)\b{V}_2)\right\|_\infty \\
&= \gamma\left\|\max_{a \in \A}(\b{P}(a)(\b{V}_1 - \b{V}_2))\right\|_\infty \\
&\leq \gamma\|\b{V}_1 - \b{V}_2\|_\infty
\end{aligned}
$$

最后一个不等号源于 $\b{P}(a)$ 矩阵中，每一行的元素之和必然为 $1$。如果假设 $\b{V}_1 - \b{V}_2$ 各元素中最大为 $p$（亦其 $L^\infty$ 范数），那么 $\b{P}(a)(\b{V}_1 - \b{V}_2)$ 每一个元素都不会大于 $p$。

从而得证。

事实上我们有下述定理，描述了迭代法求解的正确性。对于任意初始化的 $\b{V}_0$：

$$
\lim_{n \to \infty} \mathcal{T}^{\pi(n)}\b{V}_0 = \b{V}^\pi, \lim_{n \to \infty} \mathcal{T}^{\star(n)}\b{V}_0 = \b{V}^\star
$$