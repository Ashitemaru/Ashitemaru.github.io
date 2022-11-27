---
title: JS 文件本地化
date: 2022-08-20 01:35:54
category:
    - 【杂谈】开发
---

自从 JSDelivr 半挂了之后就一直在为这些事情苦恼。

<!-- more -->

简而言之，一个静态站需要拉取一堆 JS、CSS 文件。而正常而言的 JS 文件完全可以从网络上当场获取，但是由于 JS CDN JSDelivr 半墙，我只能速速转战其他的 CDN，结果近日发现速度非常不理想，甚至仅仅是拉 jQuery 都花了 15 秒，这完全不能接受。

![](/uploads/blog-js-localize/1.png)

本来想尝试一下换其他的 CDN，但是怎么试都不算快。

还是暴力解决方法比较好，我直接把 jQuery 等一系列常用的东西的 min JS 打包放在了自己博客文件夹内，每次请求只不过是访问本地文件，太完美了。

虽然问题暂且是解决了，但是我还是觉得，为啥这些 CDN 会受限呢，真的和什么敏感领域有关吗。