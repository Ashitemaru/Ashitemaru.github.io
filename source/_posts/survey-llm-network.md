---
title: LLM 训练策略与网络通信交叉领域调研
date: 2025-04-17 15:30:59
category:
    - 【论文笔记】计算机
mathjax: true
---

大概也是准备好自己起炉灶了，所以还是得广泛地调研各种论文和工作方向。

<!-- more -->

# LLM 训练调度算法

这个方向主要聚焦于如何调度多卡多机，从 high-level 的角度思考如何合理安排资源和设定并行。现在也聚焦于如何把这个方向相对地拆解为若干子方向，这样也方便后续调研和对接可能的需求（zhipu 存在一些？）。

## AdaPipe: Optimizing Pipeline Parallelism with Adaptive Recomputation and Partitioning (EuroSys '24)

## MuxFlow - efficient GPU sharing in production-level clusters with more than 10000 GPUs

## LoongServe - Efficiently Serving Long-Context Large Language Models with Elastic Sequence Parallelism

# 将流媒体技术应用到 LLM 框架

这是一种相对直接且容易想到的交叉方向，代表的组可能是 Juncheng Jiang 组。

## RAGServe - Fast Quality-Aware RAG Systems with Configuration Adaptation

## CacheBlend - Fast Large Language Model Serving for RAG with Cached Knowledge Fusion

