---
title: 2023 CMO Day2 P5 几何试题解答
date: 2023-11-30 04:09:38
mathjax: true
category:
    - 【杂谈】数学
---

本来应该干活的，结果发现 CMO 题出来了，无聊就来做了一下。

<!-- more -->

# Problem

已知锐角三角形 $\triangle ABC$，在 $BC$ 延长线上有点 $K$，过 $K$ 分别作 $AB, AC$ 的平行线 $KP, KQ$ 令 $BK = BP, CK = CQ$。$\triangle KPQ$ 的外接圆与 $AK$ 相较于点 $T$。证明：

1. $\angle BTC + \angle APB = \angle CQA$
2. $AP\cdot BT\cdot CQ = AQ\cdot CT\cdot BP$

# Analysis & Solution

总之先画图：

<image src="/uploads/cmo-geo/1.png" style="zoom:50%;" />

总之立刻从 $P, Q$ 是对应点发现，第一个要证明的角度关系应该改写为 $\angle BTC = \angle CQA - \angle APB$。之后，很显然就要去想如何去转化 $\angle CQA$。这里有个初中数学就知道的东西，等腰三角形 $CQK$，然后还有 $CA \parallel QK$，很快就有 $CA$ 平分 $\angle BCQ$。这个平分有个相当大的作用，就是我可以把 $Q$ 给对称下来了，同理 $P$ 也可以：

<image src="/uploads/cmo-geo/2.png" style="zoom:50%;" />

然后这里就得到了 $\angle CQA - \angle APB = \angle CDA - \angle AEB = \angle DAE$，我们要证明的关系就从三个角变成了两个角。再来考虑 $D, E$ 的特点，不难根据刚刚的相等发现 $B$ 是 $KE$ 的中点，$C$ 是 $KD$ 的中点，那么这么多中点其实就给了我们把 $\angle DAE$ “拉到”图形里面的一个思路。找 $KA$ 的中点 $M$，立刻就有 $\angle DAE = \angle BMC$。这个时候我们要证明的，其实就是 $B, C, T, M$ 四点共圆：

<image src="/uploads/cmo-geo/3.png" style="zoom:60%;" />

到这一步，找这个共圆就几乎不能走角度的路线了，因为好用的好描述的角基本都用完了，所以该去找线段关系了，显然我们要用圆幂定理，不难发现我们应该尝试去证明 $KM\cdot KT = KB\cdot KC$，也就是 $KA\cdot KT = 2KB\cdot KC$。比较麻烦的是 $KT$，因为这条线段最难描述。然而思考 $T$ 的成因，不难发现 $AK$ 是 $\triangle KPQ$ 的外接圆的截线，其实可以先在 $\triangle KPQ$ 的外接圆上搞一步圆幂去把 $T$ 消掉。

要去思考 $A$ 对 $\triangle KPQ$ 的外接圆的幂，其实最直接的想法就是去找 $\triangle KPQ$ 的外心，然而不难发现这个外心真的是位置太好了，这玩意就是 $A$ 关于 $\triangle ABC$ 外心的对称点。

<image src="/uploads/cmo-geo/4.png" style="zoom:60%;" />

那么很快啊，我们就能写出来 $AT\cdot AK = AO^2 - OK^2$。要证明 $KA\cdot KT = 2KB\cdot KC$，我们只要证明两个式子加起来是成立的，就是只要证明 $AT\cdot AK + KA\cdot KT = AO^2 - OK^2 + 2KB\cdot KC$，即：

$$
AK^2 = AO^2 - OK^2 + 2KB\cdot KC
$$

进一步发现 $KB\cdot KC$ 是 $K$ 对 $\triangle ABC$ 的外接圆的幂，就是 $KR^2 - RA^2$，所以我们只要证明：

$$
AK^2 = AO^2 - OK^2 + 2(KR^2 - RA^2)
$$

而上式在 $\triangle AOK$ 中利用中线长公式显然成立，从而第一问证明完毕。

---

第二问实际上在第一问基础上已经没东西了，我们直接考虑刚刚的四点共圆，这至少暗示了两对三角形相似，也就是 $\triangle CKT \sim \triangle MKB$ 以及 $\triangle MKC \sim \triangle BKT$。这表明：

$$
\frac{CT}{CK} = \frac{BM}{MK}, \frac{BT}{BK} = \frac{CM}{MK}
$$

两个式子除一下：

$$
\frac{CT}{BT}\cdot\frac{BP}{CQ} = \frac{CT}{BT}\cdot\frac{BK}{CK} = \frac{BM}{CM} = \frac{AE}{AD} = \frac{AP}{AQ}
$$

这就证明完毕了。

# Comment

感觉，不难，除了找 $M$ 这个中间点把 $\angle EAD$ 搞进去这一步想了一段时间之外就没怎么卡过。而且看到有两问的题目我直接先把第一问中间结论不证明，拿过来证明第二问，发现是平凡的之后这道题就索然无味了。

没用到什么复数、三角、反演等 fancy 的东西，感觉不如，联赛二试。