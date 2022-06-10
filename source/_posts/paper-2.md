---
title: 《Stick - A Harmonious Fusion of Buffer-based and Learning-based Approach for Adaptive Streaming》论文笔记
date: 2022-06-10 13:28:15
category:
    - 科研杂谈
mathjax: true
---

看起来是一个有关结合两种已有方法的一种创想。

虽然可能暂且对这个领域了解不是特别深入，总之尝试着去阅读了解一下。

<!-- more -->

# Introduction

首先考虑到现实应用背景，视频流传输已经占据了大部分的网络传输带宽。但视频传输的问题在于视频内容的多样性和可能的网络波动。一种可能的解决方案是使用 Adaptive bitrate (ABR) 算法，这些算法已经被证实可以提供高 QoE 的传输服务。

传统的 ABR 算法往往是 buffer-based 的（例如 BBA 和 BOLA），这些算法通过固定的 buffer 和参数来选定下一个视频块的码率，这可能难以在波动网络环境下保证 QoE。

另一方面，基于深度强化学习（DRL）的 ABR 算法 Pensieve 却给出了 state-of-the-art 的表现。在实践上，这类算法也常常部署在服务器侧以避免客户端的高计算开销。但是考虑到 ABR 算法往往需要部署在前端以避免连接后端的网络延迟，这使得这类算法理论可用但是并不实际。

那么我们可以将这两种方法结合起来，使用 DRL 来强化 buffer-based 的方法，同时使用固定的缓冲区限制来限制计算开销。从而该论文提出了 Stick 框架，该框架将 QoE 参数、网络状态、视频特征作为输入参数，而缓冲区限制（连续的标量）作为其输出。

考虑到连续的动作空间，我们计划使用 Deep deterministic policy gradient (DDPG) 来从头开始训练模型。此外，Stick 在无假设条件下通过和 ABR 环境交互来学习策略。

为了保证计算效率，我们进一步提出了 Trigger。Trigger 会观测当前和先前的缓冲区限制，只在必要的时候激活 Stick 来更新缓冲区限制。Trigger 使用 imitation learing 方法来学习专家行为。同时，为了克服数据的无偏分布【Question #1】，我们使用了 prioritized experience replay 来存储专家策略。同时，该论文还提出了一种可以让其他 ABR 算法框架接入 Trigger 的方法。

这篇论文的重点在于：

- 首次提出结合 buffer-based 方法和 learning-based 方法
- 通过 Stick，可以使用更小的模型实现比已有方法更好的效果
- 通过 Trigger，证明轻量级模型可以有效降低计算开销

# Buffer-based & learning-based ABR algorithm

BBA 只根据当前缓冲区限制来选定下一个视频块的码率。具体而言，其将对吞吐量的估计固定为启动态额吞吐量以克服吞吐量的波动，之后在稳定态时，使用缓冲区限制 $\{B_{\rm min}, B_{\rm max}\}$ 来获取块映射，块映射的作用是将缓冲区占用情况映射为码率。

而这个映射也是简单的分块线性映射，缓冲区占用低于 $B_{\rm min}$ 时下一个视频块码率为低码率 $R_{\rm min}$，缓冲区占用高于 $B_{\rm max}$ 时下一个视频块码率为高码率 $R_{\rm max}$，位于这两者之间的时候使用线性函数决定。

这个方法大部分情况下简单且有效，但是也在许多条件下效果不理想。原因是 BBA 使用的固定参数应当根据应用环境做出相应的调整。

而 learning-based ABR，例如 Pensieve 则给出了较好的理论结果，但是该方案并不容易应用到现实中。此外，Pensieve 也在部分应用环境下效果不理想。

# Key ideas

总结起来是这样的几点：

- 使用 DRL 的方法调整 ABR 的参数，避免使用查表或者预训练模型造成的过大存储和计算成本
- 模型的状态空间需要考虑全面，不仅包括当前缓冲区限制，还要包括网络状况和视频特征
    - 此外，实验发现 Stick 可以仅使用一个参数控制缓冲区，而非两个参数
- QoE 要求会被作为 goal 输入模型，该参数会随机初始化，这会让模型意识到 QoE 参数和 feedback reward 之间的关系
- 使用轻量级的 Trigger 来决定是否需要通过 Stick 更新缓冲区限制，以降低计算成本

# System overview

整体的框架可以表现为：

![](/uploads/paper-2/1.png)

## Stick mechanism

Stick 的结构图可以表示为：

![](/uploads/paper-2/2.png)

### Design

首先叙述状态空间的构造：

- $T$ 为由客户端测量的前 $k$ 个序列的吞吐量
- $d$ 为前 $k$ 个序列的下载时间
- $q$ 为上一个视频块选定的码率
- $r$ 为视频剩余播放时间
- $b$ 为当前缓冲区占有情况
- $S$ 为表示下一个视频块大小的向量
- $g$ 为 QoE goal 参数，由 $\mu, \tau$ 构成

动作空间则是一个连续标量 $B \in [0, {\rm buffer}_{\rm max}]$，用于控制缓冲区限制。

奖励函数则根据不同的 $g$，使用经典的线性 QoE 指标来构造。

### Training methodology & Implementation

【这一部分在阅读完 DPG 相关论文之后再具体展开】

## Trigger

Trigger 的一个核心思想在于，如果 Stick 在连续两个视频块上给出相同的结果，那么其第二次计算是无用的。而实验证明，无用操作可能占比达到 30%，所以我们的目标就是用 Trigger 去判别哪些操作是无用的。

现在考量 Trigger 的状态空间。根据 Related work，网络吞吐量是我们需要考虑的重要参数。但是我们也发现了，先前的缓冲区占用情况也是一个重要特征，其可以揭示网络环境状态和视频块大小之间的隐藏关系。

所以我们最终令 Trigger 的输入是先前的网络吞吐量、缓冲区大小和现在的缓冲区限制。而输出是一个二维向量，表示 Stick 保持或者改变当前缓冲区限制的概率。

---

使用 imitation learning 来训练 Trigger，这种方法能够根据 expert demonstration 给出预期的结果。训练的算法可以表示为：

![](/uploads/paper-2/3.png)

---

另外，我们需要关注训练数据集分布的问题。论文使用的数据集中有 83% 的操作是无用操作，这种分布不均匀的数据集和理想的 50%-50% 数据集差距过大，可能导致特征训练不均衡。所以这里使用了 Prioritized experience replay 方法来重新平衡数据集。

关于这个方法的介绍可以参考 [这篇文章](https://paperswithcode.com/method/prioritized-experience-replay)。

之后讨论如何将 Trigger 应用到其他的 ABR 算法上。
