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
\newcommand{\b}{\boldsymbol}
\newcommand{\lv}{\left[\begin{matrix}}
\newcommand{\rv}{\end{matrix}\right]}
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

# 一般模拟信号分解

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

- 频谱是离散的，且只会在 $n\omega(n\in \mathbb Z)$ 点处存在值，这些具有值的频率称为**谐波频率（ Harmonic frequency ）**。

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

### FT 表达式的推理

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

### FT 的运算性质

FT 显然是线性变换：

$$
\mathcal{F}\left[\sum_{k = 1}^n \lambda_kf_k\right] = \sum_{k = 1}^n \lambda_k\mathcal{F}[f_k]
$$

下述论述中，记 $F := \mathcal{F}[f]$ 。

其与反褶变换 $\mathcal{R}$ 具有以下性质：

$$
\begin{cases}
\mathcal{F}\mathcal{R}[f] = \mathcal{R}[F] \\
\mathcal{F}[f^*] = \mathcal{R}^*[F] \\
\mathcal{F}\mathcal{R}^*[f] = F^* \\
\end{cases}
$$

即时域上反褶等价于频域反褶，时域上共轭等价于频域反褶共轭，时域上反褶共轭等价于频域共轭。

而对于压扩变换 $\mathcal{E}_a$ ，这里 $\mathcal{E}_a[f](t) = f(at)$ ，我们有：

$$
\mathcal{F}\mathcal{E}_a[f] = \frac{1}{|a|}\mathcal{E}_{\frac{1}{a}}[F]
$$

对于平移变换 $\mathcal{S}_{t_0}$ ，我们有（这里符号 $\mathcal{F}$ 的两个下标分别代表时域和频域使用的自变量符号）：

$$
\mathcal{F}_{t, \omega}[f(t - t_0)] = F(\omega)e^{-\j\omega t_0}
$$

综合压扩变换和平移变换：

$$
\mathcal{F}_{t, \omega}[f(at - t_0)] = \frac{1}{|a|}F\left(\frac{\omega}{a}\right)e^{-\j\omega t_0 / a}
$$

综合上述可以看出，时域的压扩会导致频域相反的压扩，并且会伴随幅度的变化。而时域的平移不影响幅度频谱，但是相位频谱会发生改变。

{% note info %}
这里使用 $\mathcal{F}_{t, \omega}$ 实在是折中之举。我自己一向是想要明确函数和函数值的区别的，比如说对函数的变换应该使用花写字体，使用中括号。按照这样的思路， $\mathcal{F}[f(at - t_0)]$ 是错误表达，因为方括号内实际上是函数值而非一个函数。

但是由于通用数学符号很多时候并没有区分两者，所以为了避免使用更多自定义符号导致难以阅读，还是暂且使用 $\mathcal{F}_{t, \omega}$ 这样的折中符号。
{% endnote %}

实际上我们注意到 FT 和 IFT 具有相同的数学结构，那么如果在时域上乘以复指数，频域则会发生平移：

$$
\mathcal{F}_{t, \omega}[f(t)e^{\j\omega_0 t}] = F(\omega - \omega_0)
$$

结合压扩变换得到：

$$
\mathcal{F}_{t, \omega}\left[\frac{1}{|a|}f\left(\frac{t}{a}\right)e^{\j\omega_0 t / a}\right] = F(a\omega - \omega_0)
$$

---

FT 和微分积分运算关系大致如下。

考虑时域上的微分：

$$
\mathcal{F}_{t, \omega}\left[f^{(1)}(t)\right] = \j\omega F(\omega)
$$

考虑频域上的微分：

$$
\mathcal{F}^{-1}_{t, \omega}\left[F^{(1)}(\omega)\right] = -\j tf(t)
$$

考虑时域上的积分：

$$
\mathcal{F}_{t, \omega}\left[f^{(-1)}(t)\right] = \frac{1}{\j\omega}F(\omega) + \pi F(0)\delta(\omega)
$$

考虑频域上的积分：

$$
\mathcal{F}^{-1}_{t, \omega}\left[F^{(-1)}(\omega)\right] = -\frac{1}{\j t}f(t) + \pi f(0)\delta(t)
$$

---

而 FT 和卷积的关系就很明朗，时域与频域相对，其中一个域上的乘积直接对应另一个域上的卷积。不过需要注意可能出现的常系数：

$$
\begin{aligned}
& \mathcal{F}[f_1 * f_2] = \mathcal{F}[f_1] \cdot \mathcal{F}[f_2] \\
& \mathcal{F}[f_1 \cdot f_2] = \frac{1}{2\pi} \mathcal{F}[f_1] * \mathcal{F}[f_2] \\
\end{aligned}
$$

FT 和相关运算的关系为：

$$
\begin{aligned}
& \mathcal{F}[R(f_1, f_2)] = F_1F_2^* \\
& \mathcal{F}[R(f, f)] = \|F\|^2
\end{aligned}
$$

{% note info %}
使用这个数学关系就可以得到矩形窗截取可能导致频域畸形的理论解释。我们提到过矩形脉冲乘以某一个信号可以用于截取信号指定区间的信息。但是截取后的信号在频域上的表现则会畸形，考虑下述关系：

$$
\mathcal{F}[G_\tau f] = \frac{1}{2\pi} \mathcal{F}[G_\tau] * \mathcal{F}[f]
$$

而 $\mathcal{F}[G_\tau]$ 是 ${\rm Sa}$ 函数的衍生函数，其和原信号频谱的卷积可能导致频域畸变。
{% endnote %}

### FS 和 FT 的关系

我们可以这样研究 FS 和 FT 的关系，考虑非周期信号 $f$ ，其在区间 $[-T / 2, T / 2]$ 外取值均为零。定义周期为 $T$ 的周期信号 $\tilde{f}$ ，其在周期 $[-T / 2, T / 2]$ 上有 $\tilde{f} = f$ 。

考虑 $\tilde{f}$ 的 FS 系数（这里 $\omega := 2\pi / T$ ）：

$$
F_k = \frac{1}{T} \int_{-T / 2}^{T / 2} \tilde{f}(t)e^{-\j k\omega t} \d t = \frac{1}{T} \int_{-\infty}^{+\infty} f(t)e^{-\j k\omega t} \d t = \frac{1}{T} F(k\omega)
$$

也就是说如果将一个脉冲式的信号（即除了某一个有限区间外取值均为零）扩展为一个周期信号，那么周期信号的离散频谱的包络线和脉冲信号的连续频谱仅仅相差一个常数倍数。

比如说上述求解过的周期矩形脉冲信号，如果我们仅仅关注一个周期内的矩形脉冲，其连续频谱的表达式为（和之前的论证使用相同的变量符号）：

$$
F(\omega) = E\tau_0{\rm Sa}\left(\frac{\omega\tau_0}{2}\right)
$$

### 准周期信号

有一些非周期信号可能会具有类似周期信号的时域重复结构，即可以将时域均匀划分为准周期，每一个准周期内信号的结构相互类似，这类信号就是准周期信号，如果其准周期的长度为 $T$ ，一般会将 $f_0 = 1 / T$ 称为准周期信号的**基频（ Pitch ）**。

准周期信号的频谱特征也介于周期信号频谱和非周期信号频谱之间，即其频谱依然连续，但是在谐波频率处具有明显的尖峰。谐波频率处的尖峰就是准周期信号的一大特征。

### 周期信号的 FT

鉴于 FS 和 FT 具有相同的本质，而周期信号的频谱表现是离散的，我们可以预料到对周期信号求解 FT 会得到若干离散的冲激函数之和。

比如说根据：

$$
\frac{1}{2\pi} \int_{-\infty}^{+\infty} (2\pi\delta(\omega - \omega_0)) e^{\j\omega t} \d\omega = e^{\j\omega_0 t}
$$

就可以得到复指数信号的 FT 为：

$$
\mathcal{F}_{t, \omega}[e^{\j\omega_0 t}] = 2\pi\delta(\omega - \omega_0)
$$

进一步可以推出：

$$
\begin{aligned}
& \mathcal{F}_{t, \omega}[\cos\omega_0 t] = \pi(\delta(\omega + \omega_0) + \delta(\omega - \omega_0)) \\
& \mathcal{F}_{t, \omega}[\sin\omega_0 t] = \j\pi(\delta(\omega + \omega_0) - \delta(\omega - \omega_0)) \\
\end{aligned}
$$

### 其他常用函数的 FT

考虑冲激函数的 FT ：

$$
\mathcal{F}[\delta_{E, 0}](\omega) = \int_{-\infty}^{+\infty} E\delta(t)e^{-\j\omega t} \d t = E
$$

也就是说冲激函数的傅立叶变换为常函数。这意味着冲激信号的频谱是处处均匀的，这种频谱一般称为**白色谱**或者**均匀谱**。

对冲激函数做 IFT 得到：

$$
\mathcal{F}^{-1}[\delta_{E, 0}](t) = \frac{1}{2\pi} \int_{-\infty}^{+\infty} E\delta(\omega) e^{\j\omega t} \d\omega = \frac{E}{2\pi}
$$

也就是说常函数的傅立叶变换是在原点的冲激函数，这是好理解的，因为常函数就是频率为零的三角函数。

上述推理说明了：

$$
\begin{aligned}
& \mathcal{F}[\delta] = 1 \\
& \mathcal{F}[1] = 2\pi\delta \\
\end{aligned}
$$

# 信号采样

## 采样的概念

我们使用计算机存储时域上连续的信号的时候，只能存储部分点处的信号幅度，这就要求我们决定存储哪些点的信号值。这类每隔一定的时间间隔，从连续信号上取出该点信号幅度的行为就是**采样**。每次采样之间的时间间隔称为**采样周期**，一般标记为 $T_s$ 。其倒数 $f_s = 1 / T_s$ 即称为**采样频率**， $\omega_s = 2\pi / T_s$ 则是**采样（角）频率**。

理想情况下，我们一般使用冲激串采样，即使用函数：

$$
p(t) = \sum_{k = -\infty}^{+\infty} \delta(t - kT_s)
$$

使用 $p(t)$ 乘以需要采样的信号 $f(t)$ 即可得到采样的结果。

## 采样定理

现在考虑理想冲激串采样，我们考虑采样后信号的频谱表现。记：

$$
f_p(t) := f(t)p(t) = \sum_{k = -\infty}^{+\infty} f(t)\delta(t - kT_s)
$$

那么根据 FT 和卷积的关系（这里规定 $F := \mathcal{F}[f]$ ）：

$$
\begin{aligned}
\mathcal{F}[f_p] &= \frac{1}{2\pi}F * \mathcal{F}[p]
\end{aligned}
$$

这里我们需要求解 $\mathcal{F}[p]$ ，即冲激串的傅立叶变换。由于冲激串函数实际上是周期函数，所以可以得知其频谱表现也是冲激串。我们可以求解其 FS 系数（这里 $\omega_s := 2\pi / T_s$ ）：

$$
F_k = \frac{1}{T_s} \int_{-T_s / 2}^{T_s / 2} p(t)e^{-\j k\omega_s t} \d t = \frac{1}{T_s}
$$

将 $p(t)$ 写成 FS 得到：

$$
p(t) = \sum_{k = -\infty}^{+\infty} \frac{1}{T_s}e^{\j k\omega_s t}
$$

所以说可以有：

$$
\mathcal{F}[p](\omega) = \mathcal{F}_{t, \omega}\left[\sum_{k = -\infty}^{+\infty} \frac{1}{T_s}e^{\j k\omega_s t}\right] = \frac{1}{T_s} \sum_{k = -\infty}^{+\infty} \mathcal{F}_{t, \omega}[e^{\j k\omega_s t}] = \frac{2\pi}{T_s} \sum_{k = -\infty}^{+\infty} \delta(\omega - k\omega_s)
$$

也就是说时域的冲激串在频域上也是冲激串。

{% note info %}
如果你尝试这样求解 $\mathcal{F}[p]$ ：

$$
\mathcal{F}[p] = \mathcal{F}\left[\sum_{k = -\infty}^{+\infty} \delta_{1, kT_s}\right] = \sum_{k = -\infty}^{+\infty} \mathcal{F}[\delta_{1, kT_s}]
$$

显然这会得到错误的结果。错误的原因是这里的级数和傅立叶变换这里不能交换（而上面先写成 FS 再 FT 的时候，级数和傅立叶变换又是可以交换的），而更深层次的原因则是冲激串严格上不具有 FT （不满足 Dirichlet 条件）。

实际上如果从更深层的意义上说，我们根本没有严格定义冲激函数，毕竟我们能看出来冲激函数根本不可能使用 Riemann 积分计算，所以还有更深层的数学原理被忽视了。

但一定程度上，这就是工科。想要钻研这些理论内部严格数学原理的读者可以阅读拓扑、泛函、实分析相关内容。
{% endnote %}

那么就可以计算取样后信号的频谱了：

$$
\mathcal{F}[f_p] = \frac{1}{2\pi}F * \mathcal{F}[p] = \frac{1}{T_s}F * \left(\sum_{k = -\infty}^{+\infty} \delta_{1, k\omega_s}\right) = \frac{1}{T_s}\sum_{k = -\infty}^{+\infty} \mathcal{S}_{k\omega_s}[F]
$$

也就是说抽样后的信号的频谱是将原先的频谱以 $\omega_s$ 为周期平移后叠加后得到的。

---

现在我们考虑现实中常见的信号。常见信号往往具有一个**高频截止频率** $\omega_M$ ，其含义为信号的频谱 $F$ 在区间 $[-\omega_M, \omega_M]$ 外均取零。此外，之前也有提到过带宽相关知识，即大多数信号的能量聚集在低频区段，所以往往会截断高频的部分，而这个截断频率，也可以称为高频截止频率。这类信号频谱中位于区间 $[-\omega_M, \omega_M]$ 内的频谱峰称为**主峰**。

对一个高频截止频率为 $\omega_M$ 的信号按照频率 $\omega_s$ 采样，采样后的频谱记为 $F_p$ ，其是周期为 $\omega_s$ 的周期函数，每一个周期都是由原先信号的频谱主峰平移而来。我们现在固定高频截止频率，降低采样频率，考察 $F_p$ 的变化。在 $\omega_s$ 降低的时候， $F_p$ 周期减小，峰与峰之间的距离减小，从而存在一个临界取样频率，让相邻的两个频谱峰恰好相交。如果取样频率低于这个临界频率，就意味着 $F_p$ 的各个相邻的频谱峰会相互重叠，即发生**频谱混叠**。

如果没有发生频谱混叠，我们可以在采样之后使用低通滤波器将 $F_p$ 的主峰过滤出来，从而完美还原原先的信号。但是如果发生混叠，这种完美复原则是不可能的。而这个临界频率是显而易见的，即 $\omega_s = 2\omega_M$ 。

这就是 Nyquist 取样定理，即使用不低于两倍高频截止频率 $\omega_M$ 的采样频率 $\omega_s$ 采样得到的数据点可以完美还原原信号。

综合上述，只有信号满足**频带受限**（即严格具有高频截止频率）并且**取样频率足够高**的时候，取样才能无损表示原有信号。

## 信号复原（内插）

思考从采样序列恢复原先连续信号的方法，这其实就是**插值法**的应用，只不过在信号处理原理中我们将之称为内插法。

现在我们有信号 $x(t)$ 以抽样周期 $T$ 抽样得到的抽样序列 $\{x(nT)\}_{n \in \mathbb Z}$ 。进行内插之前，我们需要确定内插函数 $h(t)$ ，而内插函数一般就是我们使用的滤波器的单位冲激响应函数。随后用下述方法计算出内插后的连续信号 $\tilde x(t)$ ：

$$
\tilde x(t) := \sum_{n = -\infty}^{+\infty} x(nT)h(t - nT) \approx x(t)
$$

使用不同的滤波器对应的内插函数 $h(t)$ ，我们能获取到不同效果的复原信号 $x(t)$ 。

{% note info %}
这里需要尝试解释一下滤波器、单位冲激响应、内插这些概念。

首先简单理解一下**系统（ System ）**的概念。系统可以简单理解为对给定输入（称之为**激励**）给出给定输出（称之为**响应**）的，具有特定功能的整体。

滤波器本质上就是一个对信号函数的变换，也是一种系统。只不过这个变换很多时候是用于过滤信号频谱之中的低频（或高频）成分的，所以才会有低通滤波器（或高通滤波器）之类的名称。其激励就是要过滤的信号，响应就是过滤后的信号。根据我们对滤波器性质的要求，我们至少需要滤波器是线性系统，满足激励叠加等于响应叠加以及激励倍增等于响应倍增。

而单位冲激响应就是一种特殊的响应。如果某一个滤波器的激励为单位冲激信号，其在这个条件下的响应就是单位冲激响应。

现在考虑一个滤波器，其对应的函数变换为 $\mathcal{P}$ ，其单位冲激响应为 $h$ ，即 $\mathcal{P}[\delta] = h$ 。滤波器的线性保证了下述的性质：

$$
\begin{cases}
\mathcal{P}[f + g] = \mathcal{P}[f] + \mathcal{P}[g] \\
\mathcal{P}[\lambda f] = \lambda\mathcal{P}[f]
\end{cases}
$$

接下来我们证明**任何激励和单位冲激响应的卷积就是该激励下滤波器的响应**。这个结论说明了单位冲激响应携带了一个线性系统所有的信息，即只需要知晓一个线性系统的单位冲激响应就可以计算任何激励下的系统响应。本证明完全不理会系统函数变换和极限、级数是否可交换，工科生不管这些：

$$
\begin{aligned}
\mathcal{P}[x] &= \mathcal{P}[x * \delta] = \mathcal{P}\left[\int_{-\infty}^{+\infty} x(\tau)\delta(\cdot - \tau) \d\tau\right] = \mathcal{P}\left[\lim_{\Delta \to 0} \sum_{k = -\infty}^{+\infty} x(k\Delta)\delta(\cdot - k\Delta) \Delta\right] \\
&= \lim_{\Delta \to 0} \sum_{k = -\infty}^{+\infty} x(k\Delta) \mathcal{P}[\delta_{1, k\Delta}]\Delta = \lim_{\Delta \to 0} \sum_{k = -\infty}^{+\infty} x(k\Delta) h(\cdot - k\Delta)\Delta \\
&= \int_{-\infty}^{+\infty} x(\tau)h(\cdot - \tau) \d\tau = x * h
\end{aligned}
$$

这里第一行到第二行直接默认系统函数变换和极限可交换且无穷求和下系统线性依然可以保证。

现在我们讨论上述说明的这些和信号复原（内插）是什么关系。我们已经说明了在符合取样定理的条件下，时域取样等价于频域周期延拓，所以我们要复原信号，只需要在频域上过滤出主峰即可。这个时候就需要使用到低通滤波器，我们假设使用的低通滤波器具有单位冲激响应 $h(t)$ 。而这个滤波器的输入信号就是取样周期为 $T$ 的理想冲激串对原信号 $x(t)$ 取样得到的取样信号：

$$
x_p(t) = \sum_{k = -\infty}^{+\infty} x(kT)\delta(t - kT)
$$

我们考虑 $x_p(t)$ 通过滤波器处理后的输出，根据上述结论，该输出就是 $x_p$ 与 $h$ 的卷积，这里证明依然默认级数和无穷积分可交换：

$$
\begin{aligned}
(x_p * h)(t) &= \int_{-\infty}^{+\infty} x_p(\tau)h(t - \tau) \d\tau = \int_{-\infty}^{+\infty} \left(\sum_{k = -\infty}^{+\infty} x(kT)\delta(\tau - kT)\right) h(t - \tau) \d\tau \\
&= \sum_{k = -\infty}^{+\infty} x(kT) \int_{-\infty}^{+\infty} h(t - \tau)\delta(\tau - kT) \d\tau = \sum_{k = -\infty}^{+\infty} x(kT)h(t - kT)
\end{aligned}
$$

这个结果和我们介绍的使用内插函数 $h$ 对 $x$ 的取样序列进行内插法得到 $\tilde x$ 的公式一致。

其实内插法和使用滤波器滤波本质上在叙说同样的概念，只不过内插法是从时域的角度观察，即这样操作之后时域上缺失的数值点就会被插入回来，从而复原信号。而滤波是从频域的角度观察，即这样的操作本质上是在频域上过滤出低频峰。内插和滤波其实就是信号复原这个操作两个角度下观察的结果。
{% endnote %}

在此基础上我们继续介绍。为了复原初始连续信号，最理想的情况是使用理想低通滤波器，其频域表现为矩形窗，时域表现为 Sa 函数。其只需要在时域上将取样后的信号卷积上一个 Sa 函数即可获取原先的连续信号。

但是这里我们介绍两个不完全恢复的内插函数，即**零阶保持内插**和**一阶保持内插（线性内插）**。零阶保持内插的内插函数为：

$$
h(t) = \begin{cases}
1 & 0 \leq t \leq T \\
0 & {\rm otherwise}
\end{cases}
$$

这里 $T$ 是取样周期。可以发现，内插函数是时域上的矩形窗，将其代入 $\tilde x$ 的公式会发现其复原出的信号大致为：

![](/uploads/note-of-dsp/1.png)

这里灰色线是原先的信号 $x$ ，红色线则是内插后得到的 $\tilde x$ 。这样的内插在每一个抽样周期内保持了抽样值本身，最终复原得到了类似阶跃的信号。

而一阶保持内插则是使用线性的方式插值，其内插函数为三角窗：

$$
h(t) = \begin{cases}
\dfrac{t}{T} + 1 & -T \leq t < 0 \\
-\dfrac{t}{T} + 1 & 0 \leq t \leq T \\
0 & {\rm otherwise}
\end{cases}
$$

其内插复原的信号则为：

![](/uploads/note-of-dsp/2.png)

可见其效果为直接使用线段连接各取样值以近似原信号。

---

上述讨论完全基于采样满足采样定理，现在讨论采样频率过低导致频谱混叠的**欠采样**情况。此时即使采用理想低通滤波也无法获取原信号，但是我们可以说明，无论如何内插，采样点处的值保持。即无论采用何种内插函数 $h$ ，得到的 $\tilde x$ 都保证 $\tilde x(kT) = x(kT)(k \in \mathbb Z)$ 。

{% note warning %}
事实上这个性质要求内插函数 $h$ 满足这样的条件：

$$
\begin{cases}
h(0) = 1 \\
h(kT) = 0, k \in \mathbb Z \backslash \{0\}
\end{cases}
$$

事实上我们上面的叙述也只能为大家建立内插和滤波之间的一个直观感受，但是严格而言内插和滤波并非是一对完全严格对应的概念。内插法，或者说插值，天然要求插值后采样点处的函数值不改变，但是滤波并不严格保证这一点，比如说令滤波器的单位脉冲响应为 $h(t) \equiv 1$ ，就不能保证采样点处函数值不变了。
{% endnote %}

# 离散信号处理

上述的讨论均是理论的，理论的模拟信号时域连续、时域无限长、数值取值范围无限、精度无限。但是实际使用计算机处理的时候，这三点均无法满足，即计算机能处理的信号时域离散、时域有限长、数值取值范围有限、精度有限。

所以我们需要将上述的理论进行一定的修正才能应用到真实的信号处理场景中。

## 从抽样序列还原原信号时域

这一问题已经在采样部分叙述过，频带有限信号的采样频率足够高的时候，就可以根据抽样序列无损还原原信号。

## 从抽样序列还原原信号频谱

连续时域信号通过抽样可以得到离散时域信号。考虑具有高频截止频率 $\omega_M$ 的频带有限信号 $f$ ，其抽样信号 $f_p$ 定义为下式，这里 $T_s$ 是抽样周期：

$$
f_p(t) = \sum_{n = -\infty}^{+\infty} f(nT_s)\delta(t - nT_s)
$$

我们先前计算过抽样信号的 FT ，这里 $\omega_s$ 是抽样频率， $F$ 是 $f$ 的频谱：

$$
F_p(\omega) = \frac{1}{T_s} \sum_{m = -\infty}^{+\infty} F(\omega - m\omega_s)
$$

在不发生频谱混叠的时候，抽样信号的频谱的主峰严格和原信号的频谱仅相差常数系数。也就是说在满足抽样定理的时候：

$$
F_p(\omega) = \frac{1}{T_s} F(\omega), \omega \in \left[-\frac{\omega_s}{2}, \frac{\omega_s}{2}\right] 
$$

我们形式上根据 FT 的计算公式可以推理得到下述关系，这里形式上将级数和无穷积分认为是可以交换的，所以严格而言是不严谨的推理：

$$
\begin{aligned}
F_p(\omega) &= \int_{-\infty}^{+\infty} f_p(t)e^{-\j\omega t} \d t = \int_{-\infty}^{+\infty} \left(\sum_{n = -\infty}^{+\infty} f(nT_s)\delta(t - nT_s)\right)e^{-\j\omega t} \d t \\
&= \sum_{n = -\infty}^{+\infty} f(nT_s) \left(\int_{-\infty}^{+\infty} \delta(t - nT_s)e^{-\j\omega t} \d t\right) = \sum_{n = -\infty}^{+\infty} f(nT_s) e^{-\j\omega nT_s} \d t
\end{aligned}
$$

那么：

$$
F(\omega) = T_sF_p(\omega) = T_s\sum_{n = -\infty}^{+\infty} f(nT_s) e^{-\j\omega nT_s}, \omega \in \left[-\frac{\omega_s}{2}, \frac{\omega_s}{2}\right] 
$$

从而我们就有从抽样信号序列复原原信号频谱的方式，这种变换相当于从离散信号作傅立叶变换，称为**离散时间傅立叶变换（ Discrete Time Fourier Transformation / DTFT ）**。

考虑其逆变换，即从原信号的频谱获取抽样信号。事实上 DTFT 的式子可以认为是一个 FS ， $\{f(nT_s)\}_{n \in \mathbb Z}$ 可以认为是频域上的 FS 系数数列，原信号周期为 $\omega_s$ ，不过注意这里指数上是 $-\j\omega nT_s$ ，是有负号的。那么根据 FS 系数的公式：

$$
f(nT_s) = \frac{1}{\omega_s} \int_{-\omega_s / 2}^{\omega_s / 2} F_p(\omega)e^{\j n\omega T_s} \d\omega
$$

现在我们尝试进行归一化。由于 $T_s$ 实质上只是作为系数出现，所以可以将其归一化为 $1$ 。而原先的采样序列记作数列 $\{x(n)\}_{n \in \mathbb Z}$ ，这种归一化后的采样序列一般称为**数字信号（ Digital signal ）**。在这样的表示之下考虑 DTFT 和 IDTFT ，我们将 DTFT 的结果记作 $X(\omega)$ ，这也称为数字信号的频谱：

$$
\begin{aligned}
& X(\omega) = {\rm DTFT}[x](\omega) = \sum_{n = -\infty}^{+\infty} x(n) e^{-\j\omega n} \\
& x(n) = \frac{1}{2\pi} \int_{-\pi}^{\pi} X(\omega)e^{\j n\omega} \d\omega
\end{aligned}
$$

根据上述推理，很容易知晓数字信号的频谱一定是周期的（根据 FT 和 IFT 的对偶，实际上根据周期信号的频谱离散就不难理解），并且由于进行了归一化处理，所以这个周期一定是 $2\pi$ ：

$$
X(\omega + 2\pi) = X(\omega)
$$

DTFT 的运算法则和 FT 有很大类似之处。首先有 DTFT 是线性的：

$$
{\rm DTFT}\left[\sum_{k = 1}^n \lambda_kx_k\right] = \sum_{k = 1}^n \lambda_k{\rm DTFT}[x_k]
$$

时域和频域的平移均会导致另外一个域的相位平移：

$$
\begin{aligned}
& {\rm DTFT}_{n, \omega}[x(n - n_0)] = X(\omega)e^{-\j\omega n_0} \\
& {\rm DTFT}_{n, \omega}[x(t)e^{\j\omega_0 n}] = X(\omega - \omega_0)
\end{aligned}
$$

其和反褶、共轭的关系也和 FT 类似：

$$
\begin{cases}
{\rm DTFT}[\mathcal{R}x] = \mathcal{R}[X] \\
{\rm DTFT}[x^*] = \mathcal{R}^*[X] \\
{\rm DTFT}[\mathcal{R}^*x] = X^* \\
\end{cases}
$$

压扩变换不能直接应用到数字信号上，所以我们给出时域扩展的定义：

$$
\mathcal{E}_a[x](n) = \begin{cases}
x(k) & n = ka, k \in \mathbb Z \\
0 & {\rm otherwise}
\end{cases} \ (a \in \mathbb{Z} \backslash \{0\})
$$

和 FT 类似，时域扩展的 DTFT 满足：

$$
{\rm DTFT}_{n, \omega}[\mathcal{E}_a[x](n)] = X(a\omega)
$$

DTFT 我们只探讨频域微分，和 FT 类似，频域微分等价于时域上的加权求和：

$$
{\rm DTFT}_{n, \omega}[nx(n)] = \j X^{(1)}(\omega)
$$

讨论卷积的时候，就不得不考虑到 DTFT 得到的频谱是周期的，普通的卷积可能不收敛。所以这里定义**圆卷积**，直观上而言就是指考虑一个周期内的卷积。比如说周期为 $T$ 的两个周期信号 $x, y$ 的圆卷积定义为：

$$
(x\otimes y)(t) := \int_T x(\tau)y(t - \tau) \d\tau
$$

那么：

$$
\begin{aligned}
& {\rm DTFT}[x_1 * x_2] = {\rm DTFT}[x_1] \cdot {\rm DTFT}[x_2] \\
& {\rm DTFT}[x_1 \cdot x_2] = \frac{1}{2\pi} {\rm DTFT}[x_1] \otimes {\rm DTFT}[x_2] \\
\end{aligned}
$$

Parseval 定律依然类似成立：

$$
\sum_{n = -\infty}^{+\infty} \|x(n)\|^2 = \frac{1}{2\pi} \int_{-\pi}^{\pi} \|X(\omega)\|^2 \d\omega
$$

## 从有限抽样序列还原信号频谱

现在我们基本上解决了时域离散的问题，但是我们要考虑到计算机处理信号的时候时域不可能无限。

我们考虑对数字信号加上一个窗函数：

$$
w(n) := \begin{cases}
1 & 0 \leq n \leq L - 1 \\
0 & {\rm otherwise}
\end{cases}
$$

那么 $L$ 长的有限长度数字信号就可以表示为：

$$
x_L = x \cdot w
$$

那么显然根据 DTFT 的性质得到 $x_L$ 的 DTFT 为：

$$
X_L(\omega) = \frac{1}{2\pi} ({\rm DTFT}[x] \otimes {\rm DTFT}[w])(\omega)
$$

显然：

$$
\begin{aligned}
{\rm DTFT}[w](\omega) &= \sum_{n = -\infty}^{+\infty} w(n)e^{-\j\omega n} = \sum_{n = 0}^{L - 1} e^{-\j\omega n} \\
&= \frac{1 - e^{-\j L\omega}}{1 - e^{-\j\omega}}
\end{aligned}
$$

这个窗函数的频谱在周期 $[-\pi, \pi]$ 内表现为低频有一个主峰，其余部分有若干指数衰减的副峰。主峰的宽度是绝对值最小两个零点的距离：

$$
\Delta\omega_L = \frac{2\pi}{L}
$$

### 窗宽的限制

现在我们考虑下述数字信号：

$$
x(n) = A_1e^{\j\omega_1 n} + A_2e^{\j\omega_2 n} (0 < \omega_1, \omega_2 < \pi)
$$

显然其 DTFT 为：

$$
X(\omega) = 2\pi(A_1\delta(\omega - \omega_1) + A_2\delta(\omega - \omega_2))
$$

现在我们将其乘上窗函数得到有限长的数字信号：

$$
x_L(n) = x(n)w(n)
$$

其 DTFT 为：

$$
X_L(\omega) = \frac{1}{2\pi}(X \otimes W)(\omega) = A_1W(\omega - \omega_1) + A_2W(\omega - \omega_2)
$$

这里我们只考虑 $W$ 的主峰，显然使用窗函数截取之后，频谱上原先是两个冲激的位置变为了两个窗函数频谱主峰。为了保证这两个主峰可以区分，我们要求这两个主峰不重叠。显然这需要 $|\omega_1 - \omega_2|$ 足够大。根据已知的主峰宽度，我们有：

$$
|\omega_1 - \omega_2| > \frac{2\pi}{L}
$$

也就是说如果数字信号的最小频率间隔为 $\Delta\omega$ ，那么为了保证截取后频谱上各个频率峰可分辨，必须要求窗函数宽度 $L$ 不小于 $2\pi / \Delta\omega$ 。

### DFT 的引入和矩阵形式

现在我们回到求解有限长数字信号傅立叶变换的问题上。我们继续延续上述 DTFT 的思路。不过我们现在只考虑在 $\omega_k = 2k\pi / N$ 处频谱的取值，即频域 $[0, 2\pi]$ 区间内的 $N$ 个特征点：

$$
\begin{aligned}
X_L(\omega_k) &= \frac{1}{2\pi} ({\rm DTFT}[x] \otimes {\rm DTFT}[w])(\omega) \\
&= \frac{1}{2\pi} \int_{-\pi}^{\pi} \left(\sum_{m = -\infty}^{+\infty} x(m)e^{-\j\left(\frac{2k\pi}{N} - \mu\right) m}\right)\left(\sum_{n = 0}^{L - 1} e^{-\j\mu n}\right) \d\mu \\
&= \frac{1}{2\pi} \int_{-\pi}^{\pi} \sum_{n = 0}^{L - 1}\sum_{m = -\infty}^{+\infty} x(m)e^{-\j\frac{2km\pi}{N}}e^{-\j\mu (n - m)} \d\mu \\
&= \frac{1}{2\pi} \sum_{n = 0}^{L - 1}\sum_{m = -\infty}^{+\infty} x(m)e^{-\j\frac{2km\pi}{N}} \int_{-\pi}^{\pi} e^{-\j\mu (n - m)} \d\mu \\
&= \sum_{n = 0}^{L - 1} x(n)e^{-\j\frac{2kn\pi}{N}}
\end{aligned}
$$

这里最后一个等号是利用指数函数在周期上的积分除非系数为零，否则积分结果均为零，所以最后只有 $m = n$ 的项被保留。

便于表示，我们把 $X_L(\omega_k)$ 简记为 $X(k)$ 。

从而我们就可以做到从有限长的数字信号还原出原信号频谱，这个变换就是**离散傅立叶变换（ Discrete Fourier Transformation / DFT ）**。

我们注意到这样的一点，就是这里有两个参数是可以独立确定的，即窗函数长度 $L$ 和频域特征点数 $N$ 。但实际应用中一般都有 $L = N$ ，这主要是因为这样计算方便且 $L \neq N$ 的情况均可以还原成 $L = N$ 的情况。

---

为了论证这一点，我们可以从另外一个角度推导 DFT 公式。也就是回归傅里叶分析的本质，而傅里叶分析的本质就是将函数分解成若干个指数函数的线性组合。之前论述过的 FT 用于分解时域无限的连续信号，而 DFT 则会用于分析时域有限的离散信号。

考虑信号 $f$ ，我们在区间 $[0, 1]$ 上均匀取 $N$ 个样，组成代表这个信号的向量：

$$
\b{x} = \lv
f(0) & f\left(\frac{1}{N}\right) & \cdots & f\left(\frac{N - 1}{N}\right)
\rv
$$

同样的，我们需要对作为基的函数 $e^{2\pi\j kt}$ 也在这些点取样，函数 $e^{2\pi\j kt}$ 取到的样为：

$$
\b{e}_k = \lv
\varepsilon_N^0 & \varepsilon_N^k & \cdots & \varepsilon_N^{(N - 1)k}
\rv
$$

这里 $\varepsilon_N$ 表示 $N$ 次单位根。

根据周期性，显然得到：

$$
\b{e}_k = \b{e}_{k + N}
$$

所以将 $\b{x}$ 拆分为 $\b{e}_k$ 的线性组合的时候只需要考虑一个周期内的 $\b{e}_k$ 即可，也就是说我们假设存在这样的一系列系数 $\b{X} = \lv X(0) & X(1) & \cdots & X(N - 1)\rv$ 满足（这里的系数 $1 / N$ 是为了简化后续的讨论）：

$$
\b{x} = \frac{1}{N}\sum_{k = 0}^{N - 1} X(k)\b{e}_k
$$

如果我们定义这样的一个矩阵：

$$
F_N = \lv \b{e}_0 & \b{e}_1 & \cdots & \b{e}_{N - 1}\rv
$$

上述等式实际上就是 $N\b{x} = F_N\b{x}$。所以要求解系数向量，其实就等价于求解 $F_N$ 的逆矩阵。

考虑这样的数学关系（这里 $\cdot^H$ 表示 Hermit 转置，即共轭转置）：

$$
\b{e}_i^H\b{e}_j = \sum_{k = 0}^{N - 1} (\varepsilon_N^{ik})^*\varepsilon_N^{jk} = \sum_{k = 0}^{N - 1} \varepsilon_N^{(j - i)k} = \begin{cases}
N & i = j \\
\dfrac{1 - \varepsilon_N^{N(j - i)}}{1 - \varepsilon_N^{j - i}} = 0 & i \neq j
\end{cases}
$$

这说明 $\dfrac{F_N}{\sqrt{N}}$ 是酉矩阵。即有：

$$
F_N^{-1} = \frac{1}{\sqrt{N}}\left(\frac{F_N}{\sqrt{N}}\right)^{-1} = \frac{1}{\sqrt{N}}\left(\frac{F_N}{\sqrt{N}}\right)^H = \frac{1}{N} (F_N^T)^* = \frac{1}{N} F_N^*
$$

所以系数向量就可以表示为：

$$
\b{X} = F_N^* \b{x}
$$

对比会发现这个和我们之前推导出来的 DFT 公式是一致的。也就是说我们得到了 $L = N$ 时的 DFT 向量表示。

---

回到先前的问题，我们思考为何 $L \neq N$ 的情况都可以化归为 $L = N$ 。

如果 $L < N$ ，也就是说窗宽（序列长度）不足，我们可以在序列 $x(n)$ 末尾补零至 $N$ 位得到 $x_D(n)$，以此计算 DFT ：

$$
X_D(k) = \sum_{n = 0}^{N - 1} x_D(n)e^{-\j\frac{2\pi nk}{N}} = \sum_{n = 0}^{L - 1} x_D(n)e^{-\j\frac{2\pi nk}{N}} = \sum_{n = 0}^{L - 1} x(n)e^{-\j\frac{2\pi nk}{N}} = X(k)
$$

可见通过补零，补零序列的 DFT 就是原序列的 DFT 。所以完全可以认为 $L < N$ 时应化归为 $L = N$ 。

而在 $L > N$ 的时候，我们需要使用**回绕**。即定义这样的新序列：

$$
\tilde{x}(n) = \sum_{k \mathop{\equiv} n \mathop{\rm mod} N} x(k)
$$

直观而言，就是将原先的过长序列拆为若干长度为 $N$ 的短序列后对齐相加。为了解释此时依然可以化归，我们使用先前得到矩阵形式的 DFT 即可说明。首先我们取 $F_N$ ，这里 $N$ 即 DFT 点数， $F_N$ 定义与之前相同。取新矩阵：

$$
F_{N\times L} = \lv F_N & F_N & \cdots \rv
$$

即使用 $F_N$ 的列按顺序拼接出一个 $N$ 行 $L$ 列的新矩阵，那么：

$$
{\rm DFT}[x](k) = F_{N\times L}\b{x} = (F_N\lv I_N & I_N & \cdots \rv)\b{x} = F_N\tilde{\b{x}} = {\rm DFT}[\tilde{x}](k)
$$

所以回绕序列和原序列具有相同的 DFT 。

基于上述讨论，考虑到 $L$ 一般而言是原信号的长度，是一个不能修改的量。而 DFT 的点数 $N$ 则是计算过程中自由选取的参数，是可以随意更改的。为了方便，一般而言直接选定 $N = L$ 以方便计算。

### DFT 的性质

实际上我们可以发现 DFT 就是对 DTFT 频域一个周期内的取样，即时域从无限变为有限的时候，频域就会变成原先频域一个周期内的取样。这一点对连续信号也是成立的，时域从无限（周期无限）变为有限（有限周期信号）的时候，频域也会变为原先频域一个周期内的取样（周期信号的频域是离散的）。

现在考虑实序列的 DFT ，实际上我们考虑 DTFT 即可。令实序列 $x(n)$ 的 DTFT 为 $X(\omega)$ 。根据 DTFT 定义：

$$
X(\omega) = \sum_{n = -\infty}^{+\infty} x(n)e^{-\j\omega n}
$$

显然：

$$
X(-\omega) = X^*(\omega)
$$

另外一个相当重要的性质是：

$$
X(\omega) = X^*(2\pi - \omega)
$$

这说明了在周期 $[0, 2\pi]$ 上，实序列的 DTFT 和自身共轭对称。这个性质的证明也是简单的：

$$
\begin{aligned}
X(2\pi - \omega) = \sum_{n = -\infty}^{+\infty} x(n)e^{-\j(2\pi - \omega) n} = \sum_{n = -\infty}^{+\infty} x(n)e^{\j\omega n} = X^*(\omega)
\end{aligned}
$$

这个性质反映在 DFT 上就是，实序列的偶数点 DFT 序列自身和自身共轭对称。即长度为 $2N$ 的实序列 $x$ 的 DFT 序列 $X$ 满足：

$$
X(N + k) = X^*(N - k)
$$

---

DFT 的其他性质和 FT 类似。比如 DFT 是线性的，和共轭及反褶的关系也和 FT 一致，其余类似时域平移、频域平移等性质则自行推导。

`TODO`

### FFT 算法

直接计算 $N$ 点 DFT 的时间复杂度为 $O(n^2)$ ，但是考虑到 $F_N$ 矩阵的优良性质，应该具有更快速的算法。有一种思路是使用分治，考虑下述数学事实，这里 $X$ 为 $x$ 的 $N$ 点 DFT 序列，$x$ 长度为 $N$，不妨 $N$ 是偶数：

$$
\begin{aligned}
{\rm DFT}_{n, k}[x(n)](k) &= \sum_{n = 0}^{N - 1} x(n)e^{-\j\frac{2\pi nk}{N}} \\
&= \sum_{r = 0}^{N / 2 - 1} x(2r)e^{-\j\frac{2\pi rk}{N / 2}} + \sum_{r = 0}^{N / 2 - 1} x(2r + 1)e^{-\j\frac{2\pi(r + 1 / 2)k}{N / 2}} \\
&= \sum_{r = 0}^{N / 2 - 1} x(2r)e^{-\j\frac{2\pi rk}{N / 2}} + e^{-\j\frac{2\pi k}{N}}\sum_{r = 0}^{N / 2 - 1} x(2r + 1)e^{-\j\frac{2\pi rk}{N / 2}} \\
&= {\rm DFT}_{n, k}[x(2n)](k) + e^{-\j\frac{2\pi k}{N}}{\rm DFT}_{n, k}[x(2n + 1)](k)
\end{aligned}
$$

也就是说，将原序列按照奇偶拆为两个序列，分别作 DFT 后再合并即得到原序列的 DFT 。按此分治算法即可得到 $O(n\log n)$ 的 FFT 算法。

`TODO: 似乎还有一些和采样相关的东西之后看`

# 傅里叶分析的简单总结

`TODO`

# 系统概念基础

在这门课程内，我们将下述概念称为**系统（ System ）**。对给定的激励给出对应的响应的，具有特定功能的整体称为系统。

本课程主要探讨离散时间的、线性的、时不变的、因果系统。即这类系统仅处理离散时间激励，满足线性，且无论何时接收到相同的激励都会给出相同的响应（时不变性），响应和当前激励之后的激励无关（因果性还可以表述为响应仅和当前激励及先前的激励有关，现实的系统都是因果的）。另外，我们可能还需要附加稳定性要求，即对于有界激励会给出有界响应。

## 系统流图

我们可以使用流图来描述一个系统，流图中常用的单元包括以下。

首先是延时单元，含义为将自变量向过去推迟一个单位。

{% mermaid graph LR %}
S([Source]) -->|"$x(n)$"| U["$Z^{-1}$"]
U -->|"$x(n - 1)$"| T([Terminal])

SR([Source]) -->|"$x(n)$"| UR["$D$"]
UR -->|"$x(n - 1)$"| TR([Terminal])
{% endmermaid %}

这两个符号都是允许的。

其次是求和单元，含义为多个量求和。

{% mermaid graph LR %}
SL([Source 1]) -->|"$x_1(n)$"| U(("$+$"))
SR([Source 2]) -->|"$x_2(n)$"| U
U -->|"$x_1(n) + x_2(n)$"| T([Terminal])
{% endmermaid %}

中间的加号换为 $\Sigma$ 也是允许的。

最后是系数单元，含义为乘以常数。

{% mermaid graph LR %}
S([Source]) -->|"$x(n)$"| U{"$a_0$"}
U -->|"$a_0x(n)$"| T([Terminal])

SR([Source]) -->|"$a_0$"| TR([Terminal])
{% endmermaid %}

这两种表达都是可以的。

---

比如说激励为 $x$ ，响应为 $y$ 的系统如果满足：

$$
y(n) = \frac{1}{b_0}\left[a_0x(n) + a_1x(n - 1) - b_1y(n - 1)\right]
$$

其流图就可以为：

{% mermaid graph LR %}
S(["$x(n)$"]) --> Input-split[" "]
Input-split ---->|"$a_0$"| Sum(("$\Sigma$"))
Input-split --> Input-shift["$Z^{-1}$"]
Input-shift -->|"$a_1$"| Sum
Output-split --> Output-shift["$Z^{-1}$"]
Sum ---->|"$1 / b_0$"| Output-split[" "]
Output-shift -->|"$-b_1$"| Sum
Output-split --> T(["$y(n)$"])
{% endmermaid %}