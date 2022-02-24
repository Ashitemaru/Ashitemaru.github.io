---
title: Pytorch3D 框架摸索实录
date: 2022-02-13 21:57:35
category:
    - 科研杂谈
---

可微渲染还是会和机器学习或多或少有一些关联的，但是由于其涉及到三维数据，所以不能直接使用 Pytorch 框架而是使用可以处理三维数据的 Pytorch3D 框架。

这个框架用的人比较少，网上可以参考的资料也鲜有中文的。这篇文章基于 Pytorch3D 的官方 API 文档以及 [YouTube 的相关教程视频](https://www.youtube.com/watch?v=MOBAJb5nJRI)，也算是记录自己摸索这个被称为有许多坑的框架的过程。

<!-- more -->

## CUDA 的问题

我的笔记本显卡是 RTX 3050 ，使用不了 CUDA 10 ，但是 Pytorch3D 官方给出的安装指引是基于 CUDA 10 的，所以我们要自己安装合适版本的 Pytorch 以及 Pytorch3D 。

主要需要注意的就是要装 CUDA 11 ，然后 Pytorch 一定不能是 CPU only 的，最好是 `0.10.1+cu113` 这种明确显示支持 CUDA 的版本。最后就是，根据 StackOverflow 的一些回答，从 pip 或者 Anaconda 安装的 Pytorch3D 似乎是支持不了 GPU 的，要自己 clone 下来然后 build from source ，然后在 Windows 上安装的话一定要有 MSVC 。

总之这个配环境是真的折磨，而且其实到现在我也没有敢说后面就一定不会在环境上出问题。