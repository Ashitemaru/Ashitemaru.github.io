---
title: 《信号处理原理》学习笔记
date: 1021-09-14 13:19:01
mathjax: true
category:
    - 计算机科学
---

一门纯数学课，没有什么特别有意思的地方。但也正因为它是数学课，所以我感觉我学起来会稍微轻松一些，希望是我四大原理之中最舒服的一门课。

$$
\newcommand{\d}{\mathrm{d}}
\newcommand{\j}{\mathrm{j}}
$$

<!-- more -->

# 信号处理基本知识

信号是一种载有信息的物理量，所以可以说信号是信息的表现形式，信息是信号蕴含的内容。我们探测信号的工具称为**传感器（ Sensor ）**，传感器是将一种物理变化转化为另一种物理变化的装置。比如说为了探测声波，我们可以使用传感器将声波转化为电信号。

通过传感器我们可以将某些难以处理的信号转化为我们易于处理的信号。对信号的处理大致可以分为三类，即**降噪**、**特征抽取**以及**编码解码**。

信号常常使用函数或者序列进行描述，而函数具有图像，所以有的时候信号也会使用具体的图像进行描述。

---

信号可以按照对于给定的自变量能不能得到确定的因变量分类为**确定信号**和**随机信号**。另外，信号 $f(t)$ 也可以按照是否存在 $T > 0$ 满足 $\forall t \in \mathbb R, f(t) = f(t + T)$ 分为**周期信号**和**非周期信号**。

## 常见信号形式

**正余弦信号**是最常见的信号形式之一，其数学表达式为：

$$
\begin{aligned}
& f(t) = K \sin(\omega t + \theta) \\
& f(t) = K \cos(\omega t + \theta) \\
\end{aligned}
$$

这里 $K$ 表示**振幅**， $\omega$ 表示**角频率**， $\theta$ 表示**初相位**。

---

除去正弦余弦信号， **Sa 信号**也是常见的一种信号形式，其数学表达式为：

$$
{\rm Sa}(t) = \frac{\sin t}{t}
$$

这个信号是一个偶函数，其零点构成的集合为 $\{ k\pi \mid k \in \mathbb Z, k \neq 0 \}$ 。这个信号的积分性质为：

$$
\int_{-\infty}^{+\infty} {\rm Sa}(t) \d t = \pi
$$

这个积分一般被称为 **Dirichlet 积分**，其一种计算方式可以看下列推导。

考虑函数：

$$
F(t) = \int_{0}^{+\infty} \frac{\sin x}{x} e^{-tx} \d x
$$

这个函数的导数可以描述为：

$$
\frac{\d F}{\d t} = \frac\d{\d t} \int_{0}^{+\infty} \frac{\sin x}{x} e^{-tx} \d x = \int_{0}^{+\infty} \frac{\partial e^{-tx}}{\partial t} \frac{\sin x}{x} \d x = -\int_{0}^{+\infty} e^{-tx}\sin x \d x
$$

进一步：

$$
\begin{aligned}
\frac{\d F}{\d t} &= \int_{0}^{+\infty} e^{-tx} \d \cos x = \left. e^{-tx}\cos x \right|_{0}^{+\infty} + t \int_{0}^{+\infty} e^{-tx}\cos x \d x \\
&= -1 + t \int_{0}^{+\infty} e^{-tx} \d \sin x = -1 + t \left( \left. e^{-tx}\sin x \right|_{0}^{+\infty} + t \int_{0}^{+\infty} e^{-tx}\sin x \d x \right) \\
&= -1 + t^2 \int_{0}^{+\infty} e^{-tx}\sin x \d x = -1 - t^2 \frac{\d F}{\d t}
\end{aligned}
$$

也就是说：

$$
\frac{\d F}{\d t} = -\frac{1}{1 + t^2} \Rightarrow F(t) = -\arctan t + C
$$

不难得知 $F(+\infty) = 0$ ，这就说明 $C = \pi / 2$ ，从而 Dirichlet 积分的结果为：

$$
\int_{-\infty}^{+\infty} {\rm Sa}(t) \d t = 2F(0) = \pi
$$

---

此外，**指数信号**也较为常见，其数学表达式为：

$$
f(t) = K e^{\alpha t}
$$

指数信号的特征就是，其积分或微分后依然是指数信号。

## 复信号分析

在分析一般的信号的时候，我们可以常常将其看作某一个复信号的实部或者虚部。比如说考虑复指数信号 $f(t) = K e^{st}$ ，这里 $s \in \mathbb C$ 。显然有：

$$
f(t) = K e^{[\Re(s) + \j\Im(s)]t} = K e^{(\Re s)t} (\cos(t\Im(s)) + \j\sin(t\Im(s)))
$$

这里可以发现，**复指数信号的实部和虚部都是正余弦信号**。

## 函数空间与函数分解

我们继续沿用线性空间的基向量、正交向量组、正交基和标准正交基的概念。现在我们考虑这样的一个线性空间，即定义在 $[t_1, t_2]$ 上的可积复函数空间，定义其内积为：

$$
\langle\varphi_1, \varphi_2\rangle = \int_{t_1}^{t_2} \varphi_1(t)\varphi^*_2(t) \d t
$$

两函数如果内积为零，则称两函数**正交**。

如果函数 $\varphi_1, \varphi_2, \cdots, \varphi_n$ 满足：

$$
\langle\varphi_i, \varphi_j\rangle = \int_{t_1}^{t_2} \varphi_i(t)\varphi^*_j(t) \d t = \begin{cases}
0 & i \neq j \\
k_i & i = j
\end{cases}
$$

这里 $k_i$ 为非零常数。那么这些函数构成**正交函数集**。

称一个正交函数集是完备的，如果不存在一个这个函数集之外的函数 $\psi$ 满足 $0 < \psi \cdot \psi < +\infty$ 且 $\psi$ 和该函数集之中所有函数正交。实际上完备的正交函数集就说明了这个函数集不能容纳更多的成员。

# 信号的运算

## 常规运算

对于信号 $f_1, f_2$ ，我们可以仿照函数四则运算的规定来规定其四则运算方式：

$$
\begin{aligned}
& (f_1 + f_2)(t) = f_1(t) + f_2(t) \\
& (f_1 - f_2)(t) = f_1(t) - f_2(t) \\
& (f_1 \cdot f_2)(t) = f_1(t) \cdot f_2(t) \\
& \left(\frac{f_1}{f_2}\right)(t) = \frac{f_1(t)}{f_2(t)}
\end{aligned}
$$

另外，我们还可以对信号进行波形变换。常见的变换包括**平移变换**、**反褶变换**、**压扩变换**。

考虑平移变换：

$$
\begin{aligned}
f &\mapsto f' \\
f(t) &\to f(t - b)\\
\end{aligned}
$$

这个变换可以将信号的波形沿着横轴（时间轴）正向平移 $b$ 个单位。

反褶变换指的是下述的变换：

$$
\begin{aligned}
f &\mapsto f' \\
f(t) &\to f(-t)\\
\end{aligned}
$$

这个变换会将信号沿着纵轴翻折。

而压扩变换指的是：

$$
\begin{aligned}
f &\mapsto f' \\
f(t) &\to f(at)\\
\end{aligned}
$$

参数 $a$ 控制了压扩的形式。如果 $a > 0$ ，则压扩的时候不需要反褶。如果 $a < 0$ ，则压扩的时候需要反褶。如果 $|a| > 1$ ，则变换是压缩。如果 $|a| < 1$ ，则变换是扩张。

## 积分微分运算与能量

对于符合一定条件的信号，我们可以进行微分积分运算，记号为：

$$
\begin{aligned}
& \left(\frac{\d}{\d t} f\right)(t_0) = \left.\frac{\d f}{\d t}\right|_{t = t_0} \\
& \left(\int_{-\infty} f \d t\right)(t_0) = \int_{-\infty}^{t_0} f(t) \d t
\end{aligned}
$$

基于积分微分运算，我们可以给定信号能量和信号功率的定义。对于连续空间（即定义在全实数上）的信号 $f(t)$ ，能量定义为：

$$
E(f) = \int_{-\infty}^{+\infty} \|f(t)\|^2 \d t
$$

功率则是能量除以时间，但是考虑到时间范围是无穷大的，所以需要从极限进行定义：

$$
P(f) = \lim_{T\to+\infty}\frac{1}{T}\int_{-T / 2}^{T / 2} \|f(t)\|^2 \d t
$$

对于离散空间定义的信号 $f$ ，表达类似：

$$
\begin{aligned}
& E(f) = \sum_{n = -\infty}^{+\infty} \|f(n)\|^2 \\
& P(f) = \lim_{N\to\infty}\frac{1}{2N + 1} \sum_{n = -N}^{N} \|f(n)\|^2
\end{aligned}
$$

能量有限的信号称为能量有限信号，或在不引起混淆的时候简称**能量信号**。功率有限的信号称为功率有限信号，或在不引起混淆的时候简称**功率信号**。

## 卷积运算

对于两个连续时间信号 $f, g$ ，定义两者的卷积为：

$$
(f * g)(t) = \int_{-\infty}^{+\infty} f(t - \tau)g(\tau) \d\tau
$$

卷积的存在性取决于上述积分是否存在，并且要求积分结果是有限的。

而对于离散的时间信号 $f, g$ ，卷积定义为：

$$
(f * g)(n) = \sum_{m = -\infty}^{+\infty} f(n - m)g(m)
$$

从几何意义上讲，卷积指的是一个信号的反褶信号在时间轴上滑动的过程中，它与另外一个信号重合部分相乘得到的新信号的面积。

卷积显然符合下述运算律：

$$
\begin{cases}
f_1 * f_2 = f_2 * f_1 \\
f_1 * (f_2 + f_3) = f_1 * f_2 + f_1 * f_3 \\
(f_1 * f_2) * f_3 = f_1 * (f_2 * f_3)
\end{cases}
$$

卷积和微分积分运算的关系则为：

$$
\frac{\d}{\d t}(f_1 * f_2) = f_1 * \left(\frac{\d}{\d t} f_2\right) = \left(\frac{\d}{\d t} f_1\right) * f_2
$$

以及：

$$
\int_{-\infty} (f_1 * f_2) \d t = f_1 * \left(\int_{-\infty} f_2 \d t\right) = \left(\int_{-\infty} f_1 \d t\right) * f_2
$$

对于高阶微分积分，存在下述关系：

$$
(f_1 * f_2)^{(n)} = f_1^{(m)} * f_2^{(n - m)}
$$

上标括号中若为正整数，则表示高阶微分。若为负整数，则表示高阶积分。可以发现上述卷积的积分微分运算规律均为该公式的特殊情况。

## 相关运算

定义两个信号的相关为：

$$
R(f_1, f_2)(t) = \int_{-\infty}^{+\infty} f_1(\tau)f_2^*(\tau - t)\d\tau = \int_{-\infty}^{+\infty} f_1(\tau + t)f_2^*(\tau)\d\tau
$$

显然可以注意到：

$$
\begin{cases}
R(f_1, f_2) = \mathcal{R}[R^*(f_2, f_1)] \\
R(f_1, f_2) = f_1 * \mathcal{R}^*[f_2]
\end{cases}
$$

这里 $\mathcal{R}$ 表示反褶变换，即 $\mathcal{R}[f](t) = f(-t)$ 。

第一条定律说明了相关运算并不是交换的。

# 常用奇异信号

我们已经介绍过正弦余弦信号、指数信号等常用信号，另外还有一些形式较为特殊的信号称为奇异信号。这些信号的价值一般在于具有一些较为优越的运算特性。

首先介绍**单位斜变信号**，数学形式为：

$$
R(t) := \begin{cases}
0 & t < 0 \\
t & t \geq 0
\end{cases}
$$

另外常用的还有**截顶单位斜变信号**：

$$
R_\tau(t) := \begin{cases}
0 & t < 0 \\
t & 0 \leq t < \tau \\
\tau & t \geq \tau
\end{cases}
$$

从单位斜变信号衍生得到**单位阶跃信号**：

$$
u(t_0) = \frac{\d R}{\d t}(t_0) := \begin{cases}
0 & t_0 < 0 \\
1 & t_0 \geq 0
\end{cases}
$$

进而可以衍生出**单位矩形脉冲信号**：

$$
G_\tau(t) := \begin{cases}
1 & |t| \leq \tau / 2 \\
0 & |t| > \tau / 2
\end{cases}
$$

矩形脉冲信号和阶跃信号具有下述的关系：

$$
G_\tau(t) = u\left(t + \frac{\tau}{2}\right) - u\left(t - \frac{\tau}{2}\right)
$$

单位矩形脉冲信号的一个重要作用是作**窗函数**以截取某信号的某一段信息。对于信号 $f$ ，将其与单位矩形脉冲信号作乘法得到 $G_\tau f$ 。该信号仅有 $[-\tau / 2, \tau / 2]$ 上的信息被保留，其余均为零。也就是说单位矩形脉冲信号进行了信息截取。

---

单位阶跃信号另外一种衍生是**符号函数信号**：

$$
{\rm sgn}(t) := \begin{cases}
1 & t \geq 0 \\
-1 & t < 0
\end{cases}
$$

该信号和单位阶跃信号的关系是：

$$
{\rm sgn}(t) = 2u(t) - 1
$$

---

对单位阶跃信号求导即可得到**单位冲激信号** $\delta$ ，其定义为：

$$
\begin{aligned}
& \int_{-\infty}^{+\infty} \delta(t) \d t = 1 \\
& \delta(t) = 0 (t \neq 0)
\end{aligned}
$$

更一般的冲激信号可以用单位冲激信号定义，对于在 $t_0$ 处冲激，强度为 $E$ 的冲激信号，其表达式为：

$$
\delta_{E, t_0}(t) = E\delta(t - t_0)
$$

另外一种定义方式是利用单位矩形脉冲信号取极限，也就是保证全实数集上积分为 $1$ 的条件下令取非零值的区间尽可能小：

$$
\delta = \lim_{\tau\to 0}\frac{G_\tau}{\tau}
$$

冲激信号具有下述压扩性质：

$$
\delta(at) = \frac{1}{|a|}\delta(t) (a \neq 0)
$$

冲激信号的一个特征就是**搬移特性**：

$$
f * \delta_{1, t_0} = \mathcal{S}_{t_0}[f]
$$

这里 $\mathcal{S}_{t_0}$ 表示平移变换，即 $\mathcal{S}_{t_0}[f](t) = f(t - t_0)$ 。

上述的平移会将 $f$ 原先的原点平移到冲激信号的冲激点处。

证明则可以直接根据卷积的定义：

$$
(f * \delta_{1, t_0})(t) = \int_{-\infty}^{+\infty}f(t - \tau)\delta_{1, t_0}(\tau)\d\tau = f(t - t_0) = \mathcal{S}_{t_0}[f](t)
$$

与搬移特性原理类似的是**筛选特性**，也就是冲激信号和信号乘积的积分会得到该信号在冲激点处的值：

$$
\int_{-\infty}^{+\infty}f(t)\delta(t - t_0)\d t = f(t_0)
$$

# 信号分解

## 基本分解

对于信号 $f$ ，我们可以将其分解为直流分量和交流分量，我们规定交流分量的积分为零，即：

$$
\begin{aligned}
& f_{\rm DC}(t) = \lim_{T\to+\infty}\frac{1}{T}\int_{-T / 2}^{T / 2}f(t)\d t \\
& f_{\rm AC}(t) = f(t) - f_{\rm DC}(t) \\
\end{aligned}
$$

此外，还有奇偶分解：

$$
\begin{aligned}
f_e(t) = \frac{f(t) + f(-t)}{2} \\
f_o(t) = \frac{f(t) - f(-t)}{2} \\
\end{aligned}
$$

以及实虚分解：

$$
\begin{aligned}
f_r(t) = \frac{f(t) + f^*(t)}{2} \\
f_i(t) = \frac{f(t) - f^*(t)}{2\j} \\
\end{aligned}
$$

上述分解都是严格的，但下述的脉冲分解则是一种近似分解。脉冲分解的思想是把信号分解为若干个矩形脉冲的和，在 $[t_0, t_0 + \Delta t]$ 区间上，信号 $f$ 可以近似表示为：

$$
f(t) \approx f(t_0)[u(t - t_0) - u(t - t_0 - \Delta t)]
$$

将这些脉冲求和，则得到了信号 $f$ 的一个近似表达。

## 正交分解概述

下述讨论均基于函数平方可积的性质。如果函数 $f$ 满足：

$$
\int_{-\infty}^{+\infty} f^2(t) \d t < +\infty
$$

那么函数 $f$ 平方可积。所有的平方可积函数构成函数空间 $\mathcal{L}^2(\mathbb{R})$ 。

此外，依然沿用先前的函数内积、正交、完备正交函数集等定义。

---

我们考虑 $[t_1, t_2]$ 上的完备正交函数集 $\{\varphi_i\}$ 以及函数 $f$ ，其正交分解为：

$$
f = \sum_{k = 1}^{+\infty} c_k\varphi_k
$$

显然我们可以得到（这一步求解系数的变换称为 Karhunen-Loeve 变换）：

$$
c_k = \frac{\langle f, \varphi_k \rangle}{\langle \varphi_k, \varphi_k \rangle}
$$

据此，可以证明 Parseval 恒等式：

$$
E(f) = \int_{t_1}^{t_2} \|f(t)\|^2 \d t = \sum_{k = 1}^{+\infty} \langle \varphi_k, \varphi_k \rangle\|c_k\|^2 = \sum_{k = 1}^{+\infty} E(c_k\varphi_k)
$$

这个定理表明，在正交分解下，信号的能量等于各个分量能量的和。其证明只需要进行展开就可得到：

$$
\begin{aligned}
\int_{t_1}^{t_2} \|f\|^2 \d t &= \int_{t_1}^{t_2} \left\|\sum_{k = 1}^{+\infty} c_k\varphi_k(t)\right\|^2 \d t = \int_{t_1}^{t_2} \left(\sum_{k = 1}^{+\infty} c_k\varphi_k(t)\right)\left(\sum_{k = 1}^{+\infty} c_k\varphi_k(t)\right)^* \d t \\
&= \int_{t_1}^{t_2} \left(\sum_{k = 1}^{+\infty} c_k\varphi_k(t)\right)\left(\sum_{k = 1}^{+\infty} c_k^*\varphi_k^*(t)\right) \d t \\
&= \int_{t_1}^{t_2} \left(\sum_{k = 1}^{+\infty} c_kc_k^*\varphi_k(t)\varphi_k^*(t) + \sum_{i\neq j} c_ic_j^*\varphi_i(t)\varphi_j^*(t)\right) \d t \\
&= \sum_{k = 1}^{+\infty} \|c_k\|^2 \int_{t_1}^{t_2} \varphi_k(t)\varphi_k^*(t) \d t = \sum_{k = 1}^{+\infty} \langle \varphi_k, \varphi_k \rangle\|c_k\|^2
\end{aligned}
$$

{% note info %}
你说我展开没考虑连续、可导、收敛啥的？我们工科生不懂这些。
{% endnote %}

将信号展开为完全正交函数集的线性组合的过程即函数正交分解，求解线性组合系数的过程为**信号变换**。

## 周期信号的正交分解

现在考虑满足 Dirichlet 条件的周期信号 $f$ 。 Dirichlet 条件指的是：

- 在一个周期内间断点有限

- 在一个周期内极值点有限

- 在一个周期内绝对值积分有限

我们考虑下述两个常见的完备正交函数集，这里 $\omega = 2\pi / T$ ， $T$ 是 $f$ 的周期，下述函数集在任意的长度为 $T$ 的闭区间上正交完备：

- 三角函数集 $\{1, \cos(n\omega t), \sin(n\omega t): n \in \mathbb{N}^+\}$

- 指数函数集 $\{e^{\j n\omega t}: n \in \mathbb{Z}\}$

在这两个基底下展开得到的线性组合式可以理解为一个无穷级数，这个无穷级数就是**傅里叶级数（ Fourier series ）**。两个函数集得到的傅里叶级数分别称为三角形式的傅里叶级数和指数形式的傅里叶级数。

三角形式的傅里叶级数表示为：

$$
f(t) = a_0 + \sum_{k = 1}^{+\infty} (a_n\cos(n\omega t) + b_n\sin(n\omega t))
$$

不难根据先前的论证得到系数的计算公式，这里 $t_0$ 为任意实数：

$$
\begin{aligned}
a_0 &= \frac{\omega}{2\pi} \int_{t_0}^{t_0 + 2\pi / \omega} f(t) \d t \\
a_n &= \frac{\omega}{\pi} \int_{t_0}^{t_0 + 2\pi / \omega} f(t)\cos(n\omega t) \d t \\
b_n &= \frac{\omega}{\pi} \int_{t_0}^{t_0 + 2\pi / \omega} f(t)\sin(n\omega t) \d t \\
\end{aligned}
$$

指数形式的傅里叶级数表示为：

$$
f(t) = \sum_{k = -\infty}^{+\infty} F_ke^{\j k\omega t}
$$

其中有：

$$
F_k = \frac{\omega}{2\pi} \int_{t_0}^{t_0 + 2\pi / \omega} f(t)e^{-\j k\omega t} \d t
$$

{% note info %}
两个形式的傅里叶级数的适用范围均是全实数，但实际上这只是因为 $f$ 是周期的。

严格意义上说，由于我们给定的正交函数集只是在某个长度为 $T$ 的闭区间上正交，所以展开的傅里叶级数也只应当在这个闭区间上可用。但是由于周期性，我们可以简单地说明闭区间外这个级数表达依然成立。

这个区分是有必要的，因为严格意义上，在计算 $f$ 的能量、功率等数值的时候，积分区间均应该是长度为 $T$ 的闭区间。
{% endnote %}

三角形式和指数形式的傅里叶级数之间的关系可以考虑下述变形：

$$
\begin{aligned}
f(t) &= a_0 + \sum_{k = 1}^{+\infty} (a_n\cos(n\omega t) + b_n\sin(n\omega t)) \\
&= a_0 + \sum_{k = 1}^{+\infty} \left(a_n\frac{e^{\j n\omega t} - e^{-\j n\omega t}}{2\j} + b_n\frac{e^{\j n\omega t} + e^{-\j n\omega t}}{2}\right) \\
&= a_0 + \sum_{k = 1}^{+\infty} \left(-\j a_n\frac{e^{\j n\omega t} - e^{-\j n\omega t}}{2} + b_n\frac{e^{\j n\omega t} + e^{-\j n\omega t}}{2}\right) \\
&= a_0 + \sum_{k = 1}^{+\infty} \left(\frac{b_n - \j a_n}{2} e^{\j n\omega t} + \frac{b_n + \j a_n}{2} e^{-\j n\omega t}\right) \\
\end{aligned}
$$

也就是说这两者之间是等价的，并且可以据此发现上述系数计算公式之间的关系。

据此，我们也可以得到，如果周期信号 $f$ 是偶函数，那么其指数形式 FS 系数为实数序列。如果为奇函数，则为纯虚数序列。

---

考虑 Parseval 定律在 FS 上的应用，显然有（注意积分区间是长度为 $T$ 的闭区间）：

$$
P(f) = \sum_{k = -\infty}^{+\infty} \|F_k\|^2 = \|a_0\|^2 + \frac12 \sum_{k = 1}^{+\infty}(\|a_k\|^2 + \|b_k\|^2)
$$

## 频谱

### 频谱的定义

现在默认 FS 使用指数形式的。考虑系数序列 $\{F_n \in \mathbb C\}_{-\infty < n < +\infty}$ 。以频率为横轴，将频率所对应的 FS 系数的模为该处的频谱值，即可做出信号的幅度频谱。如果以 FS 系数的辐角作为该处的频谱值，则得到相位频谱。两个频谱合并就可以表现出该信号所有的 FS 系数的信息。

我们思考 FS 系数的意义，事实上 FS 系数作为线性组合的系数，代表了被分解信号中某一个频率的成分的多少。从而这就意味着，频谱表现的是信号在频率意义上的构成。

不过注意，由于指数形式的 FS 之中包含 $e^{-\j n\omega t}(n\in \mathbb{N}^+)$ 项，其频率为负数 $-n\omega$ ，所以信号的频谱图上会出现负频率。

我们可以断定频谱的一些性质：

- 频谱是离散的，且只会在 $n\omega(n\in \mathbb Z)$ 点处存在值

- 幅度频谱图是关于纵轴对称的，而相位频谱是关于原点对称的，这是由于 $F_k$ 与 $F_{-k}$ 共轭。

现在我们考虑一个重要周期信号的频谱，即周期矩形脉冲。

假设某一周期矩形脉冲信号 $f$ 周期为 $T_0 = 2\pi / \omega_0$ ，在区间 $[-T_0 / 2, T_0 / 2]$ 上定义为：

$$
f(t) = \begin{cases}
E & |t| \leq \tau_0 / 2 \\
0 & |t| > \tau_0 / 2
\end{cases}
$$

这里 $E > 0$ 且 $\tau_0 < T_0$ 。

其 FS 系数是显然的：

$$
F_k = \frac{\omega_0}{2\pi} \int_{-\pi / \omega_0}^{\pi / \omega_0} f(t)e^{-\j k\omega_0 t} \d t = \frac{E\omega_0}{2\pi} \int_{-\tau_0 / 2}^{\tau_0 / 2} e^{-\j k\omega_0 t} \d t = \frac{E\omega_0\tau_0}{2\pi}\frac{\sin(\omega_0 k\tau_0 / 2)}{\omega_0 k\tau_0 / 2}
$$

也就是：

$$
F_k = \frac{E\omega_0\tau_0}{2\pi}{\rm Sa}\left(\frac{\omega_0 k\tau_0}{2}\right)
$$

这个结果说明了，周期矩形脉冲信号的频谱具有包络线：

$$
F(\omega) = \frac{E\omega_0\tau_0}{2\pi}{\rm Sa}\left(\frac{\omega\tau_0}{2}\right)
$$

### 带宽

事实上我们注意到，大多的信号幅度频谱在高频区域取值很小，这也就说明大多信号在高频区域几乎不具有能量。

实际应用中，在失真可忽略的条件下，信号传递可以只传递低频分量（一般而言取频谱包络线最小绝对值的零点以内的分量），这一段区间的长度称为**频带宽度**，简称**带宽**。

## 非周期信号的正交分解和频谱

任何非周期信号 $f$ 实际上都可以看成周期无穷大的周期信号。我们考虑周期为 $T$ 的周期信号 $f_T$ ，其在周期 $[-T / 2, T / 2)$ 上定义为 $f_T(t) = f(t), t \in [-T / 2, T / 2)$ 。实际上可以得到：

$$
\lim_{T \to +\infty} f_T = f
$$

{% note info %}
我们没有定义过一个函数列如何收敛为一个函数，故这样的表述并非严谨。

后续很多证明也仅仅是形式化的，其作用仅仅是形成对定律的直观理解，请不要将这些推导用于严谨的证明之中。
{% endnote %}

考虑 $f_T$ 的傅里叶级数，如果令 $\omega = 2\pi / T$ ，显然有：

$$
\begin{aligned}
f_T(t) &= \sum_{k = -\infty}^{+\infty} F_k e^{\j k\omega t} = \sum_{k = -\infty}^{+\infty} \left(\frac{1}{T}\int_{-T / 2}^{T / 2} f_T(\tau)e^{-\j k\omega\tau} \d\tau\right)e^{\j k\omega t}
\end{aligned}
$$

考虑离散频谱中相邻谱线之间的间隔，即 $\Delta\omega = \omega = \dfrac{2\pi}{T}$ ，在 $T \to +\infty$ 的时候谱线间隔 $\Delta\omega \to 0$ ，那么我们有：

$$
\begin{aligned}
f(t) &= \lim_{T \to +\infty} f_T(t) = \lim_{T \to +\infty} \sum_{k = -\infty}^{+\infty} \left(\frac{1}{T}\int_{-T / 2}^{T / 2} f_T(\tau)e^{-\j k\omega\tau} \d\tau\right)e^{\j k\omega t} \\
&= \frac{1}{2\pi} \lim_{\Delta\omega \to 0} \sum_{k = -\infty}^{+\infty} \left(\int_{-T / 2}^{T / 2} f_T(\tau)e^{-\j k\omega\tau} \d\tau\right)e^{\j k\omega t} \Delta\omega
\end{aligned}
$$

现在我们定义：

$$
\begin{aligned}
F_T(x) &= \int_{-T / 2}^{T / 2} f_T(\tau)e^{-\j x\tau} \d\tau \\
F(x) &= \int_{-\infty}^{+\infty} f(\tau)e^{-\j x\tau} \d\tau
\end{aligned}
$$

显然有：

$$
\lim_{\Delta\omega \to 0} F_T = F
$$

该定义下，可以有：

$$
f(t) = \frac{1}{2\pi} \lim_{\Delta\omega \to 0} \sum_{k = -\infty}^{+\infty} F_T(k\omega) e^{\j k\omega t} \Delta\omega
$$

下一步则是将该极限转写为积分。但我们注意这样的一点，即 $\Delta\omega$ 在变动的时候，被积函数 $F_T$ 本身也在变动。按照严格的数学理论，这里我们应当严格说明收敛性。但我们只需要直观理解，具体的证明则省略：

$$
f(t) = \frac{1}{2\pi} \lim_{\Delta\omega \to 0} \sum_{k = -\infty}^{+\infty} F_T(k\omega) e^{\j k\omega t} \Delta\omega = \frac{1}{2\pi} \int_{-\infty}^{+\infty} F(\omega) e^{\j\omega t} \d\omega
$$

---

上述推导中，我们实际上已经给出了一个函数 $F(\omega)$ 和信号 $f(t)$ 之间的关系：

$$
\begin{aligned}
f(t) &= \frac{1}{2\pi} \int_{-\infty}^{+\infty} F(\omega) e^{\j\omega t} \d\omega \\
F(\omega) &= \int_{-\infty}^{+\infty} f(t)e^{-\j\omega t} \d t
\end{aligned}
$$

这里我们可以给出函数 $F(\omega)$ 的一个理解，我们将其和 FS 比较：

$$
\begin{aligned}
f(t) &= \sum_{k = -\infty}^{+\infty} F_ke^{\j k\omega t} \\
f(t) &= \frac{1}{2\pi} \int_{-\infty}^{+\infty} F(\omega) e^{\j\omega t} \d\omega \\
\end{aligned}
$$

可以发现上述用 $F(\omega)$ 推出 $f(t)$ 的公式实际上类似于将 $f$ 表达为正交函数的线性组合，只不过这里的正交函数集是无穷集。这里 $F(\omega)$ 实际上相当于信号 $f(t)$ 的频谱，即其频域表现。

由 $f$ 推出 $F$ 的过程就称为**傅立叶变换（ Fourier transformation ）**，简称 FT 。反过来由 $F$ 推出 $f$ 则称为**傅立叶逆变换**，简称 IFT 。标记为：

$$
F = \mathcal{F}[f], f = \mathcal{F}^{-1}[F]
$$