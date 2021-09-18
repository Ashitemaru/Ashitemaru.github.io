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
$$

<!-- more -->

# 信号处理引言

信号是一种载有信息的物理量，所以可以说信号是信息的表现形式，信息是信号蕴含的内容。我们探测信号的工具称为**传感器（ Sensor ）**，传感器是将一种物理变化转化为另一种物理变化的装置。比如说为了探测声波，我们可以使用传感器将声波转化为电信号。

通过传感器我们可以将某些难以处理的信号转化为我们易于处理的信号。对信号的处理大致可以分为三类，即**降噪**、**特征抽取**以及**编码解码**。

信号常常使用函数或者序列进行描述，而函数具有图像，所以有的时候信号也会使用具体的图像进行描述。

---

信号可以按照对于给定的自变量能不能得到确定的因变量分类为**确定信号**和**随机信号**。另外，信号 $f(t)$ 也可以按照是否存在 $T > 0$ 满足 $\forall t \in \mathbb R, f(t) = f(t + T)$ 分为**周期信号**和**非周期信号**。

## 常见信号形式

**正余弦信号**是最常见的信号形式之一，其数学表达式为：

$$
\begin{aligned}
& f(t) = K \sin(\omega t + \theta) \
& f(t) = K \cos(\omega t + \theta) \
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
\frac{\d F}{\d t} &= \int_{0}^{+\infty} e^{-tx} \d \cos x = \left. e^{-tx}\cos x \right|_{0}^{+\infty} + t \int_{0}^{+\infty} e^{-tx}\cos x \d x \
&= -1 + t \int_{0}^{+\infty} e^{-tx} \d \sin x = -1 + t \left( \left. e^{-tx}\sin x \right|_{0}^{+\infty} \right + t \int_{0}^{+\infty} e^{-tx}\sin x \d x \right) \
&= -1 + t^2 \int_{0}^{+\infty} e^{-tx}\sin x \d x
\end{aligned}
$$

---

此外，**指数信号**也较为常见，其数学表达式为：

$$
f(t) = K e^{\alpha t}
$$

指数信号的特征就是，其积分或微分后依然是指数信号。

## Euler 公式