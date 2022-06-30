---
title: 《Soft Rasterizer - A Differentiable Renderer for Image-based 3D Reasoning》论文笔记
date: 2022-01-18 11:12:41
mathjax: true
category:
    - 【论文笔记】计算机
---

进可微渲染项目之后要求阅读的第一篇论文，就当作一种入门论文吧。

<!-- more -->

## 工作概述

主要思想还是想把逆向渲染管线（通过二维图像推理三维模型的参数）和神经网络结合，但是最大的问题是管线中的光栅化是离散化操作，可能导致不可微。一些其他的工作在尝试**近似估算**梯度，但是这个工作提出了一种真正可微的框架，能做到：

- 直接用可微函数渲染染色的网格
- 能从轮廓、阴影等各种二维图像信息有效反向传递为网格顶点参数

这个工作的重点是把渲染过程的部分看作**聚合函数（Aggregation function）**，该函数联系了三维模型的网格三角形的概率分布以及其渲染出来的像素。

{% note info no-icon %}
看起来标题中的 Soft 的含义就是使用概率分布的方式来把离散的光栅化变成可微的，最初始的光栅化一般会涉及到离散采样（即确定每一个离散的像素是否要显示网格上的某一个三角形），这也是不可微性的来源。
{% endnote %}

本论文主要讲述了如何通过 Soft rasterization 框架实现可微的光栅化以保证渲染管线的可微性，基于此，该渲染框架即可方便地接入神经网络以将二维图像信息反向传播到三维模型。同时，本论文也会指出近期的近似估算梯度的方式（使用 hand-crafted 的函数近似表示梯度）并不具有泛用性，且可能会降低三维模型信息更新的效率。

### SoftRas 与传统光栅化

SoftRas 在将模型投影到平面上的时候，会将模型转换为投影平面上的概率分布。而传统光栅化则是转化为具体某个像素是否染色。

另外，似乎 SoftRas 对遮挡的处理也和传统渲染管线的 z-buffer 不一样。传统光栅化会使用 onehot 的方式处理遮挡（应该指的是处理为完全遮挡），而 SoftRas 也会基于概率分布做出不同处理。

## 相关工作

### 可微渲染

主要关注如何描述二维图像变化和三维模型操作的关系，主要解决如何从二维图像重建三维场景。考虑给定的二维图片 $A$，首先粗略初始化一个三维场景 $B'$，然后通过渲染管线渲染得到二维图片 $B$。计算 $A$ 和 $B$ 的 loss，将其梯度反向传播给三维场景 $B'$ 的参数，更新之。这里反向传播就是可微渲染最重要的步骤，即如何计算梯度，因为三维场景参数更新量取决于 loss 对参数的偏导数。

先前提到，这里获取梯度有困难。有些工作使用估计梯度的方式进行，有些工作可以精确计算梯度但是不具有泛用性。

### 基于图像的三维推理

依然考虑从二维图像重建三维场景。传统方法往往要求获得从多个角度观察得到的二维图像，而由于数据集的扩大，基于学习的重建方法可以使用一张或少量二维图像来重建场景。部分工作会将重建三维场景拆分为首先重建 2.5 维的表示（深度表等）。

另外，姿态估计也是三位推理的一个重要的子任务。通过深度学习的方法，预测刚体姿态是较为简单的，而预测人体这类非刚体的姿态则较为困难。

## SoftRas 基本原理

大约是这样的一个流程：

![](/uploads/paper-1/1.png)

这里外部因素包含相机参数 $\boldsymbol{P}$ 和环境光照 $\boldsymbol{L}$，内部因素包括网格信息 $\boldsymbol{M}$ 以及各个顶点的信息（如颜色、材质）$\boldsymbol{A}$。

这里第一步要做的是获取三个中间信息，即网格法向量 $\boldsymbol{N}$、视图空间（投影后的空间）各点坐标 $\boldsymbol{U}$ 以及 View dependent depths $\boldsymbol{Z}$。这三个量只需要使用相机参数和网格信息即可得到。

之后可以使用例如 Phong 模型等着色模型计算出颜色 $\boldsymbol{C}$。

进行到这一步，上述这些操作都是可微的。但是如果按照传统的渲染管线，下一步应该分别确定每一个网格三角形与每一个像素的对应（即光栅化），之后还要用 z-buffer 处理遮挡，这两步都因为离散采样从而是不可微的。

SoftRas 软化光栅化和 z-buffer 的方式是引入 probability maps $\{\mathcal{D}_j\}$ 和聚合函数 $\mathcal{A}(\cdot)$ 分别代替光栅化和 z-buffer。这里 $\mathcal{D}_j$ 表示的是每一个像素在网格三角形 $f_j$ 内部的概率在平面上的分布。而聚合函数 $\mathcal{A}(\cdot)$ 联系起了 probability maps 以及深度信息，作为整个后续渲染流程的代表。

### probability maps 计算

考虑网格三角形在屏幕上的投影 $f_j$ 和屏幕上的像素 $p_i$，记在屏幕上 $p_i$ 到 $f_j$ 的边最近距离为 $d(i, j)$。显然，这个距离越小的像素点，出现在内部的概率越大。由于其可以反映三角形对屏幕的影响，并且其可微，所以可以用此作为 probability maps 的代表。随后通过添加控制常数、符号标记并归一化到 $(0, 1)$ 内之后，我们得到了 probability maps 的公式：

$$
\mathcal{D}_{i, j} = \mathrm{sigmoid}\left(\delta_{i, j}\cdot\frac{d^2(i, j)}{\sigma}\right)
$$

这里 $\sigma$ 是一个正常数，一般默认为 $1\times 10^{-4}$。而 $\delta_{i, j}$ 标记了 $p_i$ 是否在 $f_j$ 之中。若在，则 $\delta_{i, j} = 1$，否则 $\delta_{i, j} = -1$。

下图展示了 $d(i, j)$ 的定义以及 $\sigma$ 常数对概率分布的影响：

![](/uploads/paper-1/2.png)

显然看出 $\sigma$ 越大，概率分布就越模糊。

另外一个显然的点是 $\sigma \to 0$ 的时候概率分布中三角形外的点概率密度为 $0$，而三角形内的点为 $1$。此时 SoftRas 退化为一般的光栅化。

### 聚合函数

我们考虑最后一个阶段，这里我们需要根据深度信息、 probability maps 以及颜色信息计算出最后的渲染结果。

最后渲染结果中，像素 $p_i$ 的渲染结果用聚合函数表示，定义为：

$$
I_i = \mathcal{A}_S(\{C_j\}) := \sum_j w_{i, j}C_{i, j} + w_{i, b}C_b
$$

这里渲染结果定义为一系列颜色信息的加权求和，颜色构成的集合为 $\{C_j\}$，符号 $C_{i, j}$ 表示三角形 $f_j$ 在像素 $p_i$ 处的颜色。而 $C_b$ 表示背景颜色。

权重 $w_{i, j}$ 至少满足归一化条件：

$$
w_{i, b} + \sum_j w_{i, j} = 1
$$

而权重的具体值取决于先前得到的 probability maps 以及深度信息。我们将三角形 $f_j$ 中投影到 $p_i$ 像素的点的逆深度记为 $z_{i, j}$。概率分布沿用先前的符号。定义：

$$
w_{i, j} := \frac{\mathcal{D}_{i, j}\exp(z_{i, j} / \gamma)}{\sum_k \mathcal{D}_{i, k}\exp(z_{i, k} / \gamma) + \exp(\varepsilon / \gamma)}
$$

依据归一化条件就有：

$$
w_{i, b} := \frac{\exp(\varepsilon / \gamma)}{\sum_k \mathcal{D}_{i, k}\exp(z_{i, k} / \gamma) + \exp(\varepsilon / \gamma)}
$$

{% note info no-icon %}
可以看出权重的计算公式其实是类似 Softmax 函数：

$$
{\rm softmax}(z_1, z_2, \cdots, z_n)_j = \frac{e^{z_j}}{\sum_i e^{z_i}}
$$
{% endnote %}

这里可以看出 $\varepsilon$ 是一个小正常数，其作用是将背景颜色纳入计算。$\gamma$ 是一个调节清晰度的正常数，与计算 probability maps 的 $\sigma$ 类似，默认值一般是 $1 \times 10^{-4}$。

可以看出，具有更大概率密度的，且离投影平面更近的（即具有更大逆深度的）三角形会获得更大的权重，这符合直觉。

---

现在考虑退化。针对像素 $p_i$，假设 $z_{i, 1} < z_{i, 2} < \cdots < z_{i, N}$，考虑 $\gamma\to 0$。首先考察 $j = N$：

$$
\begin{aligned}
w_{i, N} &= \lim_{\gamma\to 0}\frac{\mathcal{D}_{i, N}\exp(z_{i, N} / \gamma)}{\sum_k \mathcal{D}_{i, k}\exp(z_{i, k} / \gamma) + \exp(\varepsilon / \gamma)} \\
&= \lim_{\gamma\to 0}\frac{\mathcal{D}_{i, N}}{\sum_k \mathcal{D}_{i, k}\exp((z_{i, k} - z_{i, N}) / \gamma) + \exp((\varepsilon - z_{i, N}) / \gamma)} \\
&= \lim_{\gamma\to 0}\frac{\mathcal{D}_{i, N}}{\sum_{k = 1}^{N - 1} \mathcal{D}_{i, k}\exp((z_{i, k} - z_{i, N}) / \gamma) + \mathcal{D}_{i, N} + \exp((\varepsilon - z_{i, N}) / \gamma)} \\
&= 1
\end{aligned}
$$

最后一步注意到 $z_{i, N}$ 大于任何 $z_{i, k}(k < N)$，故有这样的结果。而对于 $j < N$：

$$
\begin{aligned}
w_{i, j} &= \lim_{\gamma\to 0}\frac{\mathcal{D}_{i, j}\exp(z_{i, j} / \gamma)}{\sum_k \mathcal{D}_{i, k}\exp(z_{i, k} / \gamma) + \exp(\varepsilon / \gamma)} \\
&= \lim_{\gamma\to 0}\frac{\mathcal{D}_{i, N}}{\sum_k \mathcal{D}_{i, k}\exp((z_{i, k} - z_{i, j}) / \gamma) + \exp((\varepsilon - z_{i, j}) / \gamma)} \\
&= \lim_{\gamma\to 0}\frac{\mathcal{D}_{i, N}}{\left(\sum_{k = 1}^{j - 1} + \sum_{k = j + 1}^{N}\right) \mathcal{D}_{i, k}\exp((z_{i, k} - z_{i, N}) / \gamma) + \mathcal{D}_{i, N} + \exp((\varepsilon - z_{i, N}) / \gamma)} \\
&= 0
\end{aligned}
$$

这里要注意到分母上 $\sum_{k = j + 1}^{N}$ 的部分最终会趋向于正无穷，整体极限为 $0$。最后根据归一化条件也有 $w_{i, b} = 0$。

结合这样的推理，可以看出：

$$
I_i = \mathcal{A}_S(\{C_j\}) = C_{i, N}
$$

也就是说最后渲染的结果只考虑了最近的网格三角形，这也就是 z-buffer 方法。

---

似乎还有一个和轮廓相关的聚合函数：

$$
I_{i, S} = \mathcal{A}_O(\{\mathcal{D}_j\}) := 1 - \prod_j (1 - \mathcal{D}_{i, j})
$$

这里没有出现颜色项，这是因为轮廓的颜色一般设定为常数。这里聚合函数形式上实际上就是计算出了像素 $p_i$ 被至少一个三角形覆盖的概率。

## 实验内容

### Single-View Mesh Reconstruction

使用这样的结构：

![](/uploads/paper-1/3.png)

这里固定了渲染管线中的外部变量，即固定了相机位置和环境光照。每一个输入图片都会进入 color generator 和 shape generator 生成三维模型参数 $\boldsymbol{C}$ 和 $\boldsymbol{M}$。随后后续接入 SoftRas，渲染得到带颜色的图像 $I_C$ 和轮廓 $I_S$，最后和原先图像比较计算 loss。

最后定义的 loss 分为三部分。在叙述定义之前，我们将原始图像的颜色和轮廓定义为 $\hat I_C$ 以及 $\hat I_S$：

- color loss。定义为颜色之差的 1- 范数：

$$
\mathcal{L}_C := \|I_C - \hat I_C\|_1
$$

- silhouette loss。定义为，这里使用 $\otimes$ 表示逐元素求积，$\oplus$ 表示逐元素求和：

$$
\mathcal{L}_S := 1 - \frac{\|I_S \otimes \hat I_S\|_1}{\|I_S \oplus \hat I_S - I_S \otimes \hat I_S\|_1}
$$

- geometry loss。作用是对 loss 进行 L1 正则化。

最后定义的 loss 是这三部分的加权求和：

$$
\mathcal{L} := \mathcal{L}_S + \lambda\mathcal{L}_C + \mu\mathcal{L}_G
$$

这里论文主要提了一下 color generator 的结构：

![](/uploads/paper-1/4.png)

似乎是提出了一种新结构，让颜色重建变为一个多分类问题，进而降低了时间复杂度。但是论文的描述中有若干的暂且不太清楚的名词，暂且把原文贴在这里以后再来看：

{% note info no-icon %}
Instead of directly regressing the color value, our color generator formulates color reconstruction as a classification problem that learns to reuse the pixel colors in the input image for each sampling point. Let $N_c$ denote the number of sampling points on $\boldsymbol{M}$ and $H, W$ be the height and width of the input image respectively. However, the computational cost of a naive color selection approach is prohibitive, i.e. $O(HWN_c)$. To address this challenge, we propose a novel approach to colorize mesh using a color palette, as shown in Figure 7. Specifically, after passing input image to a neural network, the extracted features are fed into (1) a sampling network that samples the representative colors for building the palette; and (2) a selection network that combines colors from the palette for texturing the sampling points. The color prediction is obtained by multiplying the color selections with the learned color palette. Our approach reduces the computation complexity to $O(N_d(HW + N_c))$, where $N_p$ is the size of color palette. With a proper setting of $N_p$, one can significantly reduce the computational cost while achieving sharp and accurate color recovery.
{% endnote %}

### Image-based Shape Fitting

似乎依然是从图像重建模型，但是这个重建任务似乎和姿态预测有较为紧密的联系。

传统方法面临的问题依然是梯度不精确的问题，这样就导致反向传播较为困难。另外，传统方法也面临这样的问题：

- 遮挡问题。传统方法中，被遮挡的三角形无法调整参数，而 SoftRas 允许被遮挡的三角形参与到运算中，所以这个问题可以解决。

- 局部最小值问题。我们希望误差反向传播可以传播到较大范围，即希望一次三维模型参数调整不局限于较小的局部，否则可能会导致模型收敛到局部最小值而非更优的结果。而 SoftRas 使用的概率分布算法允许全局的像素参与到运算中，所以理论上误差可以传播到较大范围，这个问题可以解决。

所以最后就直接使用这样的方式训练就行了：

$$
\{\rho, \theta, t\} = \mathop{\mathrm{argmin}}_{\rho, \theta, t}\|R(M(\rho, \theta, t)) - I_t\|_2
$$

这里 $\rho, \theta, t$ 是三维模型的参数，分别是非刚体形变参数、姿态角度、转换参数（或许就是视图转换的参数），$M(\cdot)$ 将这些参数转换为网格，$R(\cdot)$ 是渲染函数，$I_t$ 则是目标图像。这个训练方式也就是机器学习的通式。

## 实验结果

### Single-View Mesh Reconstruction

实验结果看确实挺好的，下面展示的是不带颜色的网格重建：

![](/uploads/paper-1/5.png)

颜色似乎也重建得挺好：

<img src="/uploads/paper-1/6.png" height="50%" width="50%" />

使用的评价方法是 3D IoU，即 3D Intersection over Union。这是一种物体检测领域的评价函数。二维情况下的 IoU 定义如下图所示。这里的矩形均指代物体的包围盒，分别为实际的包围盒和模型预测的包围盒：

<img src="/uploads/paper-1/7.png" height="50%" width="50%" />

SoftRas 的平均 3D IoU 超出了 state-of-art 的 NMR 的 3D IoU 约 4.5 点，从而说明在网格重建任务上 SoftRas 的表现超出了传统方法。

另外似乎还有一项称为 Ablation Study 的后续实验，这里省略。

### Image-based Shape Fitting

首先实验刚体结构预测，这里采用六面着不同色的正方体。给定渲染后图像，要求给出正方体应当旋转的角度。SoftRas 和 NMR 的实验结果如下：

![](/uploads/paper-1/8.png)

可以发现 SoftRas 成功获得了最后的结果，而 NMR 则收敛到了一个局部最小值无法得到正确结果。

论文认为 NMR 失败的原因正是因为六个面的相互遮挡，这导致了 NMR 无法越过局部最小值。

另外其也做了大量实验，表明 SoftRas 预测的旋转角度误差严格优于 NMR，在不优化的情况下，SoftRas 的角度误差平均比 NMR 低 $10.60^\circ$。

这里似乎涉及到 ${\rm SO}(3)$ 旋转群的一些东西，后面再来研究吧。

---

非刚体结构预测则选用了人体结构预测，这里初始化时人的右手位于背后，而目标图像中右手已经移动到前面，实验目标是将手移出来。显然 NMR 依然因为无法处理遮挡，最后没有能够完成任务。而 SoftRas 完成了：

![](/uploads/paper-1/9.png)

## 附录：梯度计算

我们现在研究一下 SoftRas 渲染出来的图像 $\boldsymbol{I}$ 对网格参数 $\boldsymbol{M}$ 的梯度，这个梯度也就是在反向传播的时候要计算的值。首先显然有：

$$
\frac{\partial\boldsymbol{I}}{\partial\boldsymbol{M}} = \frac{\partial\boldsymbol{I}}{\partial\boldsymbol{U}}\frac{\partial\boldsymbol{U}}{\partial\boldsymbol{M}} + \frac{\partial\boldsymbol{I}}{\partial\boldsymbol{Z}}\frac{\partial\boldsymbol{Z}}{\partial\boldsymbol{M}} + \frac{\partial\boldsymbol{I}}{\partial\boldsymbol{N}}\frac{\partial\boldsymbol{N}}{\partial\boldsymbol{M}}
$$

这里显然可以看出 $\dfrac{\partial\boldsymbol{U}}{\partial\boldsymbol{M}}, \dfrac{\partial\boldsymbol{Z}}{\partial\boldsymbol{M}}, \dfrac{\partial\boldsymbol{N}}{\partial\boldsymbol{M}}$ 是可以简单计算的，因为 $\boldsymbol{U}, \boldsymbol{Z}, \boldsymbol{N}$ 都可以由 $\boldsymbol{M}$ 通过简单的视图变换得到，由变换矩阵即可推算偏导数。

而根据着色模型，$\dfrac{\partial\boldsymbol{I}}{\partial\boldsymbol{N}}$ 也是可以计算的。

剩余的两个梯度中，$\dfrac{\partial\boldsymbol{I}}{\partial\boldsymbol{Z}}$ 直接和聚合函数相关，而 $\dfrac{\partial\boldsymbol{I}}{\partial\boldsymbol{U}}$ 经过了两层，分别是概率分布计算和聚合函数，所以我们需要拆解：

$$
\dfrac{\partial\boldsymbol{I}}{\partial\boldsymbol{U}} = \dfrac{\partial\boldsymbol{I}}{\partial\mathcal{D}}\dfrac{\partial\mathcal{D}}{\partial\boldsymbol{U}}
$$

### 概率分布对视图空间坐标的梯度

