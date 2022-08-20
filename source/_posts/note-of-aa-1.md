---
title: 《抽象代数 (1)》学习笔记
date: 2022-05-31 21:14:16
category:
    - 【学习笔记】数学
mathjax: true
---

大约是由于还是想学一点数学，所以大四趁着没有培养方案要求了选了《抽象代数 (2)》。但是平心而论自己的抽象代数水平不过是翻过一点点 Artin 而已，于是问同学要来了这位老师《抽象代数 (1)》的课件，自己先学着一点吧。

<!-- more -->

# 抽象代数引论

我们现在关注几何体的对称性，不妨先考虑下述定义：

{% note info no-icon %}
**Definition 1.01** 我们定义标准欧氏空间 $\mathbb R^n$ 上的**等距（Isometry）**变换 $f: \mathbb R^n \to \mathbb R^n$ 是满足对于任何的 $x, y \in \mathbb R^n$ 都有：

$$
d(x, y) = d(f(x), f(y))
$$

的变换。

这里 $d(x, y)$ 表示欧氏空间 $\mathbb R^n$ 上两点 $x, y$ 的欧氏距离。
{% endnote %}

此外，我们沿用下述定义：

{% note info no-icon %}
**Definition 1.02** 已知内积空间 $V$ 上的线性变换 $\mathcal{A}: V \to V$，我们称 $\mathcal{A}$ 是**正交变换**，如果其保持内积，即对于任何的 $x, y \in V$，都有：

$$
x \cdot y = \mathcal{A}(x) \cdot \mathcal{A}(y)
$$

这里 $\cdot$ 表示线性空间 $V$ 上的内积。
{% endnote %}

我们可以证明下述定理：

{% note success no-icon %}
**Theorem 1.01** 任何的等距变换均可以唯一分解为一个正交变换和平移变换的复合。
{% endnote %}

不妨假设我们我们现在讨论的是 $\mathbb{R}^n$ 上的等距变换 $f$，不妨定义 $a := f(\boldsymbol{0}_n)$，定义平移变换：

$$
g: x \mapsto x - a
$$

我们下面只需要证明 $h := g \circ f$ 是一个正交变换即可说明该分解的存在性，因为如果该断言成立就说明 $f = g^{-1} \circ h$，这样就构造出了 $f$ 的分解。

首先显然能够得到 $h$ 也是等距变换，这里省略证明。同时我们还得知 $h$ 保原点，即 $h(\boldsymbol{0}_n) = \boldsymbol{0}_n$。

现在我们讨论的内积空间是 $\mathbb{R}^n$。基于此，根据 $h$ 的等距性质得到对于任何的 $x, y \in \mathbb{R}^n$ 都有：

$$
[h(x) - h(y)]^T[h(x) - h(y)] = (x - y)^T(x - y)
$$

令 $y = \boldsymbol{0}_n$ 得到对于任何的 $x \in \mathbb{R}^n$ 都有：

$$
h(x)^Th(x) = x^Tx
$$

展开上述根据等距变换得到的式子：

$$
h(x)^Th(x) + h(y)^Th(y) - 2h(x)^Th(y) = x^Tx + y^Ty - 2x^Ty
$$

根据推论即可知道 $h(x)^Th(x) = x^Tx, h(y)^Th(y) = y^Ty$，即可得到对于任何的 $x, y \in \mathbb{R}^n$ 都有：

$$
h(x)^Th(y) = x^Ty
$$

这说明了 $h$ 是保内积的。

那么任意取 $x_1 \neq x_2, y_1, y_2 \in \mathbb{R}^n$，显然有：

$$
h(x_i)^Th(y_1 + y_2) = x_i^T(y_1 + y_2) = x_i^Ty_1 + x_i^Ty_2 = h(x_i)^Th(y_1) + h(x_i)^Th(y_2) 
$$

这里 $i \in \{1, 2\}$，从而：

$$
h(x_i)^T[h(y_1 + y_2) - h(y_1) - h(y_2)] = 0, i \in \{1, 2\}
$$

两式作差：

$$
[h(x_1) - h(x_2)]^T[h(y_1 + y_2) - h(y_1) - h(y_2)] = 0
$$

显然 $h(x_1) \neq h(x_2)$，否则 $|h(x_1) - h(x_2)| = 0 \neq |x_1 - x_2|$。所以只能得到：

$$
h(y_1 + y_2) = h(y_1) + h(y_2)
$$

同样，任意取 $x_1 \neq x_2, y \in \mathbb{R}^n$ 以及 $a \in \mathbb{R}$，也能同理推出下述：

$$
[h(x_1) - h(x_2)]^T[h(ay) - ah(y)] = 0
$$

最后得到：

$$
h(ay) = ah(y)
$$

这就说明了 $h$ 是线性的，结合其保内积的性质得知 $h$ 是正交变换。

---

下面说明这样的分解唯一。事实上我们只需要考虑正交变换保原点即可得知分解出的平移变换仅有可能是 $g: x \mapsto x + f(\boldsymbol{0}_n)$。而根据平移变换的可逆性，显然不可能具有多个正交变换 $h$ 令 $f = h \circ g$，即仅有可能令 $h := f \circ g^{-1}$，唯一性证明完毕。
