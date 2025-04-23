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

> 其实相关保守决策 RL 已经有人在做了，基本来源于 2020 提出的 conservative Q-learning。相关应用也不少，比如机器人领域 2024 有 CASOG: Conservative Actor–Critic With SmOoth Gradient for Skill Learning in Robot-Assisted Intervention

所谓的学习分布则是直接将 critic 的输出改成一个分布，loss 函数使用 Huber 函数表示分布之间的距离。

## Evaluation

![](/uploads/survey-nsdi25/4.png)

evaluation 没什么好说的，基本就是 overall 加上特定场景比较，最后补一些消融。

## Comment

核心就是一个保守化的 ABR 训练策略，但是说实话，有一个大坑没填上，那就是用 GCC 的 trace 到底怎么训练出来超越 GCC 这么多的算法的，也就是说，究竟怎么超越模仿学习的。

conservative training 只能解决 data shift 导致的一个子问题，也就是外推误差累积，而且这种解决也不完善。

# Dissecting and Streamlining the Interactive Loop of Mobile Cloud Gaming

主旨是优化安卓平台下的云游戏延迟，他们认为最主要的因素是 5 次串行执行的垂直同步事件。

![](/uploads/survey-nsdi25/5.png)

## Measurement

测量的 setup 是使用高速摄像机拍摄，计算触控笔按下和按钮高亮之间的延迟，测量考虑了网络高峰平峰时段以及网络种类等信息。

![](/uploads/survey-nsdi25/6.png)

结论是：

- 移动端云游戏延迟显著高于 PC 端云游戏，这是因为移动端云游戏可能需要 OS 虚拟化等等
- 网络延迟并非是延迟的最主要组成部分
- 交互延迟与网络高峰平峰没有太大关系，显示出大部分系统的弹性

更具体的测量表明，安卓云游戏延迟中最主要的部分是占比 36% 的安卓内核垂直同步，而这个垂直同步是默认开启的。同时，由于垂直同步，小幅度的抖动可能导致帧播放错过 deadline，制造高达 13 毫秒的延迟，从而放大抖动的影响。

接下来是安卓云游戏的垂直同步拆解：

- VSync1：游戏进程等待固定帧率的时钟（无法撤销或者绕过，该同步用于恒定帧率）
- VSync2：SurfaceFlinger 等待前序处理以合成画面
- VSync3：HWcomposer 等待显示器刷新完毕
- VSync4：客户端与服务端同步
- VSync5：客户端 SurfaceFlinger 送显

解决的方式则是剪切和协调。例如第 2 和第 3 次垂直同步可以通过工程的方式绕过，第 4 次可以用算法协调，其余两次则基本无法绕过。这也分别带来了来自工程和算法上的两方面挑战。

## Design

工程上绕过两次垂直同步的思路是，考虑到图层合成仅仅是增添了 UI 边框等东西，则可以通过拦截渲染的方式获取原始帧，然后手动合成贴图。这里细节较多，不展开。

协调则主要通过预测的方式完成。使用回归树，利用下述特征对下述更细致的拆解延迟做预测：

- 历史延迟数据
- 编码帧大小
- 丢包率与带宽估计

采样间隔为 1 帧，利用历史 4 秒信息预测未来 1 秒。

![](/uploads/survey-nsdi25/7.png)

在得到未来一段时间的预测之后，做下述数学建模，目的是获得令延迟最小的 $o$：

![](/uploads/survey-nsdi25/8.png)

## Evaluation

![](/uploads/survey-nsdi25/9.png)

基本就是说明了该方法能非常有效地降低延迟。

## Comment

由于这篇和 START 第二篇工作内容太相近了所以其实可以点评的还不少。

整体的 motivation 比较相似，解决方案上的话工程上绕过 VSync 的方式不谈，算法上其实有个比较大的问题就是回归树预测肯定是不准确的，但他有个比较 trick 的地方就是，如果假定你给的网络带宽估计是准确的，那其实预测一个网络延迟是很简单的，而渲染、编解码延迟相对而言比较稳定（相对于网络）。然而问题就在于，网络带宽很难预测准，这说白了有点像用前 10 天的股价预测第 11 天的股价。

预测窗口是 5 秒也比较值得推敲，云游戏下 5 秒是能发生很多事情的。不过 5 秒的好处就是计算最优延迟绰绰有余，这里可以回避掉优化算法时间开销的问题。

整体而言事实上是个偏工程的工作，但是毕竟还是和第二篇工作内容太近了。

# Mutant - Learning Congestion Control from Existing Protocols via Online Reinforcement Learning

和 Bridging the Gap between QoE and QoS in Congestion Control: A Large-scale Mobile Web Service Perspective 有点像，都是如何充分利用已有的 CC 算法。

## Motivation

核心的 motivation 来源于不存在一个 CC 算法能应对所有网络情况。例如，Cubic 在稳定环境下表现良好，但难以适应带宽波动，BBR2 虽擅长处理突变带宽，却在低容量网络中表现欠佳。

![](/uploads/survey-nsdi25/10.png)

另外，为了适应不同的网络情况，各种 data-driven CC 也应运而生。然而这些 CC 一般限于下述两点：

- 仅依赖于历史拥塞窗口情况学习，泛化性差
- 泛化性好的方案一般都有相当长的 offline 训练流程

从而一种可行的解决方案则是，我们不主动推导拥塞窗口，而是选择合适的已有 CC 来应对当前情况。

## Design

Mutant 通过 Top-k 的方式选择初始协议，并在初始协议的基础上通过 online RL 来优化 CC 性能。

协议接口通过统一函数指针封装，首先明确协议切换时需要做的事情：

- 冻结上一个协议的所有状态变量（支持恢复）
- 切换协议时保持拥塞窗口不变，保证 CC 连续性

online RL 选择了 contextual MBA（在给定上下文状态空间编码的条件下的多臂老虎机），策略则是线性上置信界（Linear UCB）。

状态则是从内核探测，一共有 55 个字段，时间窗口分为长中短三个阶段，对应 10/200/1000 次采样，最后通过一个编码器降维压缩到 16 维。

reward 定义为：

$$
R_t = \dfrac{({\rm thr}_t - \zeta l_t)^\kappa}{d_t}
$$

分子上是发送速率和丢包率，分母是延迟。

---

在训练的时候，不断使用 MPTS 算法选出最有可能竞争的 $k$ 个 CC 算法组成算法池，在当前阶段则不断使用 contextual MBA 算法选择实际执行的算法。该阶段结束后，更新各个算法的 reward。

## Evaluation

![](/uploads/survey-nsdi25/11.png)

## Comment

该方法尝试去利用了当前已有的 CC 算法，尽力做到物尽其用。

本篇与之前 Zili Meng 的那篇 CC selector 比较，一个核心的竞争力在于本篇的 selector 是可以线上学习的，是可以根据不同的网络情况生成不同的 Top-k 然后物尽其用的。而这种线上学习也就意味着难以用神经网络构建 selector，所以本文打开了这个 selector 的黑盒，采用了更类似于启发式的算法（不可否认还是使用了 embedding 之类的策略）。

# Checkpoint

大体而言今年 NSDI 的 streaming 部分这三篇挺有代表性的：

- 新问题（云游戏中的垂直同步）
- 新方法应用到老问题，难点在讲好故事，为什么新方法可行
- 老方法聚合（包括 Zili Meng 的 CC selector）

云游戏总之还算是一种前沿，还是能提出一些视频相关的新问题（但是能不能解决不清楚，目前显得有点要么太容易解决，要么无解）。码率控制、CC 之类的相对传统的方向，总之有一种需要拼凑才能做出东西的感觉。

是否可以从一个相对系统的角度去思考问题，而非优化单一的算法（CC、ABR），从流媒体系统架构的角度去做（垂直同步那一篇）？

> 完全不知道自己在说什么。