---
title: 《Making Pre-trained Language Models Better Few-shot Learners》阅读笔记
date: 2021-07-10 08:42:58
category:
    - 计算机科学
---

咕了很长时间实验室那边，今天一定要开始做一点真正的事情。科研，先从读论文开始。

<!-- more -->

## 问题 & 解决方案概述

GPT-3 是很强大的，因为其只要通过一定的 prompt ，甚至不需要修改模型参数就可以给出相当优越的预测。但是 GPT-3 的参数量太大，实际应用可能因此受限。

这个文章之中的工作就是使用一些中等体量的模型（比如说 BERT 或者 RoBERTa ），然后使用少量数据（比如 few-shot ）做一下 finetune 来获得最终的模型。这样的模型可以在常用的硬件上进行训练，并且 few-shot 也贴合实际的应用场合。

文章里在阐述他们的解决方案的时候，叙述了他们会继续采用 **prompt-based** 的预测方法，不过他们引入了自动生成 prompt 的模型（ automatic prompt generation ）。这样的方法生成的 prompt 能和人工编写的 prompt 类似，甚至可能表现更好。

其次，他们改进了 GPT-3 的 in-context learning 范式。相较于原先较为随机地将补充内容和输入直接连接，文章中提出了一个较为有逻辑的新方法。

使用这些简单有效的方法可以让 finetuning 更为高效，文章中将这一套方法称为 LM-BFF （ better few-shot finetuning of language models ）。

## 相关工作

这里介绍了他们的工作的基础和他们所做的改进。

- 【语言模型的 prompt 】文章的工作基于 GPT-3 和 PET 的有关半监督学习的工作，但是同时改进了他们的评测系统，引入了一个更为精细的框架。

- 【自动化 prompt 查找】原先已经有部分工作尝试着将寻找 prompt 的流程自动化，但和人工选取的 prompt 相比，这些自动化寻找的 prompt 表现都不太好。但是文章提出了一系列能寻找出和人工选取表现近似的方法。

- 【语言模型的 finetune】现在对语言模型 finetune 的探索基本是在探索**更好的优化方式**和**更好的正则化**以稳定 finetune 过程。但是这篇文章的工作不聚焦于这些，其使用了一般的 finetune 方法。

- 【few-shot 方法】