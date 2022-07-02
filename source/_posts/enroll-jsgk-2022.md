---
title: 2022 年江苏高考数学（新高考全国 I）试卷
date: 2022-06-07 17:42:50
mathjax: true
category:
    - 【杂谈】数学
---

听很多人说这份卷子十分难，于是打算简单做一下吧。

<!-- more -->

# 单项选择题

单项选择一共 8 条，一条 5 分，一共是 40 分。错一条的代价实在是太大了，所以还是小心为好。

我给出的答案为 (D)(D)(B)(C)(D)(A)(C)(C)。

## Problem 1

如果集合 $M = \{x: \sqrt x < 4\}, N = \{x: 3x \geq 1\}$，求集合 $M \cap N$。

(A) $\{x: 0\leq x < 2\}$
(B) $\left\{x: \dfrac13 \leq x < 2\right\}$
(C) $\{x: 3 \leq x < 16\}$
(D) $\left\{x: \dfrac13 \leq x < 16\right\}$

---

显然 $M = (0, 16), N = \left[\dfrac13, +\infty\right)$，答案为 (D)。

## Problem 2

如果 ${\rm i}(1 - z) = 1$，求 $z + \overline z$ 的值。

(A) $-2$
(B) $-1$
(C) $1$
(D) $2$

---

假设 $z := a + b{\rm i}(a, b \in \mathbb R)$，那么：

$$
1 = {\rm i}(1 - z) = {\rm i}((1 - a) - b{\rm i}) = b + (1 - a){\rm i}
$$

得到 $z = 1 + {\rm i}$，所以 $z + \overline z = 2\Re(z) = 2$，答案为 (D)。

## Problem 3

在 $\triangle ABC$ 中，点 $D$ 在边 $AB$ 上，$BD = 2DA$，记 $\vec{CA} = \boldsymbol{m}, \vec{CD} = \boldsymbol{n}$，那么：

(A) $\vec{CB} = 3\boldsymbol{m} - 2\boldsymbol{n}$
(B) $\vec{CB} = -2\boldsymbol{m} + 3\boldsymbol{n}$
(C) $\vec{CB} = 3\boldsymbol{m} + 2\boldsymbol{n}$
(D) $\vec{CB} = 2\boldsymbol{m} + 3\boldsymbol{n}$

---

显然有 $\boldsymbol{n} = \vec{CD} = \vec{CB} + \vec{BD}$，也就是说 $\vec{BD} = \boldsymbol{n} - \vec{CB}$。

同理我们还能得到 $\vec{AD} = \vec{CD} - \vec{CA} = \boldsymbol{n} - \boldsymbol{m}$。

根据 $2\vec{AD} + \vec{BD} = \boldsymbol{0}$ 得到：

$$
\boldsymbol{n} - \vec{CB} + 2(\boldsymbol{n} - \boldsymbol{m}) = \boldsymbol{0}
$$

也就是：

$$
\vec{CB} = -2\boldsymbol{m} + 3\boldsymbol{n}
$$

答案为 (B)。

## Problem 4

一道应用题。大意为，一个棱台水库，水位为海拔 $148.5$ 米的时候，水面面积为 $140.0$ 平方千米。水位为海拔 $157.5$ 米的时候，水面面积为 $180.0$ 平方千米。求解水库水位从 $148.5$ 米上升到 $157.5$ 米时增加的水量。

(A) $1.0 \times 10^9$ 立方米
(B) $1.2 \times 10^9$ 立方米
(C) $1.4 \times 10^9$ 立方米
(D) $1.6 \times 10^9$ 立方米

---

简单的棱台体积计算题，注意单位换算：

$$
\begin{aligned}
V &= \frac13 h(S_1 + S_2 + \sqrt{S_1S_2}) \\
&= \frac13 \times (157.5 - 148.5) \times(140 + 180 + \sqrt{140 \times 180}) \times 10^6 \\
&= 6(3\sqrt 7 + 16) \times 10^7 \\
&\approx 1.4 \times 10^9 ({\rm m}^3)
\end{aligned}
$$

答案为 (C)。

## Problem 5

从 $2$ 到 $8$ 总共 $7$ 个整数中随机取两个不同的数，求两数互质的概率。

(A) $1/6$
(B) $1/3$
(C) $1/2$
(D) $2/3$

---

互质的整数对包括：

$$
\begin{aligned}
&(2, 3), (2, 5), (2, 7) \\
&(3, 4), (3, 5), (3, 7), (3, 8) \\
&(4, 5), (4, 7) \\
&(5, 6), (5, 7), (5, 8) \\
&(6, 7) \\
&(7, 8) \\
\end{aligned}
$$

所以概率为：

$$
p = \frac{14}{C_7^6} = \frac23
$$

答案为 (D)。

## Problem 6

函数 $f(x) = \sin\left(\omega x + \dfrac\pi 4\right) + b(\omega > 0)$ 的最小正周期为 $T$，如果 $\dfrac{2\pi}{3} < T < \pi$，而且曲线 $y = f(x)$ 关于点 $\left(\dfrac{3\pi}{2}, 2\right)$ 对称，求 $f\left(\dfrac\pi 2\right)$ 的值。

(A) $1$
(B) $3/2$
(C) $5/2$
(D) $3$

---

根据曲线 $y = f(x)$ 关于点 $\left(\dfrac{3\pi}{2}, 2\right)$ 对称，能得到 $f\left(\dfrac{3\pi}{2}\right) = 2$，以及 $b = 0$，也就是说：

$$
\begin{aligned}
&\sin\left(\omega \dfrac{3\pi}{2} + \dfrac\pi 4\right) = 0 \\
\Rightarrow &\omega \dfrac{3\pi}{2} + \dfrac\pi 4 = k\pi \\
\Rightarrow &\omega = \frac23\left(k - \frac14\right), k \in \mathbb Z
\end{aligned}
$$

根据最小正周期的范围，得到：

$$
T = \frac{2\pi}{\omega} \in \left(\dfrac{2\pi}{3}, \pi\right) \Rightarrow \omega \in (2, 3)
$$

进而得知 $k = 4$，也就是说 $\omega = \dfrac52$，那么：

$$
f\left(\dfrac\pi 2\right) = \sin\left(\dfrac52 \cdot \dfrac{\pi}{2} + \dfrac\pi 4\right) + 2 = 1
$$

答案为 (A)。

## Problem 7

已知 $a = 0.1e^{0.1}, b = \dfrac19, c = -\ln 0.9$，则：

(A) $a < b < c$
(B) $c < b < a$
(C) $c < a < b$
(D) $a < c < b$

---

考虑函数 $f(x) = xe^x + \ln(1 - x), x \geq 0$，有：

$$
f'(x) = (x + 1)e^x - \frac{1}{1 - x} > (x + 1)^2 - \frac{1}{1 - x} = -\frac{x(x^2 + x - 1)}{1 - x}
$$

导函数保证了函数至少在区间 $\left(0, \dfrac{-1 + \sqrt5}{2}\right)$ 上递增，而 $\dfrac1{10}$ 属于这个区间，所以：

$$
f(0.1) = 0.1e^{0.1} - \ln\frac{10}{9} > f(0) = 0
$$

所以 $a > c$。

再考虑函数 $g(x) = \ln(1 + x) - \dfrac{x}{1 + x}, x \geq 0$，有：

$$
g'(x) = \frac{1}{1 + x} - \frac{1}{(1 + x)^2} = \frac{x}{(1 + x)^2} > 0
$$

从而：

$$
g\left(\frac19\right) = \ln\frac{10}{9} - \frac{1}{10} > 0
$$

这说明：

$$
\ln\frac{10}{9} > \frac{1}{10} \Rightarrow \frac{10}{9} > e^{0.1} \Rightarrow \frac{1}{9} > 0.1e^{0.1}
$$

也就是 $b > a$，从而答案为 (C)。

## Problem 8

已知正四棱锥的侧棱长为 $l$，其各顶点都在同一个球面上，若该球体积为 $36\pi$，且 $3\leq l\leq 3\sqrt 3$，求该正四棱锥体积的取值范围。

(A) $\left[18, \dfrac{81}{4}\right]$
(B) $\left[\dfrac{27}{4}, \dfrac{81}{4}\right]$
(C) $\left[\dfrac{27}{4}, \dfrac{64}{3}\right]$
(D) $\left[18, 27\right]$

---

假设球的半径为 $R$，显然有 $V = \dfrac43\pi R^3 = 36\pi$，得到 $R = 3$。

假设正四棱锥 $O-ABCD$ 底面中心为 $P$，其外接球球心为 $Q$。显然我们得到一个直角三角形 $OPA$，而球心 $Q$ 在直线 $PO$ 上。令有向线段 $QO$ 长度为 $3$，记有向线段 $QP$ 长度为 $x \in (-3, 3)$。显然：

$$
\begin{aligned}
l^2 &= OA^2 = PO^2 + PA^2 \\
&= (QO - QP)^2 + (QA^2 - QP^2) \\
&= (3 - x)^2 + (9 - x^2) \\
&= 18 - 6x \in [9, 27]
\end{aligned}
$$

也就得到了 $x \in \left[-\dfrac32, \dfrac32\right]$。从而：

$$
V_{O-ABCD} = \frac13 |PO| \cdot 2PA^2 = \frac23(3 - x)(9 - x^2)
$$

不难得到函数 $f(x) = \dfrac23(3 - x)(9 - x^2)$ 在区间 $\left[-\dfrac32, -1\right]$ 上递增，在区间 $\left[-1, \dfrac32\right]$ 上递减，计算得到：

$$
f\left(-\dfrac32\right) = \frac{81}{4}, f\left(\dfrac32\right) = \frac{27}{4}, f(-1) = \frac{64}{3}
$$

体积的取值范围应当为 $\left[\dfrac{27}{4}, \dfrac{64}{3}\right]$，答案为 (C)。

# 不定项选择题

不定项选择一共 4 条，一条 5 分，一共是 20 分。

我给出的答案为 (ABD)(AC)(BCD)(BC)。

## Problem 9

对于正方体 $ABCD-A_1B_1C_1D_1$，下列说法中正确的有：

(A) 直线 $BC_1$ 与 $DA_1$ 所成的角为直角
(B) 直线 $BC_1$ 与 $CA_1$ 所成的角为直角
(C) 直线 $BC_1$ 与平面 $BB_1D_1D$ 所成的角为 $45^\circ$
(D) 直线 $BC_1$ 与平面 $ABCD$ 所成的角为 $45^\circ$

---

根据直线 $BC_1$ 垂直于平面 $A_1B_1CD$ 就能得到 (A)(B) 均正确。

对于 (C)，显然有直线 $A_1C_1$ 垂直于平面 $BB_1D_1D$，而 $\angle A_1C_1B = 60^\circ$，该选项叙述错误。

对于 (D)，显然有直线 $CC_1$ 垂直于平面 $ABCD$，而 $\angle CC_1B = 45^\circ$，该选项叙述正确。

所以答案为 (ABD)。

## Problem 10

已知函数 $f(x) = x^3 - x + 1$，下列说法中正确的有：

(A) $f(x)$ 有两个极值点
(B) $f(x)$ 有三个零点
(C) 点 $(0, 1)$ 为曲线 $y = f(x)$ 的对称中心
(D) 直线 $y = 2x$ 为曲线 $y = f(x)$ 的切线

---

首先：

$$
f'(x) = 3x^2 - 1
$$

得到 $f(x)$ 在 $\left(-\infty, -\dfrac{\sqrt 3}{3}\right)$ 上递增，在 $\left(-\dfrac{\sqrt 3}{3}, \dfrac{\sqrt 3}{3}\right)$ 上递减，在 $\left(\dfrac{\sqrt 3}{3}, +\infty\right)$ 上递增。

并且计算出：

$$
\begin{aligned}
f\left(-\frac{\sqrt3}{3}\right) &= \frac{2\sqrt 3}{9} + 1 > 0 \\
f\left(\frac{\sqrt3}{3}\right) &= -\frac{2\sqrt 3}{9} + 1 > 0 \\
\end{aligned}
$$

所以 $f(x)$ 有两个极值点，一个零点，选项 (A) 正确，选项 (B) 错误。

进一步，我们我们有：

$$
f(x) + f(-x) = (x^3 - x + 1) + (-x^3 + x + 1) = 2, x \in \mathbb R
$$

这说明选项 (C) 正确。

如果选项 (D) 正确，假设其为 $x = x_0$ 处的切线，根据 $f'(x_0) = 3x_0^2 - 1 = 2$ 得到 $x_0 = \pm 1$。

此外，我们知道曲线 $y = f(x)$ 在 $x = x_0$ 处切线方程为：

$$
y = f'(x_0)(x - x_0) + f(x_0) = f'(x_0)x - x_0f'(x_0) + f(x_0) = 2x
$$

那么我们得到：

$$
-x_0f'(x_0) + f(x_0) = 0 \Rightarrow 2x_0 = f(x_0) = x_0^3 - x_0 + 1
$$

将 $x_0 = \pm 1$ 代入验证，发现均不成立，所以选项 (D) 错误。

答案为 (AC)。

## Problem 11

已知 $O$ 为坐标原点，点 $A(1, 1)$ 在抛物线 $C: x^2 = 2py(p > 0)$ 上，过点 $B(0, -1)$ 的直线交 $C$ 于 $P, Q$ 两点，则下列说法中正确的有：

(A) $C$ 的准线方程为 $y = -1$
(B) 直线 $AB$ 与 $C$ 相切
(C) $|OP| \cdot |OQ| > |OA|^2$
(D) $|BP| \cdot |BQ| > |BA|^2$

---

首先显然得到 $p = \dfrac12$，进而得到 $C$ 准线方程为 $y = -\dfrac14$，选项 (A) 错误。

直线 $AB$ 方程为 $y = 2x - 1$，代入抛物线方程得到 $x^2 = 2x - 1 \iff (x - 1)^2 = 0$，此方程显然仅有 $x = 1$ 一个实数根，所以选项 (B) 正确。

不妨假设 $P, Q$ 坐标分别为 $(x_1, x_1^2), (x_2, x_2^2)$。

如果假设 $PQ$ 斜率为 $k$，那么 $PQ$ 方程为 $y = kx - 1$，代入抛物线方程得到下述二次方程：

$$
x^2 = kx - 1 \iff x^2 - kx + 1 = 0
$$

$x_1, x_2$ 为上述方程的两根，从而我们有 $x_1x_2 = 1$。

那么：

$$
\begin{aligned}
|OP|^2 \cdot |OQ|^2 &= (x_1^2 + x_1^4)(x_2^2 + x_2^4) \\
&= (x_1^2 + x_1^4)\left(\frac{1}{x_1^2} + \frac{1}{x_1^4}\right) \\
&= 2 + x_1^2 + \frac{1}{x_1^2} \\
&\geq 4 = |OA|^4
\end{aligned}
$$

上述等号无法取到，因为 $x_1 \neq x_2$，从而 $x_1 \neq \pm 1$。这说明了选项 (C) 正确。

现在继续计算 (D) 选项，我们有：

$$
\begin{aligned}
|BP|^2 \cdot |BQ|^2 &= (x_1^2 + (x_1^2 + 1)^2)(x_2^2 + (x_2^2 + 1)^2) \\
&= (x_1^4 + 3x_1^2 + 1)\left(\frac{1}{x_1^4} + \frac{3}{x_1^2} + 1\right) \\
&= 11 + \left(x_1^4 + \frac{1}{x_1^4}\right) + 6\left(x_1^2 + \frac{1}{x_1^2}\right) \\
&\geq 25 = |BA|^4
\end{aligned}
$$

上述等号无法取到，因为 $x_1 \neq x_2$，从而 $x_1 \neq \pm 1$。这说明了选项 (D) 正确。

所以答案为 (BCD)。

## Problem 12

已知函数 $f(x)$ 以及其导函数 $f'(x)$ 的定义域均为 $\mathbb R$，记 $g(x) := f'(x)$。

如果 $f\left(\dfrac32 - x\right), g(2 + x)$ 均为偶函数，那么下列说法中正确的是：

(A) $f(0) = 0$
(B) $g\left(-\dfrac12\right) = 0$
(C) $f(-1) = f(4)$
(D) $g(-1) = g(2)$

---

首先需要熟知这样的结论，即导函数为偶函数，则原函数为关于某点中心对称，导函数为奇函数，则原函数为偶函数。

由于 $f(2 + x)$ 的导函数 $g(2 + x)$ 为偶函数，那么 $f(2 + x)$ 关于点 $(2, f(2))$ 中心对称。

那么我们可以给出下述推理：

$$
\begin{aligned}
f(x) &= f\left(\frac32 - \left(\frac32 - x\right)\right) = f\left(\frac32 + \left(\frac32 - x\right)\right) \\
&= f(3 - x) = f(2 + (1 - x)) = 2f(2) - f(2 - (1 - x)) \\
&= 2f(2) - f(1 + x)
\end{aligned}
$$

也就得到 $f(x + 2) = 2f(2) - f(x + 1) = f(x)$，$f(x)$ 为周期为 $2$ 的周期函数。

对于选项 (A)，如果 $f(2) \neq 0$，显然可知 $f(0) \neq 0$。而这是存在的，取反例 $f(x) = \sin(\pi x) + 1$ 即可。选项 (A) 错误。

根据 $f(x)$ 周期显然可知 $f(2n) = f(2), n \in \mathbb Z$。

同时有 $f(2n + 1) = f(1) = 2f(2) - f(2) = f(2), n \in \mathbb Z$。也就是说对所有整数 $n$，都有 $f(n) = f(2)$，所以选项 (C) 正确。

此外根据导函数定义（下面所有操作的基础均为导函数的存在性）：

$$
g\left(\frac32\right) = \lim_{\delta\to 0}\frac{f\left(\dfrac32 + \delta\right) - f\left(\dfrac32\right)}{\delta}
$$

根据 $f\left(\dfrac32 - x\right)$ 为偶函数，得到：

$$
g\left(\frac32\right) = \lim_{\delta\to 0}\frac{f\left(\dfrac32 - \delta\right) - f\left(\dfrac32\right)}{\delta} = \lim_{\delta\to 0}\frac{f\left(\dfrac32 + \delta\right) - f\left(\dfrac32\right)}{-\delta}
$$

上述第二个等号作换元 $\delta\to-\delta$。上述两个式子相加得到 $g\left(\dfrac32\right) = 0$，据 $g(x)$ 的周期性即可得到 $g\left(-\dfrac12\right) = 0$。选项 (B) 正确。

选项 (D) 存在反例 $f(x) = \sin(\pi x)$，该函数满足所有题设约束，但是不满足 (D) 要求。

所以答案为 (BC)。

# 填空题

## Problem 13

求 $\left(1 - \dfrac yx\right)(x + y)^8$ 的展开式中 $x^2y^6$ 的系数。

---

最终展开式中 $x^2y^6$ 项可能的出现方式为 $1\cdot x^2y^6$ 以及 $-\dfrac yx \cdot x^3y^5$。那么最终系数为：

$$
C_8^2 - C_8^3 = \frac{8 \times 7}{2!} - \frac{8 \times 7 \times 6}{3!} = -28
$$

故答案为 $-28$。

## Problem 14

写出与圆 $x^2 + y^2 = 1$ 和 $(x - 3)^2 + (y - 4)^2 = 16$ 都相切的一条直线的方程。

---

假设直线方程为 $ax + by + c = 0$，显然根据直线和圆相切的要求：

$$
\begin{cases}
\dfrac{|c|}{\sqrt{a^2 + b^2}} = 1 \\
\dfrac{|3a + 4b + c|}{\sqrt{a^2 + b^2}} = 4 \\
\end{cases}
$$

根据第一个方程显然有 $c \neq 0$，那么记 $a' := \dfrac ac, b' := \dfrac bc$，上述方程组即：

$$
\begin{cases}
\dfrac{1}{\sqrt{a'^2 + b'^2}} = 1 \\
\dfrac{|3a' + 4b' + 1|}{\sqrt{a'^2 + b'^2}} = 4 \\
\end{cases}
\Rightarrow
\begin{cases}
\sqrt{a'^2 + b'^2} = 1 \\
|3a' + 4b' + 1| = 4 \\
\end{cases}
$$

分情况讨论，若 $3a' + 4b' + 1 = 4 \iff 3a' + 4b' = 3$，那么：

$$
b' = \frac{3 - 3a'}{4} \Rightarrow a'^2 + b'^2 = a'^2 + \left(\frac{3 - 3a'}{4}\right)^2 = 1
$$

即：

$$
25a'^2 - 18a' - 7 = 0 \iff a' \in \left\{1, -\frac{7}{25}\right\}
$$

对应的 $b'$ 分别为 $0$ 和 $\dfrac{24}{25}$。

若 $3a' + 4b' + 1 = -4 \iff 3a' + 4b' = -5$，那么：

$$
b' = \frac{-5 - 3a'}{4} \Rightarrow a'^2 + b'^2 = a'^2 + \left(\frac{-5 - 3a'}{4}\right)^2 = 1
$$

得到 $a' = -\dfrac35$，对应的 $b' = -\dfrac45$。

从而得到答案为 $x = -1$ 或 $-7x + 24y + 25 = 0$ 或 $-3x - 4y + 5 = 0$。

## Problem 15

若曲线 $y = (x + a)e^x$ 有两条过坐标原点的切线，求 $a$ 的取值范围。

---

首先：

$$
y' = (x + a + 1)e^x
$$

那么曲线在 $x = x_0$ 处切线方程为：

$$
y = (x_0 + a + 1)e^{x_0}(x - x_0) + (x_0 + a)e^{x_0}
$$

该切线过坐标原点等价于：

$$
-x_0(x_0 + a + 1)e^{x_0} + (x_0 + a)e^{x_0} = 0 \iff x_0 + a = x_0(x_0 + a + 1)
$$

也就是说切线过坐标原点等价于 $x_0$ 为下述二次方程的根：

$$
x_0^2 + ax_0 - a = 0
$$

根据题设，上述方程有两个互不相同的实根，即：

$$
\Delta = a^2 + 4a > 0 \iff a \in (-\infty, -4) \cup (0, +\infty)
$$

所以答案为 $(-\infty, -4) \cup (0, +\infty)$。

## Problem 16

已知椭圆 $C: \dfrac{x^2}{a^2} + \dfrac{y^2}{b^2} = 1(a > b > 0)$，$C$ 的上顶点为 $A$，两个焦点为 $F_1, F_2$，离心率为 $\dfrac12$。过 $F_1$ 且垂直于 $AF_2$ 的直线交 $C$ 于 $D, E$ 两点，$|DE| = 6$，求 $\triangle ADE$ 的周长。

---

根据离心率，不妨假设 $a = 2t, b = \sqrt3 t, c = t(t > 0)$。那么 $A$ 坐标为 $(0, \sqrt3 t)$，$F_1, F_2$ 坐标为 $(\pm t, 0)$。

那么 $AF_2$ 斜率为 $-\sqrt 3$，得知 $DE$ 的斜率为 $\dfrac{1}{\sqrt 3}$，那么 $DE$ 方程为 $x = \sqrt3y - t$。代入椭圆方程得到：

$$
\dfrac{(\sqrt3y - t)^2}{4t^2} + \dfrac{y^2}{3t^2} = 1 \iff 13y^2 - 6\sqrt3 yt - 9t^2 = 0
$$

假设上述关于 $y$ 的方程的两个根为 $y_1, y_2$，那么 $y_1 + y_2 = \dfrac{6\sqrt3 t}{13}, y_1y_2 = -\dfrac{9}{13}t^2$。

我们显然知道：

$$
\begin{aligned}
|DE|^2 &= 4(y_1 - y_2)^2 = 4((y_1 + y_2)^2 - 4y_1y_2) \\
&= \left(\frac{48t}{13}\right)^2 = 36
\end{aligned}
$$

得到 $t = \dfrac{13}{8}$。

另外，直接求解方程得到：

$$
y_{1, 2} = \frac{3\sqrt 3 \pm 12}{13}t = \frac{3\sqrt 3}{8} \pm \frac32
$$

从而得到 $D, E$ 的坐标为：

$$
\left(\frac{3\sqrt 3}{2} - \frac12, \frac{3\sqrt 3}{8} + \frac32\right), \left(-\frac{3\sqrt 3}{2} - \frac12, \frac{3\sqrt 3}{8} - \frac32\right)
$$

考虑到 $A$ 坐标为 $\left(0, \dfrac{13\sqrt 3}{8}\right)$，得到：

$$
\begin{aligned}
|AD|^2 &= \left(\frac{3\sqrt 3}{2} - \frac12\right)^2 + \left(\frac{3\sqrt 3}{8} + \frac32 - \dfrac{13\sqrt 3}{8}\right)^2 \\
&= \frac{223 - 84\sqrt 3}{16} \\
|BD|^2 &= \left(-\frac{3\sqrt 3}{2} - \frac12\right)^2 + \left(\frac{3\sqrt 3}{8} - \frac32 - \dfrac{13\sqrt 3}{8}\right)^2 \\
&= \frac{223 + 84\sqrt 3}{16} \\
\end{aligned}
$$

不难发现 $(14 \pm 3\sqrt 3)^2 = 223 \pm 84\sqrt 3$，所以：

$$
|AD| + |BD| = \frac{14 - 3\sqrt 3}{4} + \frac{14 + 3\sqrt 3}{4} = 7
$$

从而需要求的三角形周长为 $13$。即最后的答案为 $13$。

---

【另解】我自己也是算完之后才回想起来有这么一个简单的结论我忘了，离心率为 $\dfrac12$ 的椭圆，其上顶点和两个焦点构成等边三角形。

这个事实意味着直线 $DE$ 不仅垂直于 $AF_2$，而且是垂直平分 $AF_2$。也就是说 $\triangle ADE$ 全等于 $\triangle DEF_2$。那么其周长：

$$
C_{\triangle DEF_2} = (|DF_1| + |DF_2|) + (|EF_1| + |EF_2|) = 4a = 13
$$

# 解答题

这次解答题的顺序是数列、解三角形、立体几何、统计、解析几何、导数，中规中矩的顺序。

## Problem 17

记 $S_n$ 为数列 $\{a_n\}$ 的前 $n$ 项和，已知 $a_1 = 1$，且数列 $\left\{\dfrac{S_n}{a_n}\right\}$ 是公差为 $\dfrac13$ 的等差数列。

(1) 求 $\{a_n\}$ 的通项公式。

(2) 求证：

$$
\sum_{k = 1}^n \frac{1}{a_k} < 2, n \in \mathbb N
$$

---

【第一问】根据数列 $\left\{\dfrac{S_n}{a_n}\right\}$ 是公差为 $\dfrac13$ 的等差数列，并且我们能得到其首项为：

$$
\frac{S_1}{a_1} = 1
$$

那么我们得到其通项公式为：

$$
\frac{S_n}{a_n} = \frac13 n + \frac23 \iff 3S_n = (n + 2)a_n
$$

取 $n + 1$ 代替 $n$，得到等式 $3S_{n + 1} = (n + 3)a_{n + 1}$，上述两式相减：

$$
3(S_{n + 1} - S_n) = (n + 3)a_{n + 1} - (n + 2)a_n \iff (n + 2)a_n = na_{n + 1}
$$

也就是下述关系：

$$
\frac{a_{n + 1}}{a_n} = \frac{n + 2}{n}, n \in \mathbb N
$$

那么对于 $n \geq 2$：

$$
\begin{aligned}
a_n &= \frac{a_n}{a_{n - 1}} \cdot \frac{a_{n - 1}}{a_{n - 2}} \cdot \cdots \cdot \frac{a_2}{a_1} = \frac{n + 1}{n - 1} \cdot \frac{n}{n - 2} \cdot \cdots \cdot \frac{3}{1} \\
&= \frac{n(n + 1)}{2}
\end{aligned}
$$

经检验，$n = 1$ 时上述通项公式也成立，所以数列 $\{a_n\}$ 的通项公式为：

$$
a_n = \frac{n(n + 1)}{2}, n \in \mathbb N
$$

【第二问】可以计算得到：

$$
\begin{aligned}
\sum_{k = 1}^n \frac{1}{a_k} = \sum_{k = 1}^n \frac{2}{k(k + 1)} = 2\sum_{k = 1}^n \left(\frac{1}{k} - \frac{1}{k + 1}\right) = 2\left(1 - \frac{1}{n + 1}\right) < 2
\end{aligned}
$$

## Problem 18

记 $\triangle ABC$ 的内角 $A, B, C$ 的对边分别为 $a, b, c$。已知下述关系：

$$
\frac{\cos A}{1 + \sin A} = \frac{\sin 2B}{1 + \cos 2B}
$$

(1) 若 $C = \dfrac{2\pi}{3}$，求 $B$

(2) 求 $\dfrac{a^2 + b^2}{c^2}$ 的最小值

---

【第一问】根据二倍角公式有：

$$
\frac{\sin 2B}{1 + \cos 2B} = \frac{2\sin B\cos B}{1 + (2\cos^2 B - 1)} = \frac{\sin B}{\cos B}
$$

代入已知等式：

$$
\frac{\cos A}{1 + \sin A} = \frac{\sin B}{\cos B}
$$

整理得到：

$$
\cos A\cos B = \sin B + \sin A\sin B \Rightarrow \sin B = \cos A\cos B - \sin A\sin B = \cos(A + B)
$$

根据三角形内角和，有 $A + B + C = \pi \Rightarrow A + B = \pi - C$，即：

$$
\sin B = \cos(\pi - C) = -\cos C
$$

已知 $C = \dfrac{2\pi}{3}$，所以：

$$
\sin B =  -\cos\frac{2\pi}{3} = \frac12
$$

由于 $B$ 位于三角形内，所以 $B \in (0, \pi)$。

另外，考虑到 $C = \dfrac{2\pi}{3}$，若 $B > \dfrac\pi2$，则 $A = \pi - C - B < 0$，不符合要求，所以 $B < \dfrac\pi2$。

从而 $B = \dfrac\pi6$。

【第二问】根据上一问：

$$
\cos(A + B) = \sin B = \cos\left(\frac\pi2 - B\right)
$$

由于 $A + B, \dfrac\pi2 - B \in (0, \pi)$，而余弦函数在这一个区间单调，所以：

$$
A + B = \dfrac\pi2 - B \iff A + 2B = \frac\pi2
$$

根据正弦定理：

$$
\frac{a^2 + b^2}{c^2} = \frac{\sin^2A + \sin^2B}{\sin^2C}
$$

使用 $-\cos C = \sin B$ 替换，并代入 $A + 2B = \dfrac\pi2$：

$$
\begin{aligned}
\frac{a^2 + b^2}{c^2} &= \frac{\sin^2A + \sin^2B}{\sin^2C} = \frac{\sin^2\left(\dfrac\pi2 - 2B\right) + \sin^2B}{1 - \cos^2C} \\
&= \frac{\cos^2 2B + \sin^2B}{1 - \sin^2B} = \frac{(2\cos^2 B - 1)^2 + (1 - \cos^2B)}{\cos^2B} \\
&= 4\cos^2B + \frac{2}{\cos^2B} - 5 \geq 4\sqrt2 - 5
\end{aligned}
$$

等号成立当且仅当 $\cos B = \dfrac{1}{\sqrt[4]{2}}$。

即 $\dfrac{a^2 + b^2}{c^2}$ 的最小值为 $4\sqrt2 - 5$。

## Problem 19

已知直三棱柱 $ABC-A_1B_1C_1$ 的体积为 $4$，$\triangle A_1BC$ 的面积为 $2\sqrt 2$。

(1) 求 $A$ 到平面 $A_1BC$ 的距离

(2) 设 $D$ 为 $A_1C$ 的中点，已知 $AA_1 = AB$ 且平面 $A_1BC$ 垂直于平面 $ABB_1A_1$，求二面角 $A-BD-C$ 的正弦值。

---

【第一问】考虑三棱锥 $A_1-ABC$，其和直三棱柱 $ABC-A_1B_1C_1$ 有相同的底面 $\triangle ABC$ 且有相同的高 $AA_1$，所以其体积为直三棱柱 $ABC-A_1B_1C_1$ 的三分之一，即：

$$
V_{A_1-ABC} = \frac13 \times 4 = \frac43
$$

如果记 $A$ 到平面 $A_1BC$ 的距离为 $h$，$\triangle A_1BC$ 的面积为 $S$，那么 $V_{A_1-ABC} = \dfrac13 Sh$。从而得到：

$$
\dfrac13 Sh = \frac43 \Rightarrow Sh = 4
$$

由题设有 $S = 2\sqrt 2$，所以 $A$ 到平面 $A_1BC$ 的距离为：

$$
h = \frac{4}{2\sqrt 2} = \sqrt 2
$$

【第二问】

在直三棱柱 $ABC-A_1B_1C_1$ 中，我们有 $BB_1$ 垂直于平面 $ABC$，考虑到 $BC \subset$ 平面 $ABC$，所以 $BB_1 \bot BC$。

另一方面，直三棱柱 $ABC-A_1B_1C_1$ 中侧面 $ABB_1A_1$ 为长方形，而 $AA_1 = AB$，所以该侧面为正方形，进而 $AB_1 \bot A_1B$。

由于平面 $A_1BC$ 垂直于平面 $ABB_1A_1$ 且两平面交于 $A_1B$，考虑到 $AB_1 \subset$ 平面 $ABB_1A_1$，所以 $AB_1$ 垂直于平面 $A_1BC$。而又由于 $BC \subset$ 平面 $ABC$，所以 $AB_1 \bot BC$。

因为 $AB_1, BB_1$ 都是平面 $ABB_1A_1$ 内的直线且两者相交于 $B_1$，而直线 $BC$ 与两者均垂直，所以 $BC$ 垂直于平面 $ABB_1A_1$。考虑到 $AB \subset$ 平面 $ABB_1A_1$，所以 $AB \bot BC$。同理依然有 $A_1B \bot BC$。

也就是说 $\triangle ABC$ 为以 $AC$ 为斜边的直角三角形，$\triangle A_1BC$ 为以 $A_1C$ 为斜边的直角三角形。设 $AA_1 = AB = a, BC = b$，那么 $A_1B = \sqrt2 a$，$\triangle ABC$ 面积为 $\dfrac12 ab$，$\triangle A_1BC$ 面积为 $\dfrac{\sqrt 2}{2}ab = 2\sqrt 2$。

那么直三棱柱 $ABC-A_1B_1C_1$ 的体积为 $a\cdot \dfrac12 ab = \dfrac12 a^2b = 4$。

从而得到 $a = b = 2$。

上述论述中我们已经说明了 $AB, B_1B, BC$ 两两垂直，所以以 $B$ 为坐标原点，$BC, BA, BB_1$ 分别为 $x, y, z$ 轴建立空间直角坐标系。

那么 $A$ 坐标为 $(0, 2, 0)$，$A_1$ 坐标为 $(0, 2, 2)$，$C$ 坐标为 $(2, 0, 0)$。

由于 $D$ 为 $A_1C$ 中点，所以其坐标为 $(1, 1, 1)$。

那么 $\vec{BA} = (0, 2, 0), \vec{BD} = (1, 1, 1), \vec{BC} = (2, 0, 0)$。

假设平面 $ABD$ 的法向量为 $\boldsymbol{n} = (x_1, y_1, z_1)$，根据 $\boldsymbol{n} \bot \vec{BA}, \vec{BD}$ 得到：

$$
\begin{cases}
x_1 + y_1 + z_1 = 0 \\
2y_1 = 0
\end{cases}
$$

不妨取 $\boldsymbol{n} = (1, 0, -1)$。同理可以取平面 $CBD$ 的法向量 $\boldsymbol{m} = (0, 1, -1)$。

记欲求二面角大小为 $\theta$，可以得到二面角 $A-BD-C$ 的余弦值绝对值为：

$$
|\cos\langle\boldsymbol{n}, \boldsymbol{m}\rangle| = \left|\frac{\boldsymbol{n} \cdot \boldsymbol{m}}{|\boldsymbol{n}| \cdot |\boldsymbol{m}|}\right| = \frac{1}{2}
$$

从而得到该二面角的正弦值为：

$$
\sin\theta = \sqrt{1 - |\cos\theta|^2} = \frac{\sqrt 3}{2}
$$

## Problem 20

概率统计相关问题。医疗团队针对某一种疾病和个人卫生的关系做调查，抽样 $100$ 名该病患者构成病例组和 $100$ 名未患有该病的健康人构成对照组，调查他们的个人卫生，得到下述数据：

|  | 个人卫生不够良好 | 个人卫生良好 |
| :-: | :-: | :-: |
| 病例组 | $40$ | $60$ |
| 对照组 | $10$ | $90$ |

(1) 能否有 $99\%$ 的把握认为患该病的群体和未患该病的群体个人卫生情况有差异

(2) 从该地区人群中任选一人，记 $A$ 表示事件“选到的人卫生习惯不够良好”，$B$ 表示事件“选到的人患有该疾病”。记指标 $R$ 为：

$$
R = \frac{P(B \mid A)}{P(\overline B \mid A)} \cdot \frac{P(\overline B | \overline A)}{P(B \mid \overline A)}
$$

(i) 证明：

$$
R = \frac{P(A \mid B)}{P(\overline A \mid B)} \cdot \frac{P(\overline A | \overline B)}{P(A \mid \overline B)}
$$

(ii) 利用调查数据估计 $P(A \mid B), P(A \mid \overline B)$，并根据上述结论估计 $R$ 值。

---

【第一问】直接使用卡方检验：

$$
K^2 = \frac{n(ad - bc)^2}{(a + b)(c + d)(a + c)(b + d)} = \frac{200 \times (40 \times 90 - 10 \times 60)^2}{100 \times 100 \times 50 \times 150} = 24 > 6.635
$$

可知我们有 $99\%$ 的把握认为患该病的群体和未患该病的群体个人卫生情况有差异。

【第二问】我们首先证明第一小问的等式：

根据 $R$ 的定义和条件概率的定义：

$$
\begin{aligned}
R &= \frac{P(B \mid A)}{P(\overline B \mid A)} \cdot \frac{P(\overline B | \overline A)}{P(B \mid \overline A)} = \frac{\dfrac{P(AB)}{P(A)} \cdot \dfrac{P(\overline A\overline B)}{P(\overline A)}}{\dfrac{P(A\overline B)}{P(A)} \cdot \dfrac{P(\overline AB)}{P(\overline A)}} \\
&= \frac{P(AB)P(\overline A\overline B)}{P(A\overline B)P(\overline AB)} \\
&= \frac{\dfrac{P(AB)}{P(B)} \cdot \dfrac{P(\overline A\overline B)}{P(\overline B)}}{\dfrac{P(A\overline B)}{P(\overline B)} \cdot \dfrac{P(\overline AB)}{P(B)}} = \frac{P(A \mid B)}{P(\overline A \mid B)} \cdot \frac{P(\overline A | \overline B)}{P(A \mid \overline B)}
\end{aligned}
$$

容易根据调查数据估计出：

$$
P(A \mid B) = 0.4, P(\overline A \mid B) = 0.6, P(\overline A | \overline B) = 0.9, P(A \mid \overline B) = 0.1
$$

此时 $R = 6$。

## Problem 21

已知点 $A(2, 1)$ 在双曲线 $C: \dfrac{x^2}{a^2} - \dfrac{y^2}{a^2 - 1} = 1(a > 1)$ 上，直线 $l$ 交 $C$ 于 $P, Q$ 两点，直线 $AP, AQ$ 的斜率之和为 $0$。

(1) 求 $l$ 的斜率

(2) 若 $\tan\angle PAQ = 2\sqrt 2$，求 $\triangle PAQ$ 的面积

---

【第一问】由于 $A(2, 1)$ 在双曲线 $C$ 上，所以：

$$
\dfrac{4}{a^2} - \dfrac{1}{a^2 - 1} = 1 \Rightarrow a^2 = 2
$$

不妨记 $AP$ 的斜率为 $k$，那么 $AQ$ 的斜率为 $-k$。从而 $AP$ 的方程为 $y = k(x - 2) + 1$，将其代入双曲线方程得到：

$$
\dfrac{x^2}{2} - (kx - 2k + 1)^2 = 1
$$

整理为：

$$
\left(\frac{1}{2} - k^2\right)x^2 + 2(2k - 1)kx - (1 + (2k - 1)^2) = 0
$$

由于 $A$ 为双曲线和 $AP$ 的交点之一，所以 $x = 2$ 为其一根，另一根为 $P$ 的横坐标。

上述方程的两根之积为：

$$
-\frac{1 + (2k - 1)^2}{\dfrac{1}{2} - k^2} = -\frac{2 + 2(2k - 1)^2}{1 - 2k^2}
$$

得知其横坐标即可计算其纵坐标，所以 $P$ 的坐标为：

$$
\left(-\frac{1 + (2k - 1)^2}{1 - 2k^2}, \frac{2k^2 - 4k + 1}{1 - 2k^2}\right)
$$

将 $k$ 替换为 $-k$ 得到 $Q$ 的坐标：

$$
\left(-\frac{1 + (2k + 1)^2}{1 - 2k^2}, \frac{2k^2 + 4k + 1}{1 - 2k^2}\right)
$$

也就是说 $l$ 的斜率为：

$$
k_l = \frac{-\dfrac{1 + (2k + 1)^2}{1 - 2k^2} + \dfrac{1 + (2k - 1)^2}{1 - 2k^2}}{\dfrac{2k^2 + 4k + 1}{1 - 2k^2} - \dfrac{2k^2 - 4k + 1}{1 - 2k^2}} = -1
$$

【第二问】由于 $\tan\angle PAQ > 0$，所以其为锐角。我们可以知道 $\tan\dfrac12\angle PAQ = \dfrac1k$，这里 $k$ 为 $AP, AQ$ 斜率的绝对值。从而：

$$
\tan\angle PAQ = \frac{2\tan\dfrac12\angle PAQ}{1 - \tan^2\dfrac12\angle PAQ} = \frac{2 / k}{1 - 1 / k^2} = 2\sqrt 2
$$

由 $k > 0$ 得到 $k = \sqrt 2$。

从而根据上一问的结果，我们得到：

$$
P\left(\frac{10 - 4\sqrt 2}{3}, \frac{4\sqrt 2 - 5}{3}\right), Q\left(\frac{10 + 4\sqrt 2}{3}, \frac{-4\sqrt 2 - 5}{3}\right)
$$

此时 $PQ$ 方程为 $y = -x + \dfrac53$，$A$ 到此的距离为：

$$
d = \frac{|2 + 1 - 5 / 3|}{\sqrt 2} = \frac{2\sqrt 2}{3}
$$

而：

$$
|PQ| = \sqrt 2 \left(\frac{10 + 4\sqrt 2}{3} - \frac{10 - 4\sqrt 2}{3}\right) = \frac{16}{3}
$$

所以需要求的面积为：

$$
S = \frac12 |PQ| \cdot d = \frac{16\sqrt 2}{9}
$$

## Problem 22

已知函数 $f(x) = e^x - ax$ 与函数 $g(x) = ax - \ln x$ 有相同的最小值。

(1) 求 $a$

(2) 证明存在实数 $b$，令直线 $y = b$ 和曲线 $y = f(x), y = g(x)$ 共计有三个不同的交点，且这三个交点的横坐标从小到大排列构成等差数列。

---

【第一问】显然：

$$
f'(x) = e^x - a, g'(x) = a - \frac1x
$$

由于这两个函数有相同的最小值，所以：

$$
f(\ln a) = a - a\ln a = 1 + \ln a = g\left(\frac1a\right)
$$

也就是说：

$$
\ln a - \frac{a - 1}{a + 1} = 0
$$

另外，由最小值的存在性显然有 $a > 0$。考虑函数：

$$
\phi(x) := \ln x - \frac{x - 1}{x + 1}, x > 0
$$

其有：

$$
\phi'(x) = \frac1x - \frac{2}{(x + 1)^2} = \frac{x^2 + 1}{x(x + 1)^2} > 0
$$

由 $\phi(a) = \phi(1) = 0$ 得到 $a = 1$。

【第二问】首先证明，$f(x) - g(x) = 0$ 在 $(0, +\infty)$ 上有唯一的解 $x_0$。

定义函数 $h(x) := f(x) - g(x)$。

根据上一问，得知 $f(x)$ 在 $(0, +\infty)$ 上递增，$g(x)$ 在 $(0, 1)$ 上递减，这说明 $h(x)$ 在 $(0, 1)$ 上递增。

另外：

$$
h'(x) = e^x + \frac1x - 2 > e - 2 > 0, x > 1
$$

所以 $h(x)$ 在 $(1, +\infty)$ 上递增，即 $h(x)$ 在 $(0, +\infty)$ 上递增。

此外：

$$
h(1) = e - 2 > 0
$$

另外：

$$
g(e^{1 - e}) = e^{1 - e} - (1 - e) > e - 1 = f(1) > f(e^{1 - e})
$$

故 $h(e^{1 - e}) < 0$。

这说明了 $x_0$ 唯一存在于区间 $(e^{1 - e}, 1)$ 上。

下面证明 $b = f(x_0) = g(x_0)$ 满足条件。

在此之前，我们需要知道由于 $0 < x_0 < 1$，所以 $1 = f(0) < b = f(x_0) < e - 1 = f(1)$。

首先证明 $f(x) = b$ 有除 $x_0$ 外，还具有一根 $x_1 \in (-\infty, 0)$。由于 $f(x)$ 在 $(-\infty, 0)$ 上递减，并且 $f(0) < b$，且 $f(1 - e) = e^{1 - e} + (e - 1) > e - 1 > b$。

这说明了 $x_1$ 唯一存在于 $(1 - e, 0)$ 上。

再证明 $g(x) = b$ 有除 $x_0$ 外，还具有一根 $x_2 \in (1, +\infty)$。由于 $g(x)$ 在 $(1, +\infty)$ 上递增，并且 $g(1) = 1 < b$，且 $g(e) = e - 1 > b$。

这说明了 $x_2$ 唯一存在于 $(1, e)$ 上。

也就是说直线 $y = b$ 和曲线 $y = f(x), y = g(x)$ 共计有三个不同的交点，其横坐标从小到大为 $x_1, x_0, x_2$。

现在观察下述：

$$
\begin{cases}
f(\ln x_0) = e^{\ln x_0} - \ln x_0 = x_0 - \ln x_0 = g(x_0) = b \\
g(e^{x_0}) = e^{x_0} - \ln e^{x_0} = e^{x_0} - x_0 = f(x_0) = b \\
\end{cases}
$$

这说明 $\ln x_0, e^{x_0}$ 也是直线 $y = b$ 和曲线 $y = f(x), y = g(x)$ 交点的横坐标，但我们已经证明了一共仅有三个交点，这只能说明 $x_1 = \ln x_0, x_2 = e^{x_0}$。

由于 $f(x_0) = g(x_0)$，所以 $e^{x_0} + \ln x_0 = x_1 + x_2 = 2x_0$。

所以这三个横坐标从小到大排列为等差数列，证明完毕。

# 后记

卷子做完了，Holder 也已经明白了我已经没有高中时候那种手算能力了。

不少人说这份卷子很难，其实我看真正算得上是难题的几乎没有，几乎每道题都有很自然的思考角度和解决方式。

但问题就是现在大部分的高考备考流于做模板题，练死思路，结果就是这次高考连稍微难点的解三角形、立体几何都做不出来。

复习不够全面，可能连条件概率的定义、棱台的体积公式都会忘记。

---

这份卷子只不过是把平时用来送分的解答题前几条，数列、解三角形、立体几何稍微出得有了点新意，稍微加了点门槛。卷子其他地方平心而论，概率与统计只要记得条件概率定义五分钟就能写完，解析几何是考烂了的二级结论，选择填空也乏善可陈。但就是这样，就能把平时的中档生直接卡住。

而且这份卷子的特征就是，思路的高下直接影响分数。想到了妙手，就能一分钟内看出答案，想不到，那就和我做填空题最后一问一样算到起飞还得不到答案。一旦陷入了这种状况，如果不及时调整思路，可能影响的是后面的解题状态。

说明死方法没用了，脑子要灵活。总而言之，是一份好卷子，但是被骂也是理所应当。
