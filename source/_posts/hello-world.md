---
title: Hello world
date: 2021-06-16 13:34:10
comments: true
mathjax: true
---

刚刚建立好这个地方，暂且也没什么特别想说的，不如就来玩一下 hexo 的各个语法吧。

<!-- more -->

首先玩玩代码块，自然首先试试 cpp 。

{% codeblock lang:cpp %}
#include <iostream>

int main() {
    std::cout << "Hello world!" << std::endl;
    return 0;
}
{% endcodeblock %}

再来试一下 Python 。

{% codeblock lang:python %}
if __name__ == '__main__':
    print('Hello world!')
{% endcodeblock %}

不如玩一下 JavaScript 。

{% codeblock lang:js %}
console.log("Hello world!");
{% endcodeblock %}

然后玩一下数学公式。

我们可以把机器学习抽象描述为以下过程，这里$X$表示输入集合，$Y$表示输出集合，$L$表示损失函数，$\boldsymbol{w}$表示模型参数，$f_\boldsymbol{w}$表示在某一系列参数之下的模型函数。

$$
\boldsymbol{w}^*=\mathop{\arg\min}_{\boldsymbol{w} \in \Omega}\sum_{x_i \in X, y_i \in Y} L(f_\boldsymbol{w}(x_i), y_i)
$$
