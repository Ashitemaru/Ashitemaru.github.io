---
title: NSDI'25 传输相关领域调研
date: 2025-04-23 16:34:42
category:
    - 【论文笔记】计算机
mathjax: true
---

总之来看看最近的一些 streaming 相关的工作，看看大家都在干什么。

<!-- more -->

# Mowgli - Passively Learned Rate Control for Real-Time Video

## Motivation

场景是在线会议的码率控制。

- 基于规则的算法表现很差（GCC 如图 1，大量反例），所以使用 data-driven
    - 启发式算法的简单结构限制了其能力上限
- 在线会议对卡顿极其敏感，这意味着不能 online 训练（online 训练导致的质量下降如图 2，训练时导致的震荡如图 3），所以要 offline 训练
- 然而 offline 训练很容易遇到线上线下数据分布不一致的问题，模拟器也很难完整复现网络的多变性
    - 线上 finetune 方案也被很多次证明不可行，因为这种数据差异很有可能需要完全重构算法而非简单调整

![](/uploads/survey-nsdi25/1.png)

![](/uploads/survey-nsdi25/2.png)

![](/uploads/survey-nsdi25/3.png)

## Methodology

避免将不成熟的模型部署到线上，所以完全离线，数据全部由 GCC 等算法线上采集回传到服务器。这样做能够收集到大量的与当前环境相关的数据。那么难点就是：

- 日志数据分布单一（一般都是通过单一算法生成）
    - 最终决策会偏离已有日志，没有 ground truth，外推误差会累积
- 日志噪声大，其数据不仅仅和算法有关，和外部环境也有很大关系

这也就是这篇论文想要提出的核心创新点，即**如何稳定地超越朴素的模仿学习获得更优算法**。

> 这就是，异轨蒙特卡洛控制。

针对此的 methodology：

- 保守 reward，线上遇到没有学习到的模式的时候，只有有显著收益才冒险
- 学习数据的分布而非单纯的期望，扩大算法的理解能力

具体设计采用 SAC 算法。为了保证保守 reward，在 critic loss 上增添下述惩罚项：

$$
\alpha\mathbb{E}_{s\sim D}\left[\mathbb{E}_{a\sim\pi} Q(s, a) - \mathbb{E}_{a\sim D} Q(s, a)\right]
$$

实际上就是估计了 $\pi$ 与数据集 $D$ 的距离（称为**决策信心**），越远则 reward 越低。

> 其实相关保守决策 RL 已经有人在做了，比如机器人领域 2024 有 CASOG: Conservative Actor–Critic With SmOoth Gradient for Skill Learning in Robot-Assisted Intervention

所谓的学习分布则是直接将 critic 的输出改成一个分布，loss 函数使用 Huber 函数表示分布之间的距离。

## Evaluation

![](/uploads/survey-nsdi25/4.png)

evaluation 没什么好说的，基本就是 overall 加上特定场景比较，最后补一些消融。

## Comment

核心就是一个保守化的 ABR 训练策略，但是说实话，有一个大坑没填上，那就是用 GCC 的 trace 到底怎么训练出来超越 GCC 这么多的算法的，也就是说，究竟怎么超越模仿学习的。

conservative training 只能解决 data shift 导致的一个子问题，也就是外推误差累积，而且这种解决也不完善。

# Dissecting and Streamlining the Interactive Loop of Mobile Cloud Gaming