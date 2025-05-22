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

# Holmes - Localizing Irregularities in LLM Training with Mega-scale GPU Clusters

LLM 的训练需要极其大量的硬件成本，所以需要尽可能提高训练过程对硬件的利用率。一般大家都认为训练的 failure 会导致巨大的硬件资源浪费，本文发现另外一个导致资源浪费的原因，即 irregularity，指的是明显耗时过长的 iteration。

irregularity 不会中断训练，所以难以定位，但是依然会导致资源浪费。irregularity 的特征是会传染，因为其不会导致中断，所以其他 GPU 会被动等待导致传染。这使得 irregularity 甚至比 failure 更严重。

challenge 可以归纳为：

- irregularity 不会导致中断，所以难以定位
- 另一方面，定位还必须要精准，因为错误定位的恢复比 irregularity 本身的损失还要高
- 而 irregularity 的传染性让定位更加困难

算子分为计算算子和通信算子，顾名思义计算算子只要单 GPU 执行即可，通信算子则需要多 GPU 之间同步。通信算子进一步分为集群通信算子（涉及大范围的 GPU）和点对点通信算子。

## Measurement

定义 $\delta$-irregularity ($\delta > 1$，一般设定为 $1.1$) 表示出现了某一个没有中断训练，但是耗时是指定窗口内平均时长的 $\delta$ 倍的 iteration。

![](/uploads/survey-nsdi25/12.png)

上述可见：

- irregularity 出现的机率比 failure 高出若干数量级
- irregularity 出现的机率随着 GPU 数量增多而增大

![](/uploads/survey-nsdi25/13.png)

上述可见：

- irregularity 导致的时间浪费与 failure 接近
- irregularity 所需要的诊断分析实践也高于 failure
    - 这是由于 failure 处理已经相当标准化了

总之就是验证了 irregularity 的特质，也说明了全文的 motivation。

## Design

irregularity 分为三类，即 abnormal spikes、performance fluctuation、persistent degradation。

abnormal spikes 指的是某些 iteration 出现了尖峰长耗时，这种 irregularity 有较为严重的影响。经过训练归因，本文确认了这类问题主要的两个原因是 GPU 运算卡顿和网络卡顿是主要原因。

![](/uploads/survey-nsdi25/14.png)

performance fluctuation 指的是周期性的波动，虽然它们也呈现出尖峰长耗时，但是相对于 abnormal spikes 温和，并且呈现一定规律，然而正是由于规律性，performance fluctuation 的影响比 abnormal spikes 大。经过训练归因，本文确认了主要原因是网络相关的显式拥塞通知（ECN）和优先流量控制帧（PFC）。

![](/uploads/survey-nsdi25/15.png)

persistent degradation 指的是长期视角下的训练效率下降，但这类问题归因简单，基本都是 GPU 过热或者 CPU 异常高占用率。

![](/uploads/survey-nsdi25/16.png)

最终设计如下：

![](/uploads/survey-nsdi25/17.png)

工作流为：

- iteration monitor 实时追踪 GPU 日志
- 检测到异常的时候，operator detector 检查具体是什么算子导致了问题
- root localizer 通过算子图确认需要检查的 GPU
- device analytics 计算 Top-k 故障 GPU，提交给值班检修

### Iteration monitor & operator detector

首先，必然不可能什么东西都打日志，考虑到通信算子的状况既可以监测网络链路又可以一定程度代表其上游的计算算子，所以可以只打印通信算子的情况.

![](/uploads/survey-nsdi25/18.png)

在检测到异常的迭代时长的时候，需要去溯源日志去看哪个算子出问题了。这里为了管理算子的 performance，本文使用了随机森林，算子日志的每条记录都是随机森林的样本。同时，引入迭代时间、算子运行时间窗口均值等特征增强。

简而言之就是对每一个算子建立一个决策树确定其是否是异常算子。但是由于 persistent degradation 存在，所以算子的特征分布必然会 shift，所以需要设计相关的补充措施。

本文的决策树时，每个节点的所采用的特征都是明确的，所以当某一个统计量出现 shift 的时候，只需要直接调整相应节点上的阈值：

![](/uploads/survey-nsdi25/19.png)

### Root localizer

为了具体定位是哪一个 GPU 出了问题导致某个算子异常值（注意 irregularity 是有传播性的），可以建构一个图来表示 GPU 之间的依赖关系，这就是所谓的 COG。

定义 COG $G = (O, D, E)$，其中 $o \in O$ 是通信算子，$d\in D$ 是设备，如果 $d$ 需要执行 $o$，则连边 $(o, d)\in E$。

为了减缩图的复杂度，可以对 COG 分组。对某一个算子 $o$，列出所以与其连边的 $d$，这些 $d$ 构成一个 cluster $c\in C$，我们用 $C$ 代替 $D$，这样我们可以注意到最终的 COG 中每一个算子 $o\in O$ 只会有一条边，存储图的内存就可以节省。同时 $C$ 的 size 一般也是可控的。

---

如果现在异常的是集合通信算子 $o$，检测其所属 cluster $c$，对 $c$ 其余的所有算子作分析。

- 若这些算子全都异常，则说明 $o$ 本身就是问题，终止分析
- 若存在正常算子 $n$，则说明其他算子是因为等待 $n$ 而异常，此时回溯 $n$ 的前序
    - 如果没有异常算子，则说明 $n$ 运算超时
    - 如果有异常算子，重复上述过程

![](/uploads/survey-nsdi25/20.png)

如果现在异常的是点对点通信算子 $p$，检测其配对的算子。

- 若均异常，则说明通信存在问题
- 若对方正常，则回溯对方（同上）

### Device analytics

简单来说就是现在查出来根源了，怎么分锅。首先计算这个 irregularity 和上次 iteration 超时之间的 Pearson 关系系数作为惩罚项，然后如果定位为计算超时则直接惩罚到 GPU 上，如果是网络超时则惩罚到相关 GPU 和这些 GPU 之间的交换机等设备上。

## Evaluation

各组件的性能以及端到端与 baseline 的对比：

![](/uploads/survey-nsdi25/21.png)

时间开销：

![](/uploads/survey-nsdi25/22.png)

主要优势在于这个方法不需要检测所有 GPU。

> 但说实话你构建 COG 的时候，简单加一个分组是不是也可以避免 all fetch？这个对比是不是有点不公平？

## Conclusion

很像工程技术报告的文章，写得很简单直白但是做了一件挖得很深很全面的事情。

# MTP - Transport for In-Network Computing

是一个协议相关的工作，设计了一个原生支持网内计算的协议。网内计算就是将应用层的计算任务下沉到网络设备上进行的方案。

网内计算协议必须满足下述约束：

- 在应用层卸载的计算任务可能对应用消息进行修改、拦截、重排序和任意延迟的情况下，仍能保证可靠性拥塞控制等基本功能
- 不额外增加 INC 硬件的传输相关资源需求（如大量缓冲或状态维护）

现在常用的协议是 TCP 变种或者 RDMA，而这些协议多少都会违反上述约束。

## Motivation

INC 允许在网卡或者交换机上做键值缓存、机器学习内网聚合等应用层任务，但这些操作可能和下层传输协议有所冲突。

首先，卸载单元（指网卡、交换机等）可能需要修改 payload，例如注入 cookies 或者梯度聚合后替换计算结果，这会影响消息长度与分包数量。

之后，卸载单元可能会拦截消息并丢弃，例如部分负载均衡任务会丢弃不符合要求的请求。

然后，卸载单元可能会重排序信息。

最后，卸载后的延迟可能难以预测，例如 FPGA 的入侵检测的延迟长尾现象很严重，部分聚合需要等待所有节点导致实际耗时无法预测。

现今的协议主要有三个，TCP 变种（例如 QUIC）、RDMA、Homa。

- TCP、RDMA 完全无法处理消息修改、拦截、重排序，因为其传输有效性完全依赖连续的字节空间与序号，修改和拦截会导致收发两端不一致，发送端无法确认是否需要重传。重排序则会被理解为丢包导致重传和拥塞窗口缩小
- Homa 协议（不太懂）无法处理消息修改（依赖字节范围判定），兼容拦截和重排序（采用消息级独立传输），随机延迟会导致其拥塞控制失败

另外一方面，由于协议不够理想，INC 任务往往也会加入约束来适配协议。

- 卸载节点同时运行收发两端的协议栈，这会导致大量的资源浪费，部分交换机也不支持
- 约束 RPC 长度、约束修改不能改变消息长度、选择性支持（例如不支持拦截消息）等

因此，本文的设计目标为：

- 完整支持消息修改、拦截、重排序
- 在各种延迟的情况下都能保证控制有效
- 不依赖于特定的硬件设计

## Design

### Pathlet

消息传输过程中，可能会经过多个执行不同计算的卸载单元，这些卸载单元称为一个 pathlet。pathlet 中的各个节点可以共用地址也可以不共用而分布式。

![](/uploads/survey-nsdi25/23.png)

在这个基础上，我们需要实现匹配当前任务 pathlet 的发现和路由。另外，本文优先实现全缓冲模式（节点缓存全部信息后处理），这是因为当今大部分上层应用和下层硬件都支持单包/巨型帧。

### Components

两大核心组件。

- 面向消息的可靠性协议
    - 原生支持消息修改、拦截和重排序
    - 采用基于消息的重传超时（RTO）机制：接收端在收到完整消息后发送端到端（E2E）ACK 以取消定时器，否则触发整条消息重传
- 多 pathlet 拥塞控制框架
    -  pathlet 级早期拥塞反馈：pathlet 可将其拥塞状态及链路拥塞信号（如 ECN）直接反馈给发送端
    - 消息级 pathlet 选择：当存在多个同类型 pathlet 时，每条消息可动态选择不同实例以规避拥塞点

优势在于无需 pathlet 维护额外传输状态。

### Workflow

![](/uploads/survey-nsdi25/24.png)

- 连接建立
    - 采用类 TCP 三次握手
    - 发送方通过服务发现协议获取 pathlet 信息，拥塞控制模块缓存可用 pathlet 
- 消息发送
    - 应用提交消息描述符（含消息体元数据）
    - MTP 栈分配递增的消息编号，拥塞控制选择 pathlet 地址并写入描述符
    - 消息体按 MSS（本例为 4096 字节）分片，每片携带唯一分段号
    - 封装 pathlet 地址和分段数据后发送
- pathlet 处理
    - pathlet A 收到分段后立即生成拥塞反馈包（含自身及链路拥塞信号）
    - 执行消息修改（本例使消息增大 8 字节，导致分段数变化）
- 消息接收
    - 接收方根据首个到达分段创建消息描述符，用位图记录已收分段
    - 当所有分段到达后发送 E2E ACK
    - 接收方被动
        - 仅对完整消息发送ACK
        - 不检查消息编号连续性或分段顺序
        - 支持因细粒度负载均衡导致的乱序到达

设计特性：

- 不保证消息顺序交付：避免队头阻塞（HOL blocking）影响时延敏感应用
- pathlet 行为约束：每条输入消息最多产生一条输出消息、拦截消息需模拟接收端发送 E2E ACK、新增负载必须通过消息修改实现
- 扩展性：多播 pathlet 支持留作未来工作