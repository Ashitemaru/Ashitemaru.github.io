---
title: 《现代控制技术》学习笔记
date: 2022-09-25 17:05:37
mathjax: true
category:
    - 【学习笔记】计算机
---

一门数学课。因为之前退了存储技术基础，所以只能暂且选一门限选课来填充一下空荡荡的大四课程表，不过本来自己也就对控制论有一点点兴趣在的。

$$
\newcommand{\d}{\mathrm{d}}
\newcommand{\lv}{\left[\begin{matrix}}
\newcommand{\rv}{\end{matrix}\right]}
$$

<!-- more -->

# 描述系统的基本概念

## 状态空间

考虑电阻 $R$，电容 $C$，电感 $L$ 串联形成的系统，上述串联系统通过恒压源控制两端电压为 $u$。假设电容的电压为 $u_C$，串联系统中电流为 $i$，显然有下述关系成立：

$$
\begin{aligned}
i &= C\frac{\d u_C}{\d t} \\
u_C &= u - Ri - L\frac{\d i}{\d t} \\
\end{aligned}
$$

据此，我们可以看出，仅依靠 $i$ 并不能确定系统状态，仅依靠 $u_C$ 也不能。但是如果同时确定了 $i, u_C$ 实际上就确定了整个系统的状态。

我们将一组能够完全表征系统状态的最少独立变量称为系统的**状态变量**，以这些状态变量为分量的向量称为**状态向量**，将状态向量所在的 $n$ 维向量空间 $\mathbb{R}^n$ 称为**状态空间**，这里 $n$ 为状态向量的维数。

而如果我们将系统的输入记作 $u(t)$，输出记作 $y(t)$。描述系统的方程可以分为两个，其一是**状态方程**，一般是描述系统状态的微分与系统状态、系统输入、时间之间的关系的微分方程：

$$
\dot x(t) = f(x(t), u(t), t)
$$

其二是**输出方程**，一般是描述系统输出与系统状态、系统输入、时间之间的关系的方程：

$$
y(t) = g(x(t), u(t), t)
$$

而上述两个方程合称**状态空间表达式**。

在最开始的例子中，我们将 $u$ 视为输入，那么上述例子中两个方程实际上可以整理为计算 $i, u_C$ 的一阶微分的表达式，从而构成了系统的状态方程。而由于我们没有具体指出何种物理量为输出，所以无法给定输出方程。

此外，如果状态空间表达式可以使用矩阵的形式改写为：

$$
\begin{aligned}
\dot x(t) &= Ax(t) + Bu(t) \\
y(t) &= Cx(t) + Du(t) \\
\end{aligned}
$$

那么这个系统就是**线性系统**。这里矩阵 $A, B, C, D$ 分别称为系统矩阵、控制矩阵、输出矩阵、直接传递矩阵。

## 建立状态空间表达

### 简单系统的状态空间

对于简单系统，我们完全可以简单列出系统中的约束，确定状态变量后改写出状态空间表达式。例如，我们将电阻 $R$，电感 $L$，电机 $M$ 串联于恒压源 $u$ 上，恒压源电压作为该系统的输入。电机驱动转动惯量为 $J$ 的物体。电机阻尼系数为 $f$，反电势系数为 $C_e$，电磁转矩系数为 $C_m$。

记串联系统中电流为 $i$，电机转动角度为 $\theta$。那么根据串联分压首先得到约束：

$$
u = iR + L\frac{\d i}{\d t} + C_e\frac{\d \theta}{\d t}
$$

考虑电机的约束，能够得到：

$$
C_mi = J\frac{\d^2 \theta}{\d t^2} + f\frac{\d \theta}{\d t}
$$

考虑将状态变量设定为 $x_1 := \theta, x_2 := \dot\theta, x_3 := i$，定义系统的输出为 $y := \theta$。整理上述式子得到状态方程：

$$
\begin{aligned}
\dot x_1 &= x_2 \\
\dot x_2 &= \frac{1}{J}(-fx_2 + C_mx_3) \\
\dot x_3 &= \frac{1}{L}(-C_ex_2 - Rx_3 + u) \\
\end{aligned}
$$

输出方程则为：

$$
y = x_1
$$

上述方程可以改写为矩阵形式：

$$
\lv \dot x_1 \\ \dot x_2 \\ \dot x_3 \rv =
\lv
0 & 1 & 0 \\
0 & -f / J & C_m / J \\
0 & -C_e / L & -R / L \\
\rv
\lv x_1 \\ x_2 \\ x_3 \rv +
\lv 0 \\ 0 \\ 1 / L \rv u
$$

以及：

$$
y = \lv 1 & 0 & 0 \rv \lv x_1 \\ x_2 \\ x_3 \rv
$$

### 高阶微分方程描述的系统的状态空间

对于较为复杂的线性系统，我们可以考虑这样的一个通用表达。即如果，系统的输入输出满足下述的高阶微分方程：

$$
\sum_{k = 0}^n a_ky^{(n - k)} = \sum_{k = 0}^n b_ku^{(n - k)}
$$

这里 $a_0 = 1$。

我们现在考虑一个退化情况，即所有的 $k < n$ 都有 $b_k = 0$。此时上述微分方程退化为：

$$
\sum_{k = 0}^n a_ky^{(n - k)} = bu
$$

此时不妨令状态变量为 $x_1 := y, x_2 := \dot y, x_3 := \ddot y, \cdots, x_n := y^{(n - 1)}$，显然输出为 $y = x_1$。那么显然我们可以注意到状态方程为：

$$
\lv \dot x_1 \\ \dot x_2 \\ \dot x_3 \\ \vdots \\ \dot x_n \rv =
\lv
0 & 1 & 0 & \cdots & 0 \\
0 & 0 & 1 & \cdots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \cdots & 1 \\
-a_n & -a_{n - 1} & -a_{n - 2} & \cdots & -a_1 \\
\rv
\lv x_1 \\ x_2 \\ x_3 \\ \vdots \\ x_n \rv +
\lv 0 \\ 0 \\ \vdots \\ 0 \\ b \rv u
$$

输出方程是平凡的，这里不再具体写出其矩阵形式。

上述例子中定义的状态变量所引出的状态空间表达式，一般称为**能控标准型**。

此外，我们还能按照下述方式定义状态变量：

$$
\begin{cases}
x_1 := y^{(n - 1)} + a_1y^{(n - 2)} + \cdots + a_{n - 3}\ddot y + a_{n - 2}\dot y + a_{n - 1}y \\
x_2 := y^{(n - 2)} + a_1y^{(n - 3)} + \cdots + a_{n - 3}\dot y + a_{n - 2}y \\
x_3 := y^{(n - 3)} + a_1y^{(n - 4)} + \cdots + a_{n - 3}y \\
\vdots \\
x_{n - 1} := \dot y + a_1y \\
x_n := y \\
\end{cases}
$$

显然得到：

$$
\begin{cases}
\dot x_1 = -a_nx_n + bu \\
\dot x_2 = x_1 - a_{n - 1}x_n \\
\dot x_3 = x_2 - a_{n - 2}x_n \\
\vdots \\
\dot x_{n - 1} = x_{n - 2} - a_2x_n \\
\dot x_n = x_{n - 1} - a_1x_n \\
\end{cases}
$$

从而就能得到在这样的状态变量定义之下的状态方程：

$$
\lv \dot x_1 \\ \dot x_2 \\ \dot x_3 \\ \vdots \\ \dot x_n \rv =
\lv
0 & 0 & \cdots & 0 & -a_n \\
1 & 0 & \cdots & 0 & -a_{n - 1} \\
0 & 1 & \cdots & 0 & -a_{n - 2} \\
\vdots & \vdots & \ddots & \vdots & \vdots \\
0 & 0 & \cdots & 1 & -a_1 \\
\rv
\lv x_1 \\ x_2 \\ x_3 \\ \vdots \\ x_n \rv +
\lv b \\ 0 \\ 0 \\ \vdots \\ 0 \rv u
$$

输出方程是平凡的，这里不再具体写出其矩阵形式。

上述例子中定义的状态变量所引出的状态空间表达式，一般称为**能观标准型**。

---

现在回到一般情况。为了更好地设定状态变量，我们首先定义下述中间变量：

$$
\begin{cases}
\beta_0 := b_0 \\
\beta_1 := b_1 - a_1\beta_0 \\
\beta_2 := b_2 - a_1\beta_1 - a_2\beta_0 \\
\vdots \\
\beta_n := b_n - a_1\beta_{n - 1} - \cdots - a_{n - 1}\beta_1 - \beta_0 \\
\end{cases}
$$

从而定义出下述状态变量：

$$
\begin{cases}
x_1 := y - \beta_0 u \\
x_2 := \dot y - \beta_0 \dot u - \beta_1 u \\
x_3 := \ddot y - \beta_0 \ddot u - \beta_1 \dot u - \beta_2 u \\
\vdots \\
x_n := y^{(n - 1)} - \beta_0 u^{(n - 1)} - \beta_1 u^{(n - 2)} - \beta_2 u^{(n - 3)} - \cdots - \beta_{n - 1} u \\
\end{cases}
$$

从而就能得到在这样的状态变量定义之下的状态方程：

$$
\lv \dot x_1 \\ \dot x_2 \\ \dot x_3 \\ \vdots \\ \dot x_n \rv =
\lv
0 & 1 & 0 & \cdots & 0 \\
0 & 0 & 1 & \cdots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \cdots & 1 \\
-a_n & -a_{n - 1} & -a_{n - 2} & \cdots & -a_1 \\
\rv
\lv x_1 \\ x_2 \\ x_3 \\ \vdots \\ x_n \rv +
\lv \beta_1 \\ \beta_2 \\ \beta_3 \\ \vdots \\ \beta_n \rv u
$$

输出方程显然为 $y = x_1 + \beta_0 u$，这里不再具体写出其矩阵形式。

### 传递函数

在讲述传递函数之前，我们简单介绍系统的零状态响应和零输入响应。零状态响应指的是一个系统在系统所有状态变量为零时，对给定输入所给出的响应。相应的，零输入响应则是一个系统在所有输入为零的时候所给出的响应。而所谓的全响应，则是在状态变量不一定为零，输入也不一定为零的条件下系统的响应。事实上，全响应是零状态响应和零输入相应的组合。

而考虑用线性常微分方程描述的系统，线性常微分方程的解均是通解和特解的组合。这里零状态响应实际上对应了特解，因为零状态事实上仅改变了系统状态的起始值，将其强制设定为零，所得到的响应是零初始值下微分方程的一个特殊解。

实际上，零状态响应在很多条件下给出了系统的完全信息。大部分系统在零输入的条件下，零输入响应最终会衰减至可忽略，而最后表现系统结构的是零状态响应。基于这样的理解，很多时候我们仅需要了解零状态响应即可基本分析系统的结构。

---

分析零状态响应的一个重要方法为分析零状态响应下的系统传递函数。在零状态条件下，我们能够建立输入 $u$ 到输出 $y$ 的映射，而如果将该系统的单位冲激响应记为 $g$，我们显然得知 $y$ 为 $u$ 与 $g$ 的卷积：

$$
y = u * g
$$

对上述两边做 Laplace 变换：

$$
Y = GU
$$

而这里我们就将 $G(s)$ 称为系统的（零状态）**传递函数**，其定义即零状态下输出的 Laplace 变换与输入的 Laplace 变换的商。我们可以证明，系统的零状态传递函数为零状态单位冲激响应的 Laplace 变换。

---

现在我们考虑状态空间方程式和传递函数之间的互相转换。

【从传递函数推导状态空间方程式见 PPT #1，未整理】

我们已知一个线性系统的状态空间方程式如下：

$$
\begin{cases}
\dot x = Ax + Bu \\
y = Cx + Du \\
\end{cases}
$$

对状态方程两边作 Laplace 变换得到：

$$
sX(s) = AX(s) + BU(s)
$$

这里 $X := \mathcal{L}[x], U := \mathcal{L}[u]$，其余函数的 Laplace 变换符号以此类推。

那么我们就能够得到：

$$
X(s) = (sI - A)^{-1}BU(s)
$$

从而得到：

$$
Y(s) = CX(s) + DU(s) = [C(sI - A)^{-1}B + D]U(s)
$$

这显然给出了传递函数的表达：

$$
G(s) = \frac{Y(s)}{U(s)} = C(sI - A)^{-1}B + D
$$

基于这一结论我们可以分析串联和并联系统的传递函数。

现在假设有两个系统，下述为其状态空间方程式：

$$
\Sigma_1: \begin{cases}
\dot x_1 = A_1x_1 + B_1u_1 \\
y_1 = C_1x_1 + D_1u_1 \\
\end{cases}, \Sigma_2: \begin{cases}
\dot x_2 = A_2x_2 + B_2u_2 \\
y_2 = C_2x_2 + D_2u_2 \\
\end{cases}
$$

如果两个系统串联，那么新系统的输入 $u := u_1$，新系统的输出 $y := y_2$，并且我们有 $u_2 = y_1$。而这个系统的状态变量就视作原先两个系统的状态变量的拼接。从而我们得到：

$$
\dot x_2 = A_2x_2 + B_2u_2 = A_2x_2 + B_2(C_1x_1 + D_1u_1) = A_2x_2 + B_2C_1x_2 + B_2D_1u_1
$$

从而得到串联系统的状态方程：

$$
\lv \dot x_1 \\ \dot x_2 \rv =
\lv
A_1 & 0 \\
B_2C_1 & A_2
\rv
\lv x_1 \\ x_2 \rv +
\lv B_1 \\ B_2D_1 \rv u
$$

输出则有：

$$
y = y_2 = C_2x_2 + D_2u_2 = C_2x_2 + D_2(C_1x_1 + D_1u_1) = C_2x_2 + D_2C_1x_1 + D_2D_1u_1
$$

从而输出输出方程为：

$$
y = \lv D_2C_1 & C_2 \rv \lv x_1 \\ x_2 \rv + D_2D_1u
$$

根据先前推出的传递函数和状态空间方程式之间的关系，我们可以写出串联系统的传递函数为：

$$
\begin{aligned}
G(s) &= \lv D_2C_1 & C_2 \rv
\lv
sI_1 - A_1 & 0 \\
-B_2C_1 & sI_2 - A_2 \\
\rv^{-1}
\lv B_1 \\ B_2D_1 \rv + D_2D_1 \\
&= \lv D_2C_1 & C_2 \rv
\lv
(sI_1 - A_1)^{-1} & 0 \\
(sI_2 - A_2)^{-1}B_2C_1(sI_1 - A_1)^{-1} & (sI_2 - A_2)^{-1} \\
\rv
\lv B_1 \\ B_2D_1 \rv + D_2D_1 \\
&= \lv D_2C_1 & C_2 \rv
\lv
(sI_1 - A_1)^{-1}B_1 \\
(sI_2 - A_2)^{-1}B_2C_1(sI_1 - A_1)^{-1}B_1 + (sI_2 - A_2)^{-1}B_2D_1 \\
\rv + D_2D_1 \\
&= D_2C_1(sI_1 - A_1)^{-1}B_1 + C_2(sI_2 - A_2)^{-1}B_2C_1(sI_1 - A_1)^{-1}B_1 + C_2(sI_2 - A_2)^{-1}B_2D_1 + D_2D_1 \\
&= [C_1(sI_1 - A_1)^{-1}B_1 + D_1][C_2(sI_2 - A_2)^{-1}B_2 + D_2] \\
&= G_1(s)G_2(s)
\end{aligned}
$$

也就是说串联系统的传递函数为各个子系统的传递函数之积。

对于并联系统，显然有 $u_1 = u_2 = u$，而输出有 $y = y_1 + y_2$。使用上述方法很容易推出 $G(s) = G_1(s) + G_2(s)$。

也就是说并联系统的传递函数为各个子系统的传递函数之和。

## 状态空间的线性变换

对于下述的线性系统的状态空间表达式：

$$
\begin{cases}
\dot x = Ax + Bu \\
y = Cx + Du \\
\end{cases}
$$

取一个可逆矩阵 $P$，定义变换 $x = P\overline x$，得到下述的状态空间表达式：

$$
\begin{cases}
P\dot{\overline x} = AP\overline x + Bu \\
y = CP\overline x + Du \\
\end{cases}
\iff
\begin{cases}
\dot{\overline x} = P^{-1}AP\overline x + P^{-1}Bu \\
y = CP\overline x + Du \\
\end{cases}
$$

不妨定义：

$$
\overline A = P^{-1}AP, \overline B = P^{-1}B, \overline C = CP, \overline D = D
$$

这样我们就得到了线性状态空间上的线性变换。而这样的线性变换事实上能够保证变换前后的传递函数不变：

$$
\begin{aligned}
\overline G(s) &= \overline C(sI - \overline A)^{-1}\overline B + \overline D \\
&= CP(sI - P^{-1}AP)^{-1}P^{-1}B + D \\
&= C[(P^{-1})^{-1}(sI - P^{-1}AP)^{-1}P^{-1}]B + D \\
&= C[P(sI - P^{-1}AP)P^{-1}]^{-1}B + D \\
&= C(sI - A)^{-1}B + D \\
&= G(s)
\end{aligned}
$$

该性质保证了线性系统经过可逆线性变换后系统本质性质不变。而我们可以使用合适的可逆线性变换令 $A$ 对角化或者变为 Jordan 标准型。如果 $A$ 能够对角化为 $\overline A$，事实上就实现了状态变量的变量分离，因为根据 $\dot{\overline x} = \overline A\overline x + \overline Bu$ 可以看出此时某个状态变量的导数仅和其本身有关而和其余状态变量无关。

对于能够对角化的 $A \in M_{n \times n}(\mathbb{R})$，显然其具有 $n$ 个线性无关的特征向量 $p_1, p_2, \cdots, p_n$。这里不妨假设其对应的特征值分别为 $\lambda_1, \lambda_2, \cdots, \lambda_n$。那么我们只需要令 $P := (p_1, p_2, \cdots, p_n)$ 即可，此时：

$$
\overline A = \lv
\lambda_1 \\
& \lambda_2 \\
&& \ddots \\
&&& \lambda_n \\
\rv
$$

如果 $A$ 不能对角化，其总能找到与其相似的 Jordan 标准型 $\overline A \sim A$。这里给出求解 Jordan 标准型的步骤。

- 首先求解 $A$ 的特征值 $\lambda_1, \lambda_2, \cdots, \lambda_n$
- 对于其中几何重数小于代数重数的特征值（即特征向量线性相关的特征值）$\lambda_i$，求取其对应的 Jordan 链
- 如果 $v_i$ 是特征值 $\lambda_i$ 的特征向量，则不断求取 $(\lambda_i A - I)v_j = -v_{j - 1}, j \geq i + 1$ 得到 Jordan 链
- 令 $P := (v_1, v_2, \cdots, v_n)$，从而求取完毕

这里举出一例，计算其 Jordan 标准型：

$$
A = \lv
1 & -1 & -1 \\
0 & 1 & 0 \\
0 & 0 & 2 \\
\rv
$$

不难求出其特征值为 $\lambda_1 = \lambda_2 = 1, \lambda_3 = 2$。然而属于特征值 $1$ 的特征向量张成的空间维数为 $1$，该空间为 ${\rm Span}\left(\lv 1 \\ 0 \\ 0 \rv\right)$。

那么令 $v_1 = \lv 1 \\ 0 \\ 0 \rv$，求解 $(A - I)v_2 = -v_1$ 得到 $v_2 = \lv 0 \\ -1 \\ 0 \rv$。

特征值 $\lambda_3 = 2$，对应的特征向量为 $v_3 = \lv -1 \\ 0 \\ 1 \rv$。从而：

$$
P = \lv
1 & 0 & -1 \\
0 & -1 & 0 \\
0 & 0 & 1 \\
\rv,
\overline A = \lv
1 & 1 & 0 \\
0 & 1 & 0 \\
0 & 0 & 2 \\
\rv
$$

## 离散时间系统

在离散时间系统中，状态向量的导数可以使用差分表示，即 $\dot x(k) := x(k + 1) - x(k)$，那么状态空间表达式可以为：

$$
\begin{cases}
\dot x(k) = x(k + 1) - x(k) = Ax(k) + Bu(k) \\
y(k) = Cx(k) + Du(k) \\
\end{cases}
$$

令 $G := A + I, H := B$，离散时间系统的状态空间表达式即为：

$$
\begin{cases}
x(k + 1) = Gx(k) + Hu(k) \\
y(k) = Cx(k) + Du(k) \\
\end{cases}
$$

对于一般的离散时间系统，其系统性质是由下述差分方程描述的：

$$
\sum_{i = 0}^n a_iy(k + n - i) = \sum_{j = 1}^n b_ju(k + n - j), a_0 = 1
$$

其对应下述传递函数：

$$
\frac{Y(z)}{U(z)} = \frac{b_1z^{n - 1} + \cdots + b_{n - 1}z + b_n}{z^n + a_1z^{n - 1} + \cdots + a_{n - 1}z + a_n}
$$

不妨令：

$$
W(z) := \frac{U(z)}{z^n + a_1z^{n - 1} + \cdots + a_{n - 1}z + a_n}
$$

那么：

$$
\begin{aligned}
U(z) &= (z^n + a_1z^{n - 1} + \cdots + a_{n - 1}z + a_n)W(z) \\
Y(z) &= (b_1z^{n - 1} + \cdots + b_{n - 1}z + b_n)W(z) \\
\end{aligned}
$$

对两边作逆 Laplace 变换得到：

$$
\begin{aligned}
u(k) &= w(k + n) + a_1w(k + n - 1) + \cdots + a_{n - 1}w(k + 1) + a_nw(k) \\
y(k) &= b_1w(k + n - 1) + \cdots + b_nw(k) \\
\end{aligned}
$$

从而可以定义状态向量：

$$
x(k) := \lv
w(k) \\ w(k + 1) \\ \vdots \\ w(k + n - 1)
\rv
$$

那么显然有该定义下的状态空间表达式：

$$
\begin{aligned}
x(k + 1) &= \lv
& 1 \\
&& 1 \\
&&& \ddots \\
&&&& 1 \\
-a_n & -a_{n - 1} & -a_{n - 2} & \cdots & -a_1
\rv x(k) + \lv
0 \\ 0 \\ \vdots \\ 0 \\ 1
\rv u(k) \\
y(k) &= \lv b_n & b_{n - 1} & \cdots & b_1 \rv x(t) \\
\end{aligned}
$$

# 系统的运动与其离散化

## 线性系统的零输入响应

我们现在考虑线性系统 $\dot x(t) = Ax(t) + Bu(t)$。我们先前提到过系统的全响应是零状态响应和零输入响应的组合，现在我们来分析线性系统的零输入响应。

在零输入条件下，线性系统事实上退化为 $\dot x(t) = Ax(t)$。假定系统初始状态为 $x(0) =: x_0$，对状态空间表达式两边取 Laplace 变换得到：

$$
sX(s) - x_0 = AX(s) \iff X(s) = (sI - A)^{-1}x_0 \iff x(t) = \mathcal{L}^{-1}[(sI - A)^{-1}]x_0
$$

这里尝试使用级数展开的方式处理上述逆 Laplace 变换：

$$
\begin{aligned}
\mathcal{L}^{-1}[(sI - A)^{-1}] &= \mathcal{L}^{-1}\left[\sum_{i = 0}^{+\infty} \frac{A^i}{s^{i + 1}}\right] = \sum_{i = 0}^{+\infty} \mathcal{L}^{-1}\left[\frac{A^i}{s^{i + 1}}\right] = \sum_{i = 0}^{+\infty} \frac{A^it^i}{i!} = e^{At}
\end{aligned}
$$

从而我们得到了这个系统的解：

$$
x(t) = e^{At}x_0
$$

需要注意的是，我们这里默认了 $x(t)$ 仅在 $t \geq 0$ 时才具有意义，此时才可以直接运用下述定理：

$$
\mathcal{L}[t^nu(t)] = \frac{n!}{s^{n + 1}}
$$

这里 $u(t)$ 为单位阶跃函数。

如果我们得知的并非是系统初始态的状态向量，而是时刻 $t_0$ 时的状态向量 $x(t_0)$，通过时域平移可以得到系统的解为：

$$
x(t) = e^{A(t - t_0)}x(t_0), t \geq t_0
$$

总而言之，这个结果说明了零输入条件下，$e^{At}$ 完全决定了系统状态在后续时间中的推演。
