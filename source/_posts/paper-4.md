---
title: 《Neural Adaptive Video Streaming with Pensieve》论文笔记
date: 2022-07-07 14:03:15
category:
    - 【论文笔记】计算机
mathjax: true
---

打算用 PyTorch 写一遍 Pensieve，也算是尝试学学实际场景之中的强化学习的应用。不过在此之前还是好好阅读一下别人的论文看一下这个算法到底在干什么。

<!-- more -->

# Introduction

现行的 ABR 算法在选择下一个视频块的码率的时候往往面临下述四个问题：

- 网络吞吐量的不稳定
- 部分 QoE 指标内存在相互矛盾的要求（如高质量和尽可能减少缓冲）
- 当前码率选择可能对未来的选择产生影响
- ABR 的决策可能是粗粒度的

现行的大部分 ABR 方法都依赖于对网络吞吐的估计或者对缓冲区大小的估计，所以其难以应对网络吞吐的不稳定。比如说 SOTA 方法 MPC 依赖于对网络吞吐量的预估，原文：

{% note info no-icon %}
However, MPC’s performance relies on an accurate model of the system dynamics - particularly, a forecast of future network throughput.
{% endnote %}