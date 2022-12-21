---
title: 毕业设计代码框架文档
date: 2022-12-21 02:08:54
category:
    - 【杂谈】开发
mathjax: true
---

今天从参考的 paper 底下白嫖到了一份开源的 Tensorflow training framework，目前希望把这一整套学习一下，做点自定义之后作为自己毕业设计的 code base。于是为了自己有迹可循，就在这里写这个代码框架的文档，迁移到哪里写到哪里。

<!-- more -->

这里基本只考虑 `src` 目录下各个 Python 模块的作用以及重要函数的文档。

# 实验脚本 `scripts`

## GrBAL 实验脚本 `scripts/run_grbal.py`

该脚本为训练 GrBAL 模型的脚本，内部主要逻辑为一个 `run_experiment` 函数，其启动实验的流程为：

- 根据 config 拼接 log 路径并初始化 logger
- 将当前实验 config 用 JSON 格式存储到 `log` 目录下

实验的 config 可以在 `if __name__ == "__main__":` 代码块下局部变量 `config` 中修改。

# 学习环境 `envs`

## 规范化环境 `envs/normalized_env.py`

该模块提供了一个规范化的环境 wrapper，将各种不同的 Gym RL 环境的数值规范化到一个范围内。

`NormalizedEnv` 构造函数接受的参数包括：

- `env`，即需要规范化的 Gym 环境
- `scale_reward`，即对收益的规范化系数 $M_r$
- `scale_action`，即对行为的规范化系数 $M_a$
- `normalize_obs`，布尔值，即是否要规范化观测（状态）
- `normalize_reward`，布尔值，即是否要规范化收益
- `obs_alpha`，即更新观测（状态）均值和方差的步长 $\alpha_o$
- `reward_alpha`，即更新收益均值和方差的步长 $\alpha_r$

首先分析行为的规范化，这里用一维动作空间为例做分析，假设规范化前的动作空间为 $\mathcal{A} = [l, r]$。这里，通过 `self.action_space` 属性获取到的规范化后的动作空间则会是 $\mathcal{A}_M = [-M_a, M_a]$，当外部的智能体根据该空间作出行为 $a \in \mathcal{A}_M$ 后，在 `self.step()` 函数中，则会做下面的映射让在规范化动作空间中的行为映射回原动作空间：

$$
f(a) = l + \frac{(a + M_a)(r - l)}{2M_a} \in \mathcal{A}
$$

注意，只有当原动作空间是 $\mathbb{R}^n$ 中的高维长方体的时候（代码上即动作空间是 `Box` 实例）才会做上述规范化。

之后讨论收益的规范化，这里收益的规范化通过 `self._apply_normalize_reward()` 方法实现。规范化收益的方式为：

$$
r \to \frac{M_r r}{\sqrt{V_r} + \lambda}
$$

这里 $V_r$ 是目前记录的收益的方差，$\lambda = 10^{-8}$ 为平滑系数。

目前记录的收益的均值和方差的维护都是简单的，初始值均值设定为 $0$，方差设定为 $1$：

$$
\begin{aligned}
\overline r &\leftarrow (1 - \alpha_r)\overline r + \alpha_r r \\
V_r &\leftarrow (1 - \alpha_r)V_r + \alpha_r(r - \overline r)^2 \\
\end{aligned}
$$

{% note info no-icon %}
这里不太懂的就是为何收益的规范化不需要减去均值，按我的理解是需要减去均值的。以及这里会比观测（状态）的规范化多乘以一个 $M_r$ 系数，这也是我不是特别明白的。
{% endnote %}

而观测（状态）的规范化也是基本类似的：

$$
o \to \frac{o - \overline o}{\sqrt{V_o} + \lambda}
$$

这里 $\overline o$ 是目前记录的观测（状态）的均值，$V_o$ 是目前记录的观测（状态）的方差，$\lambda = 10^{-8}$ 为平滑系数。

目前记录的观测（状态）的均值和方差的维护都是简单的，初始值均值设定为全 $0$ 矩阵，方差设定为全 $1$ 矩阵：

$$
\begin{aligned}
\overline o &\leftarrow (1 - \alpha_o)\overline o + \alpha_o o \\
V_o &\leftarrow (1 - \alpha_o)V_o + \alpha_o(o - \overline o)^2 \\
\end{aligned}
$$

基于上述规范化，`self.step()` 的流程就是简单的，首先将智能体决策的行为映射回原动作空间，获取到下一状态和收益后，如果需要规范化则将其规范化，并将这些数据直接返回。

# 辅助工具 `utils`

这里大概都是一些辅助工具和辅助函数。

## 序列化基类 `utils/serializable.py`

这里主要是配合日志使用，可以将模型、环境等对象序列化为日志内容。不过这个基类内部使用了很多 Python 本身的黑魔法，目前也在尝试着弄明白，现在大体知道的是 `self.quick_init()` 的作用是将构造 `self` 对象时 `self.__init__()` 所接受的参数按照顺序存放在 `self.__args, self.__kwargs` 属性内。

这个基类的使用方式是这样的：

{% codeblock lang:python Python %}
class TestClass(Serializable):
    Serializable.quick_init(self, locals())

    # Continue
{% endcodeblock %}

也就是说用 `self.quick_init()` 代替 `super(TestClass, self).__init__()` 来初始化 `self` 对象。

## 日志管理器 `utils/logger`

这个日志管理器是从 OpenAI 那边抄过来的，把里面比较有意思的部分留了下来。

### 格式化日志 `utils/logger/formats.py`

这里定义了用于格式化日志的若干管理器，包括：

- 供开发者查看的自然语言格式的 `HumanOutputFormat`
- JSON 格式的 `JSONOutputFormat`
- CSV 格式的 `CSVOutputFormat`
- Tensorboard 格式的 `TensorBoardOutputFormat`

**这里 Tensorboard 格式并没有经过充分测试，并不保证可用。**

格式化日志接受两种数据，其一是键值对数据，其二是流数据。其中仅有自然语言格式接受流数据，并且数据呈现就是将流数据原原本本传输到标准流输出或者文件中。

键值对数据在上述格式中的数据呈现为：

{% codeblock lang:text Variety %}
// In HumanOutputFormat
--------------------
| key_a | 5.5      |
| key_b | -33.3    |
--------------------

// In JSONOutputFormat
{
    "key_a": 5.5,
    "key_b": -33.3
}

// In CSVOutputFormat
key_a,key_b
5.5,-33.3
{% endcodeblock %}

### 日志管理器后端 `utils/logger/logger.py`

这里主要定义了日志管理器类 `Logger`。其最主要的功能在于管理多种输出格式的日志，对于每条接受到的数据，其都会将其输出到所有可能输出的目标。而控制输出格式的成员变量为 `self.output_formats`，其是一个数组，存放有所有可能的输出目标。

对于键值对数据，该管理器会为其构造 buffer，仅有在调用 `self.dumpkvs()` 方法的时候才会将其实际输出到文件或者标准流输出中。这样做的原因是有求取历史平均值之类的需要回溯的日志需求。

对于流数据，该管理器会直接输出到指定位置。

对于模型参数，该管理器会根据指定的设置（每次均保存、固定间隔保存、仅保存最后一次等）将模型参数存储为 `.pkl` 文件。

### 对外接口 `utils/logger/api.py`

这里定义了日志管理器所有对外的接口，包括：

- `configure`，用于初始化日志管理器，接受的参数均直接传入日志管理器
- `logkv, logkv_mean`，用于向管理器 buffer 中记录键值对数据
- `dumpkvs`，用于将所有键值对数据输出到指定位置
- `getkvs`，用于获取当前管理器 buffer 中记录的键值对数据
- `log`，用于记录流数据，最常用的日志接口
- `debug, info, warn, error`，用于以不同等级记录流数据
- `set_level`，用于设置当前流数据日志的等级
- `get_dir`，用于获取当前保存日志的目录地址
- `save_itr_params`，用于保存某一次迭代的模型参数
- `reset`，用于重置日志管理器，重置为仅向标准流输出的日志模式
- `unittest`，用于测试当前日志管理器，仅测试自然语言格式和 CSV 格式