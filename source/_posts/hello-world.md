---
title: Hello world
date: 2021-06-16 13:34:10
comments: true
mathjax: true
category:
    - 【导航】
---

刚刚建立好这个地方，暂且也没什么特别想说的，不如就来玩一下 hexo 的各个语法吧。

<!-- more -->

首先玩玩代码块，自然首先试试 CPP。

{% codeblock lang:cpp C++ %}
#include <iostream>

int main() {
    std::cout << "Hello world!" << std::endl;
    return 0;
}
{% endcodeblock %}

再来试一下 Python。

{% codeblock lang:python Python %}
if __name__ == '__main__':
    print('Hello world!')
{% endcodeblock %}

不如玩一下 JavaScript。

{% codeblock lang:js JavaScript %}
console.log("Hello world!");
{% endcodeblock %}

然后玩一下数学公式。

我们可以把机器学习抽象描述为以下过程，这里 $X$ 表示输入集合，$Y$ 表示输出集合，$L$ 表示损失函数，$\boldsymbol{w}$ 表示模型参数，$f_\boldsymbol{w}$ 表示在某一系列参数之下的模型函数。

$$
\boldsymbol{w}^*=\mathop{\arg\min}_{\boldsymbol{w} \in \Omega}\sum_{x_i \in X, y_i \in Y} L(f_\boldsymbol{w}(x_i), y_i)
$$

再来试一下它自带的一些引用块。

{% blockquote %}
你成功建好了自己的第一个 hexo 站点！
{% endblockquote %}

hexo 文档里面还提到了一种奇怪的东西，来试一下。

{% pullquote %}
这个东西被称为 Pull Quote。
{% endpullquote %}

似乎还有奇怪的东西。

{% note success %}
这个是提示成功的引用块。
{% endnote %}

下面是一个居中引用。

{% cq %}
这里我们居中一定的文本。
{% endcq %}

这里还似乎提供了用于查询浏览器支持方法的插件。

{% can fetch %}

暂且玩到这里吧，目前的话想要做的一些事情包括：

- 写（搬运）点有点价值的东西

- 有没有可能做一些类似于管理员才可访问的东西呢

- 搞一下评论系统、浏览计数之类的

当然我们知道明天下午就考数电了，所以 Holder 该滚去复习了。