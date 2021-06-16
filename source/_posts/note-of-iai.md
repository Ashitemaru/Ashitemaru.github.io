---
title: 《人工智能导论》学习笔记
date: 2021-06-16 20:27:16
mathjax: true
category:
    - 学习笔记
---

这个文档就是一时兴起想要好好学学人工智能背后的玩意创建的。主要还是记录《人工智能导论》课程的笔记。

<!-- more -->

大概随着这个文档越来越饱满，我应该就能看懂`paper.md`这个诡异的超纲文档了。

## 搜索问题

### 盲目搜索（BFS / DFS）

DFS 优先扩展深度深的节点， BFS 优先扩展深度浅的节点。由于 DFS 往往存在深度限制，所以 DFS 是有可能找不到最优解的，并且最坏情况下 DFS 等价于枚举。而 BFS 在**单位耗散值的有解问题**上一定能找到最优解。

但有的时候搜索树上的相邻节点之间距离并非一致，所以出现了 Dijkstra 算法。这个算法是最短路算法之中较为重要的一个。在 Dijkstra 算法之中，原则是优先扩展**距离起点最近的节点**。

Dijkstra 算法考虑了当前节点与起点的距离，但是实则没有对距离终点的距离进行评估。所以之后可以引入启发式搜索。

### 启发式搜索（A / A*）

所谓启发式，就是引入**启发知识**，也就是对当前节点与目标之间的距离的评估。

在 A 算法之中，我们引入对节点 $n$ 的**评估函数** $f(n)$ ：

$$
f(n) = g(n) + h(n)
$$

这里 $h(n)$ 就是对当前节点与目标之间距离的评估，也被称为**启发函数**。

上述函数均为**估计值**。我们把相应的实际值（也就是最短路所对应的）分别标记为 $f^\*(n), g^\*(n), h^\*(n)$ 。

A 算法的核心为，优先扩展 $f(n)$ 最小的节点。

在算法的具体实现过程之中，定义了`OPEN`表以及`CLOSE`表，分别代表**当前考虑扩展的节点**以及**当前暂时不考虑扩展的节点**。一份伪代码实现为：

{% codeblock lang:python Python %}
OPEN = ['s'] # 's' is the starting node
CLOSE = []
while not OPEN.empty():
    # Select the node with minimum f value
    n = OPEN.node_with_min_f()
    
    # n is the expected node
    if expected(n):
        return n
    
    OPEN.remove(n)
    CLOSE.add(n)
    
    # Traverse the neighbors
    for m in n.neighbor():
        path_len = g(n) + distance(n, m)
        # Expand the list
        if (not m in OPEN) and (not m in CLOSE):
            OPEN.add(m)
            n.next = m
        # m has been explored before, but it needs update
        elif (m in OPEN) and (path_len < g(m)):
            g(m) = path_len
            n.next = m
        # m has been discarded, but it can be reused now
        elif (m in CLOSE) and (path_len < g(m)):
            CLOSE.remove(m)
            OPEN.add(m)
            n.next = m
# Fail
return FAIL
{% endcodeblock %}

在 A 算法之中，如果满足：

$$
h(n) \leq h^\*(n)
$$

那么 A 算法就称为 A\* 算法。 A\* 算法的特点就在于**只要初始节点到目标节点有路径，那么算法永远能找到最优解**，而 A 算法并不保证有最优解。

另外给出一个定理：

>对于两个 A\* 算法 $A\_1, A\_2$ ，如果对于非目标节点均满足：
>
>$$
>h\_2(n) > h\_1(n)
>$$
>
>那么 $A\_1$ 所扩展的节点数不小于 $A\_2$ 所扩展的节点数。

### 改进 A* 算法

A* 算法面临的一个问题在于**可能会多次扩展同一个节点**，这就导致了算法的低效，而实际上多次扩展某一个节点的原因在于扩展该节点所使用的路径并非是最短的。

我们可以认为可以使用**单调的**启发函数来解决这一问题，启发函数的单调性指的是三角形法则：

$$
\\begin{cases}
	h(n\_i) - h(n\_j) \leq {\rm distance}(n\_i, n\_j) \\\\
	h(t) = 0
\\end{cases}
$$

这里 $n\_j$ 为 $n\_i$ 的子节点。

我们可以证明如果 $h$ 是单调的，那么使用这个启发函数的 A\* 算法一旦扩展到了某一个节点 $n$ ，就已经找到了从起点到这个节点的最短路，也就是说 $g(n) = g^\*(n)$ 。

实际上还有一个结论，就是满足单调性的 $h$ 必然满足 A* 条件。

基于此改进 A* 算法如下（伪代码表示）：

{% codeblock lang:python Python %}
OPEN = ['s'] # 's' is the starting node
CLOSE = []
f_max = 0 # The maximum f value that has been found till now
while not OPEN.empty():
    # Find the nodes in OPEN with f value less than f_max
    NEST = OPEN.with_f_less_than(f_max)
    
    # Select the node
    # When the f value has been small enough to be selected into NEST
    # We just need to select the minimum g value now
    n = None
    if NEST.empty():
        n = OPEN.node_with_min_f()
        f_max = f(n)
    else:
        n = NEST.node_with_min_g()
    
    # n is the expected node
    if expected(n):
        return n
    
    OPEN.remove(n)
    CLOSE.add(n)
    
    # Traverse the neighbors
    for m in n.neighbor():
        path_len = g(n) + distance(n, m)
        # Expand the list
        if (not m in OPEN) and (not m in CLOSE):
            OPEN.add(m)
            n.next = m
        # m has been explored before, but it needs update
        elif (m in OPEN) and (path_len < g(m)):
            g(m) = path_len
            n.next = m
        # m has been discarded, but it can be reused now
        elif (m in CLOSE) and (path_len < g(m)):
            CLOSE.remove(m)
            OPEN.add(m)
            n.next = m
# Fail
return FAIL
{% endcodeblock %}

## 博弈问题（对抗搜索）

### $\alpha-\beta$ 剪枝

我们在面临双方博弈，需要我方决策的时候，我们可以先搜索一遍所有可能的双方情况并使用专家知识对局面进行评估。由于对方有利就是我方不利，所以在搜索到最底部回溯得到估值的时候，要注意并不是始终取孩子节点的最大值，而是按层数交替取最大值和最小值。

一般而言，根节点要取最大的儿子节点（一般用方形节点代表**极大过程**），下一层要取最小的儿子节点（一般用圆形节点代表**极小过程**），以此类推。

所谓剪枝，就是目前遍历到此，发现继续遍历子节点都不会更新这个节点的估值了，此时就可以剪枝。注意，判定是否剪枝的时候是要和**当前节点的所有祖先**都进行比较的，不能只比较父节点。

不过要注意，在最后给出根节点估值后确定最后决策的时候，**只能沿着决策树走一步**。这是因为你做出决策后决策树可能会改变形态，不能保证后面的决策的估值情况还保证如此。

### 蒙特卡罗方法

$\alpha-\beta$ 剪枝依赖于大量的专家知识，所以在实际应用上也有所局限。其基本的思路是在给定的时限之内不断通过模拟对弈的方式扩展搜索树，最后再给定走步方式。

基本的循环包括以下几个部分：

- 根据前期评估结果选取最有希望的叶子节点
- 在该叶子节点处随机进行一步，扩展出新叶子节点
- 从新叶子节点开始随机模拟对弈直到判定出胜负
- 根据模拟的结果反向更新决策树上各个节点的评估值

一般而言，节点的评估值包含两个方面：

- 当前信息下节点是有希望的
- 深度较浅的节点具有更大的探索可能

一般而言可以选择这样的评估方式：

$$
I\_j = \overline{X\_j} + c\sqrt{\frac{2\ln n}{T\_j(n)}}
$$

这里 $\overline{X\_j}$ 表示经过当前节点的决策路径的胜率，这是一个守成的选项。

$n$ 表示当前搜索总次数， $T\_j(n)$ 表示当前节点访问次数。

## 高级搜索

### 局部搜索

局部搜索的意思就是一直往更好的地方走，具体过程为：

- 选择初始点 $x\_0$ ，以及其相邻可考察点集 $P$ ；
- 只要不满足退出条件，进行下面循环：
  - 选择 $P' \subset P$ ，取 $P'$ 之中最优解 $x\_b$ ；
  - 如果 $x\_b$ 更优，则切换为考察 $x\_b$ ， $P$ 修改为 $x\_b$ 的相邻考察点集；
  - 否则 $P = P - P'$ ；
- 输出结果。

### 模拟退火算法

模拟退火实际上是对局部搜索的一个优化。其基本原理来自于液态金属凝固的时候如果降温过快则有可能由于分子没有充分时间排列为结晶态而导致凝固不工整（没有达到最低能状态），而实际工艺会让温度缓慢下降，并且有回升温度的可能，保证大概率凝固为结晶态。

迁移到局部搜索上，为了防止落入局部最小值，我们可以让“温度回升”是一个有概率发生的事情。对于评估函数 $E(i)$ ，以及两个状态 $p,q$ ，给出从 $p$ 迁移到 $q$ 的概率：

$$
P(p \rightarrow q) = \\begin{cases}
1 & E(q) \leq E(p) \\\\
\exp\left(\dfrac{E(p) - E(q)}{kT}\right) & E(q) > E(p) \\\\
\\end{cases}
$$

这里 $k$ 是一个常量， $T$ 是“温度”，指的是优化问题之中的控制参数。

根据物理定律（ Boltzmann 分布）实际上我们可以给出定律：

- 同一温度下，物体处于低能量状态的概率高于高能量状态；
- 温度无限高的时候，物体等概率处于任何状态；
- 温度无限低的时候，物体等概率处于任何最低能状态；
- 温度下降的时候，物体进入低能量状态概率上升，进入高能量状态概率下降。

所以我们使用模拟退火方法的时候需要尽可能保证：

- 初始能量足够高；
- 每个温度下状态交换足够充分；
- 温度的下降足够缓慢。

所以算法的基本思路就是：

- 选定初始状态；
- 随机选定初始状态的某一个相邻状态，考察它和初始状态的评估函数差：
  - 如果新状态更优，直接迁移；
  - 如果原状态更优，则按照概率迁移；
- 迁移完毕后降温，重复上述迁移步骤直到寻找到满意的状态。

这里有些细节的问题需要考量。

首先是**什么是满意的状态**，一般而言最简单的就是设定温度阈值或者降温次数阈值，另外一个是如果多次降温都没有能够让评估函数的变化超过阈值就可以认为稳定了。

其次是**什么时机降温**，上述算法框架之中每次迁移完毕就会降温，但实际上有的时候会进行若干次迁移后才会降温。一般而言我们可以设定迁移次数阈值。

之后是**怎么降温**。等比例降温是最简单的，另外还有一个较为常用的：

$$
t\_{k + 1} = \frac{t\_k}{1 + \frac{t\_k\ln(1 + \delta)}{3\sigma\_{t\_k}}}
$$

最后是**初始温度如何设定**。一般而言较高就可以了。

### 遗传算法

遗传算法的本质就是模拟生物进化的过程，通过引入交叉、变异等干扰因素尝试在若干代迭代后获得满意的结果。不过我们首先关注最为重要的**选优**过程的模拟。

考虑一个包含 $N$ 个个体的群体，其中第 $i$ 个个体的适应值为 $F(x\_i)$ ，那么在进化过程中其被选中的概率为：

$$
p(x\_i) = \frac{F(x\_i)}{\sum\_{j = 1}^N F(x\_j)}
$$

之后就可以模拟选优，过程为：

- 从 $x\_1$ 开始，以 $p(x\_1)$ 的概率选择 $x\_1$ 。若选中，结束模拟，否则继续；
- 转到 $x\_2$ ，以 $p(x\_1) + p(x\_2)$ 的概率选择 $x\_2$ 。若选中，结束模拟，否则继续；
- ......
- 结束模拟

这是选出一个染色体的方式，下面介绍如何选出一个群体：

对于每一个个体 $x\_i$ ，我们用上述方法在 $N$ 个个体之中选取 $\lfloor p(x\_i)N\rfloor$ 次。之后按照 $p(x\_i)N - \lfloor p(x\_i)N\rfloor$ 从大到小排序群体，再取若干个让选出的群体恰好有 $N$ 个个体。

这种方法就是模拟了群体的一次进化，高适应的个体就有高可能得到繁殖（被多次取到）。

另外，交叉和变异则是对表示状态的二进制（或者十进制）数字串进行一定处理。

从而就有了遗传算法的基本框架：

- 给定群体规模 $N$ ，交叉概率 $p\_c$ 以及变异概率 $p\_m$ ；
- 随机生成 $N$ 个染色体作为初始群体，并计算适应值；
- 下面不断重复循环直到找到合适的解：
  - 选优出 $N$ 个染色体成为扩展群体；
  - 按照 $p\_c,p\_m$ 进行交叉和变异，未变化的染色体保留，形成新群体；
- 选取整个进化过程中最适应的染色体作为最后输出。

不过我们注意，我们一定要**选取合适的编码方式**，否则可能会因为问题的状态难以描述而导致编码串有过多无用位，从而导致无效变异。

二进制的交叉和变异是简单的，十进制的交叉可以如此：

- 子代 1 的交叉位之后的基因从父代 2 的所有基因之中按顺序取出尚未在子代 1 之中基因；
- 指定若干的欠缺位，让父代 2 这些位置留空，之后按照父代1的顺序把原来的数字填回去形成子代 2 ；
- 指定一个一一对应的映射，以生成子代。

变异则可以：

- 指定两个位置，将后面的数字移到前面的指定位置之前；
- 指定两个位置，交换两个位置上的基因；
- 随意打乱某一区间。

## 统计机器学习

### 基本概念

现实中的预测问题都可以归纳为在函数空间 $H = \\{f \mid f: X \rightarrow Y\\}$ 之中寻找最优的预测函数。这里 $X$ 是输入集，其中的元素一般表示预测的基础（已知信息）。而 $Y$ 是输出集，其中的元素一般表示需要预测的结果。

我们假设 $f \in H$ 为最优函数，也就是我们要寻找的。一般这个函数不会已知，但我们可以知道若干对输入输出组 $(x\_i, y\_i)$ 满足 $f(x\_i) = y\_i \pm {\rm noise}$ （ ${\rm noise}$ 指的是可能出现的数据扰动）。这些输入输出组构成**训练集**。

统计机器学习的目标就是根据训练集，按照某种算法尝试找一个 $g \in H$ ，让 $g$ 尽可能表现类似 $f$ 。

统计机器学习可以按照监督的介入分为：

- 监督学习
- 无监督学习
- 半 / 弱监督学习

监督学习事实上更贴近我们上述的统计机器学习定义，其又可以被分为**回归**（如线性回归、二次回归）以及**分类**。而无监督学习实际上常常被称为**聚类**。

有无监督的差别可以理解为训练集之中的输入输出对 $(x\_i, y\_i)$ 的 $y\_i$ 是否明确。如果 $y\_i$ 明确，那么这个学习就有监督，否则无监督。

下面解释为何无监督又被称为聚类。由于 $y\_i$ 是不给定的，所以我们不能很明确给出一个已知局面具体属于何种类别，但我们对于给定的两个已知局面，我们可以判断其相似性（断定两者是否大概率属于同一类别），进而将类似的输入输出对聚合在一类里面，不类似的尽可能分开，这就是**聚类（ clustering ）**。所以尽管学习目标都是寻找 $g$ 让模型的预测尽可能准确，但是两者的学习方式是不同的。

之后我们给出两个概念，就是**过拟合**以及**泛化能力**。

过拟合是机器学习要处理的一个重要问题，其描述的就是算法给出了一个相当复杂的函数 $g$ ，其在训练集上能够相当准确率地和 $f$ 类似，但是在其余输入上却不够好。泛化能力描述的是这个模型在面对未知输入的时候是否能够做出合理输出的能力。

### 朴素 Bayes 法

这个方法应用在多分类问题上。我们可以认为输入空间为若干 $n$ 维向量的集合 $\boldsymbol X \subset \mathbb R^n$ ，输出空间则是若干类别（标签）的集合 $\boldsymbol Y = \\{c\_1, c\_2, \cdots, c\_k\\}$ 。所以说输入 $X$ 是 $\boldsymbol X$ 上的随机变量，输出 $Y$ 是 $\boldsymbol Y$ 上的随机变量。考虑 $X, Y$ 的联合分布 $f(x, y)$ 。

我们得出：

$$
P(Y = c\_k \mid X = x) = \frac{
	P(X = x \mid Y = c\_k)P(Y = c\_k)
} {
	\sum\_k P(X = x \mid Y = c\_k)P(Y = c\_k)
}
$$

这个概率实际上是在给定输入 $X = x$ 的时候，输出 $Y = c\_k$ 的后验概率。所有的先验概率来源于训练集，也就是用频率近似概率。

我们确定最后的输出的方式是寻找令后验概率最大的 $c\_k$ 。由于上述分母保持常数，所以输出：

$$
y = \arg\max\_{c\_k} P(X = x \mid Y = c\_k)P(Y = c\_k)
$$

但是我们这里要注意到先验概率之中的 $P(X = x \mid Y = c\_k)$ 项，其复杂度随着 $n$ 的上升而指数上升。但是我们可以假设 $X$ 的各个维度是独立的，所以我们可以得到：

$$
\\begin{aligned}
P(X = x \mid Y = c\_k) &= P\left(X^{(1)} = x^{(1)}, X^{(2)} = x^{(2)}, \cdots, X^{(n)} = x^{(n)} \ \middle| \  Y = c\_k\right) \\\\
&= \prod\_{j = 1}^n P\left(X^{(j)} = x^{(j)} \ \middle| \ Y = c\_k\right)
\\end{aligned}
$$

最终给出的 **Bayes 分类器**为：
$$
y = \arg\max\_{c\_k} P(Y = c\_k)\prod\_{j = 1}^n P\left(X^{(j)} = x^{(j)} \ \middle| \ Y = c\_k\right)
$$

这里给出**平滑**的概念。也就是如果训练集中如果没有出现过某一个 case ，这个时候 case 的频率为 $0$ ，但是显然我们不能把概率估计为 $0$ ，这个时候会引入平滑。这里给出一个例子：

$$
P\_\lambda\left(X^{(j)} = a\_{jl} \ \middle| \ Y = c\_k\right) = \frac{
	\sum\_{i = 1}^N {\rm idx}\left(x\_i^{(j)} = a\_{jl}, y\_i = c\_k\right) + \lambda
} {
	\sum\_{i = 1}^N {\rm idx}(y\_i = c\_k) + S\_j\lambda
}
$$

${\rm idx}$ 函数为**示性函数**，其参数为布尔表达式，为真的时候函数返回 $1$ ，否则返回 $0$ 。

这里 $S\_j$ 为 $x^{(j)}$ （输入第 $j$ 维）的取值集合 $\\{a\_{j1}, a\_{j2}, \cdots, a\_{jS\_j}\\}$ 的大小。

这里 $\lambda$ 是平滑系数，一般取 $\lambda = 1$ ，此时平滑成为 **Laplace 平滑**。

### 支持向量机（ SVM ）

SVM 是一个适用于二分类问题的计算模型。

给定线性可分训练集 $T = \\{(\boldsymbol x\_1, y\_1), (\boldsymbol x\_2, y\_2), \cdots, (\boldsymbol x\_N, y\_N)\\}$ ，这里 $\boldsymbol x\_i \in X = \mathbb R^n$ 以及 $y\_i \in Y = \\{1, -1\\}$ 。这里我们称 $1$ 为正类， $-1$ 为负类。

我们希望寻找一个超平面 $\boldsymbol w^{\*T}\boldsymbol x + b^\* = 0$ ，给定决策函数：

$$
f(\boldsymbol x) = {\rm sgn}(\boldsymbol w^{\*T}\boldsymbol x + b^\*)
$$

这就是**线性可分支持向量机**。

为了评估一个超平面 $\boldsymbol w^T\boldsymbol x + b = 0$ ，我们给定 $T$ 之中的一个样本点 $(x\_i, y\_i)$ ，定义**函数间隔**：

$$
\hat\gamma\_i = y\_i(\boldsymbol w^T\boldsymbol x\_i + b)
$$

定义**几何间隔**：

$$
\gamma\_i = y\_i\left(\frac{\boldsymbol w^T}{\\|\boldsymbol w\\|}\boldsymbol x\_i + \frac{b}{\\|\boldsymbol w\\|}\right)
$$

上述定义是针对单个样本点的，所以对于整个训练集 $T$ ，定义：

$$
\\begin{cases}
	\hat\gamma = \min\_i \hat\gamma\_i \\\\
	\gamma = \min\_i \gamma\_i \\\\
\\end{cases}
$$

这也就是训练集和超平面的函数间隔以及几何间隔。这两种间隔之间相差 $\\|\boldsymbol w\\|$ 倍。

我们选择超平面的标准就是**最大化超平面和训练集的间隔**，也就是求取 $\max\_{\boldsymbol w, b}\gamma$ 。由于 $\boldsymbol w, b$ 可以成比例缩放，所以说我们完全可以假设 $\hat\gamma = 1$ ，从而最优化问题转化为最大化 $1 / \\|\boldsymbol w\\|$ ，等价于最小化 $1 / 2\\|\boldsymbol w\\|^2$ 。所以说问题就是求解 $\min\_{\boldsymbol w, b} 1 / 2\\|\boldsymbol w\\|^2$ 。

这里由于 $\hat\gamma = 1$ ，所以说总是存在 $(\boldsymbol x\_i, y\_i)$ 满足 $y\_i(\boldsymbol w^T\boldsymbol x\_i + b) = \hat\gamma = 1$ ，这个向量就是**支持向量**。

SVM 的学习过程，可以先定义 Lagrange 函数：

$$
L(\boldsymbol w, b, \boldsymbol\alpha) = \frac{1}{2}\\|\boldsymbol w\\|^2 + \sum\_{i = 1}^N \alpha\_i[1 - y\_i(\boldsymbol w^T\boldsymbol x\_i + b)]
$$

这里 $\alpha\_i \geq 0$ ，并且 $\boldsymbol\alpha = (\alpha\_1, \alpha\_2, \cdots, \alpha\_N)^T$ 为 Lagrange 乘子向量。

我们知道：

$$
\max\_\boldsymbol\alpha L(\boldsymbol w, b, \boldsymbol\alpha) =
\\begin{cases}
	\dfrac{1}{2}\\|\boldsymbol w\\|^2 & {\rm when\ some\ requirements\ are\ satisfied} \\\\
	\infty & {\rm otherwise}
\\end{cases}
$$

所以说 $\min\_{\boldsymbol w, b}\max\_\alpha L(\boldsymbol w, b, \boldsymbol\alpha)$ 与原问题等价。

另外，我们断定：

$$
\min\_{\boldsymbol w, b} L(\boldsymbol w, b, \boldsymbol\alpha) \leq L(\boldsymbol w, b, \boldsymbol\alpha) \leq \max\_\boldsymbol\alpha L(\boldsymbol w, b, \boldsymbol\alpha)
$$

所以我们有：

$$
\max\_\boldsymbol\alpha\min\_{\boldsymbol w, b} L(\boldsymbol w, b, \boldsymbol\alpha) \leq \min\_{\boldsymbol w, b}\max\_\boldsymbol\alpha L(\boldsymbol w, b, \boldsymbol\alpha)
$$

这个等号成立的条件为 **KKT 条件**。所以我们将问题转化为求 $\max\_\boldsymbol\alpha\min\_{\boldsymbol w, b} L(\boldsymbol w, b, \boldsymbol\alpha)$ 。

我们令 $L(\boldsymbol w, b, \boldsymbol\alpha)$ 对 $\boldsymbol w, b$ 偏导为 $0$ 并带入就将问题转化为：

$$
\max\_\boldsymbol\alpha\left[-\frac{1}{2}\sum\_{i = 1}^N \sum\_{j = 1}^N \alpha\_i\alpha\_jy\_iy\_j(\boldsymbol x\_i^T\boldsymbol x\_j) + \sum\_{i = 1}^N \alpha\_i\right]
$$

这里的约束条件为：

$$
\sum\_{i = 1}^N \alpha\_iy\_i = 0\ (\alpha\_i \geq 0)
$$

我们据此获得 $\alpha^\*$ ，从而我们可以计算：

$$
\\begin{cases}
	\boldsymbol w^\* = \sum\_{i = 1}^N \alpha\_i^\*y\_i\boldsymbol x\_i \\\\
	b^\* = y\_j - \sum\_{i = 1}^N \alpha\_i^\*y\_i(\boldsymbol x\_i^T\boldsymbol x\_j)\ (\alpha\_j^\* \neq 0)
\\end{cases}
$$

但是有的时候这些点并不能被线性超平面完全分隔，所以说 $y\_i(\boldsymbol w^T\boldsymbol x\_i + b) \geq 1$ 并不能处处满足，所以需要引入**松弛变量**：

$$
y\_i(\boldsymbol w^T\boldsymbol x\_i + b) \geq 1 - \xi\_i
$$

为了尽量减小 $\xi\_i$ 的影响，所以我们可以把优化目标改为：

$$
\min\_{\boldsymbol w, b, \xi}\left(\frac{1}{2}\\|\boldsymbol w\\|^2 + C\sum\_{i = 1}^N \xi\_i\right)
$$

这种处理方式称为**软间隔最大化**，这里 $C > 0$ 为惩罚参数， $C$ 越大惩罚力度越大。

按照线性可分 SVM 的方法，问题转化为：

$$
\max\_\alpha\left[-\frac{1}{2}\sum\_{i = 1}^N \sum\_{j = 1}^N \alpha\_i\alpha\_jy\_iy\_j(\boldsymbol x\_i^T\boldsymbol x\_j) + \sum\_{i = 1}^N \alpha\_i\right]
$$

这里的约束条件为：

$$
\sum\_{i = 1}^N \alpha\_iy\_i = 0\ (0 \leq \alpha\_i \leq C)
$$

我们据此获得 $\alpha^\*$ ，从而我们可以计算：

$$
\\begin{cases}
	\boldsymbol w^\* = \sum\_{i = 1}^N \alpha\_i^\*y\_i\boldsymbol x\_i \\\\
	b^\* = y\_j - \sum\_{i = 1}^N \alpha\_i^\*y\_i(\boldsymbol x\_i^T\boldsymbol x\_j)\ (0 < \alpha\_j^\* < C)
\\end{cases}
$$

$\alpha\_i^\* > 0$ 对应的 $\boldsymbol x\_i$ 是支持向量。

- 若 $\alpha\_i^\* = 0$ ，则不是支持向量（对应曲线外侧的点）
- 若 $0 < \alpha\_i^\* < C$ ，则 $\xi\_i = 0$ ，那么 $\boldsymbol x\_i$ 在间隔边界上，是支持向量（对应两条虚线上的点）
- 若 $\alpha\_i^\* = C$ ，那么 $\boldsymbol x\_i$ 也是支持向量
  - 若 $0 < \xi\_i < 1$ ，则分类正确（对应在己方虚线和实线之间的点）
  - 若 $\xi\_i = 1$ ，则在超平面上（对应在实线上的点）
  - 若 $\xi\_i > 1$ ，则被误分（对应在实线和对方虚线之间的点）

这里给出一个结论，也就是样本点到软间隔边界（虚线边界）的距离为 $\xi\_i / \\|\boldsymbol w\\|$ 。

另外一方面，我们考虑使用非线性的方式分割数据点。事实上就是尝试建立一个非线性映射将原空间的数据点映射到新空间上，这些数据点在新空间上线性可分。

考虑使用映射 $\phi: X \rightarrow H$ 将输入 $\boldsymbol x \in X$ 映射到新空间 $H$ 上，这里把 $K(\boldsymbol x, \boldsymbol y) := \phi(\boldsymbol x)^T\phi(\boldsymbol y)$ 称为**核函数**。这个时候所有的与内积相关的运算均应该使用核函数进行。

按照线性可分 SVM 的方法，问题转化为：

$$
\max\_\alpha\left[-\frac{1}{2}\sum\_{i = 1}^N \sum\_{j = 1}^N \alpha\_i\alpha\_jy\_iy\_jK(\boldsymbol x\_i, \boldsymbol x\_j) + \sum\_{i = 1}^N \alpha\_i\right]
$$

这里的约束条件为：

$$
\sum\_{i = 1}^N \alpha\_iy\_i = 0\ (0 \leq \alpha\_i \leq C)
$$

我们据此获得 $\alpha^\*$ ，从而我们可以计算：

$$
b^\* = y\_j - \sum\_{i = 1}^N \alpha\_i^\*y\_iK(\boldsymbol x\_i, \boldsymbol x\_j)\ (0 < \alpha\_j^\* < C)
$$

原有的决策函数之中含有内积，所以也需要将其修改为：

$$
f(\boldsymbol x) = {\rm sgn}\left(\sum\_{i = 1}^N \alpha\_i^\*y\_iK(\boldsymbol x\_i, \boldsymbol x) + b^\*\right)
$$

常用的核函数包括**多项式核函数**：

$$
K(\boldsymbol x, \boldsymbol y) = (\boldsymbol x^T\boldsymbol y + 1)^p
$$

以及**高斯核函数**：

$$
K(\boldsymbol x, \boldsymbol y) = \exp\left(-\frac{\\|\boldsymbol x - \boldsymbol y\\|^2}{2\sigma^2}\right)
$$

### 决策树

我们定义随机变量 $X$ 的熵为：

$$
H(X) = -\sum\_{i = 1}^n p\_i\log p\_i
$$

当概率由训练集 $D$ 给出，该熵可以标记为 $H(D)$ 。

之后给出条件熵：

$$
H(Y \mid X) = \sum\_{i = 1}^n P(X = x\_i)H(Y \mid X = x\_i)
$$

这表示的是已经知道 $X$ 的时候 $Y$ 的不确定性。

之后定义特征 $A$ 对数据集 $D$ 的**信息增益**为：

$$
g(D, A) = H(D) - H(D \mid A)
$$

这实际上表示的是给定特征 $A$ 的时候数据集 $D$ 不确定性减少的程度。

假设有训练集 $D$ ，有 $K$ 个类 $C\_k$ ，特征 $A$ 有 $n$ 个取值 $a\_i$ ，特征 $A$ 的不同取值将 $D$ 划分为 $n$ 个子集 $D\_i$ ，记 $D\_i$ 中属于 $C\_k$ 类的样本集合为 $D\_{ik}$ ，那么：

$$
\\begin{aligned}
	g(D, A) &= H(D) - H(D \mid A) \\\\
	&= -\sum\_{k = 1}^K \frac{|C\_k|}{|D|}\log\frac{|C\_k|}{|D|} + \sum\_{i = 1}^n \frac{|D\_i|}{|D|}\left(\sum\_{k = 1}^K \frac{|D\_{ik}|}{|D\_i|}\log\frac{|D\_{ik}|}{|D\_i|}\right)
\\end{aligned}
$$

下面介绍 ID3 算法，这个算法可以用于生成决策树：

- 如果 $D$ 之中所有例子属于同一类或者没有用于判断的特征，则返回单节点树，类标记为实例数最多的类；
- 选择信息增益最大的特征 $A\_g$ ，其信息增益为 $\delta\_A$ ；
- 如果 $\delta\_A$ 小于阈值 $\varepsilon$ ，那么置单节点决策树，类标记为实例数最多的类；
- 否则按照特征取值分割训练集，如果某个分割后的块为空，那么构建单节点子树，类标记为 $D$ 实例数最多的类。若非空则构建子树，递归上述过程。

这样的算法倾向于选择分支比较多的属性。

所以定义**信息增益比**：

$$
g\_R(D, A) = \frac{g(D, A)}{H\_A(D)}
$$

将 ID3 算法之中的信息增益换成信息增益比则得到 C4.5 算法。

这种方法生成的决策树可能产生过拟合，所以需要一定程度上的剪枝。剪枝的基本流程在于找到一个父节点，剪取其下一代后将其作为新的叶子节点，其类型标记为其原来对应的子树中量最大的类。

在数据量足够大的时候，可以使用验证集进行剪枝，也就是不断使用验证集剪枝直到性能下降。如果数据量不够，则使用训练集，从下往上缩节点，直到损失函数回升。损失函数定义：

$$
C\_a(T) = \sum\_{t = 1}^{|T|}N\_tH\_t(T) + a|T|
$$

这里经验熵定义为：

$$
H\_t(T) = -\sum\_k \frac{N\_{tk}}{N\_t}\log\frac{N\_{tk}}{N\_t}
$$

这里某一个节点 $t$ 的样本数为 $N\_t$ ，其中 $k$ 类的样本数为 $N\_{tk}$ 。

## 神经网络与深度学习

### 基本神经元结构

一个神经网络中的神经元一般接受多个输出并产生一个输出，一般而言其数学表达为：

$$
y = g\left(\sum\_{i = 1}^n w\_ix\_i + b\right)
$$

这里函数 $g$ 一般是非线性的，称为**激活函数**。激活函数常见的有 sigmoid 等。

### 反向传播算法（ BP ）

训练一个多层神经网络可以分为两步，首先是正向从输入计算出输出，和标准输出对比，算出损失函数（衡量实际输出和标准输出的差别），这是**正向传播**。之后就是根据差别的大小，计算出网络中各个权重对最终输出的偏导数，从而更新权重值，这是**反向传播**。

一般而言我们常常使用均方误差评价输出，也就是说对于某一层神经元以及某一个样本 $d$ ，标记其中第 $j$ 个神经元的实际输出为 $o\_j$ ，理想输出为 $t\_j$ ，那么损失函数为：

$$
E\_d(\boldsymbol w) = \frac{1}{2}\sum\_j (t\_j - o\_j)^2
$$

根据损失函数，权重 $w\_{ji}$ （第 $j$ 个神经元对第 $i$ 个输入的权重）的更新量为：

$$
\Delta w\_{ji} = -\eta\frac{\partial E\_d}{\partial w\_{ji}}
$$

这里 $0 < \eta < 1$ 称为**学习率**。

我们设置一个中间量：

$$
n\_j := \sum\_iw\_{ji}x\_{ji} + b\_j
$$

也就是未激活的神经元输出。那么我们知道：

$$
\frac{\partial E\_d}{\partial w\_{ji}} = \frac{\partial E\_d}{\partial n\_j}\frac{\partial n\_j}{\partial w\_{ji}} = \frac{\partial E\_d}{\partial n\_j}x\_{ji}
$$

下面我们对输出层和隐含层分别计算损失函数对未激活输出的偏导数。

如果该层为输出层，那么我们进一步展开：

$$
\frac{\partial E\_d}{\partial n\_j} = \frac{\partial E\_d}{\partial o\_j}\frac{\partial o\_j}{\partial n\_j} = \frac{\partial}{\partial o\_j}\left(\frac{1}{2}\sum\_k(t\_k - o\_k)^2\right)\frac{\partial \sigma(n\_j)}{\partial n\_j} = -(t\_j - o\_j)o\_j(1-o\_j)
$$

这里默认激活函数为 sigmoid 函数。

如果该层为隐藏层，我们记其下游层的神经元构成集合 $D$ 。作如下展开：

$$
\\begin{aligned}
\frac{\partial E\_d}{\partial n\_j} &= \sum\_{k \in D}\frac{\partial E\_d}{\partial n\_k}\frac{\partial n\_k}{\partial o\_j}\frac{\partial o\_j}{\partial n\_j} \\\\
&= \sum\_{k \in D}\frac{\partial E\_d}{\partial n\_k}\frac{\partial}{\partial o\_j}\left(\sum\_l w\_{kj}o\_j + b\right)\frac{\partial\sigma(n\_j)}{\partial n\_j} \\\\
&= \sum\_{k \in D}\frac{\partial E\_d}{\partial n\_k}w\_{kj}o\_j(1 - o\_j) \\\\
&= o\_j(1 - o\_j)\sum\_{k \in D}\frac{\partial E\_d}{\partial n\_k}w\_{kj}
\\end{aligned}
$$

这里还有一项偏导数实则可以继续按照上述的方式递推计算，直到计算到输出层。

这里额外提一下，均方误差只是一个选择，在分类问题中，更常用的是**交叉熵误差**：

$$
H\_\boldsymbol t(\boldsymbol o) = -\sum\_i t\_i\log(o\_i)
$$

### 过拟合问题与正则化

过拟合问题在之前有过说明，在深度学习之中减少过拟合的一个方法就是在损失函数之中加入正则项：

$$
E\_d(\boldsymbol w) = \frac{1}{2}\sum\_k(t\_k - o\_k)^2 + \\|\boldsymbol w\\|
$$

另外还有引入 Dropout 以及引入验证集的方式，这里均不展开。

### 卷积神经网络（ CNN ）

`TODO`

### 循环神经网络（ RNN ）

`TODO`