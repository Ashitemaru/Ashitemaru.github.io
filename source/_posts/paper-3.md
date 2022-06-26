---
title: 《Comyco - Quality-Aware Adaptive Video Streaming via Imitation Learning》论文笔记
date: 2022-06-22 15:27:15
category:
    - 科研杂谈
mathjax: true
---

黄天驰学长的论文。话说和他交流下来觉得这位学长好有意思，论文写得阅读起来毫无障碍，之前听他组会讲他的工作 motivation 也感觉很贴地。

据他说，这篇文章会比较容易懂一些，总之先看。

<!-- more -->

# Introduction

这篇工作的 motivation 依然是考虑到 buffer-based ABR 难以在波动的网络条件下使用（buffer-based ABR 是基于 pre-assumption 的），learning-based ABR 可能并没有考虑完全环境参数，导致可能依然不能满足实际需求的缺陷。

本文主要尝试去解决的 learning-based ABR 的问题包括：
