---
title: 强化学习入门学习笔记
date: 2022-06-30 20:44:56
category:
    - 【学习笔记】计算机
mathjax: true
---

目前实验室的项目和强化学习关系比较大，然而自己确实没有什么强化学习的基础。

实验室的学长似乎有一个不错的强化学习入门学习仓库，感觉可以照着这个慢慢学来。

$$
\newcommand{\b}{\boldsymbol}
\newcommand{\argmax}{\mathop{\rm argmax}}
$$

<!-- more -->

本笔记也有参考代码 Repo，虽然大部分是从别人那边搬过来的，但是我自己还是稍微做了一些调整，增加了一些可读性然后修复了一些 deprecated 的问题。

代码 Repo 的地址：[Reinforce learning code repository](https://github.com/Ashitemaru/RL-tutorial)。

# 强化学习的基本概念

强化学习和常见的监督学习有一定的类似，但是也有区别。监督学习我们给出的监督往往是数据集上的标签，但是强化学习我们能给出的提示是给机器的行为作出评分。即，在强化学习中，我们会有一个不断给机器的行为评分的监督者，我们希望机器能够通过这个评分监督者学习到如何作出高分行为。

监督学习的标签往往需要事先由人类标注出来，也就是说监督学习学习的是人类标签的模式，但是强化学习不一定需要事先标注，他需要学习我们给出的评分策略的模式。

## 强化学习方法的分类

根据强化学习方法是否需要理解环境，可以将强化学习算法分为 model-based 和 model-free 两类。

model-free 方法不需要机器去理解环境，他每一步决策都需要等待真实环境的反馈才能进行下一步。而 model-based 方法可以通过 model 来模拟现实环境，这样机器可以通过这个模拟现实环境的 model 来预测自己的行为对环境的影响，甚至分析行为不同分支的影响，从而做到对现实环境的理解。

model-free 方法包括 Q learning、Sarsa、Policy gradients。

---

根据强化学习方法最后选择行为的依据，可以将强化学习算法分为基于概率（policy-based）和基于价值（value-based）两类。

基于概率的强化学习算法是最自然的，其会根据其感官接收到的信息，给出其下一步各个行为的概率，然后根据这个概率随机选择下一步的行为。这些算法包括 Policy gradients。

基于价值的强化学习算法则会测算各个行动的价值，并且选择价值最高的作为自己下一步的行为。这类算法包括 Sarsa、Q learning。

基于概率的方法的一个优势在于能够根据概率分布作出连续动作的选择，而基于价值的方法不得不在选择一步动作后才能作出下一步动作的价值评估。

当然现在也有将两者结合起来的算法，即 Actor-Critic 方法，这个方法内 Actor 会基于概率给出动作，而 Critic 会给出价值评估，这样的话能够在 Policy gradients 的基础上加快训练速度。

---

根据其更新参数的策略，可以将强化学习算法分为回合制更新（Monte-Carlo update）和单步更新（temporal difference update）。

Monte-Carlo learning 和基础版本的 Policy gradients 方法都是回合制更新的，然而 Q learning、Sarsa 和升级版本的 Policy gradients 都会基于更为现实和有效的单步更新。

---

另外还有在线算法（on policy）和离线算法（off policy）的区别。离线算法的优势就是我可以在脱离交互环境的条件下，学习先前的交互记录。而在线算法必须要在交互环境内更新参数。

最典型的在线算法是 Sarsa 和强化过后的 Sarsa lambda。而典型的离线算法为 Q learning 和强化过的 Deep Q network。

# Q learning

首先引入 Q 表这个概念，Q 表可以看作一个函数 $Q(s, a)$，这个函数值的含义为在状态 $s$ 的条件下，作出行为 $a$ 的潜在收益。而决策过程就是选择当前状态的所有可选行为中潜在收益最高的行为。

Q 表的更新是在进行决策之后开始的，如果我们原先在状态 $s$，并且我们通过行为 $a$ 到达新状态 $s'$，这个时候我们需要重新估算 $Q(s, a)$ 的值。这个估计值新增的部分应当包含两个部分，即我们采取这个行为立刻能得到的奖励和我们在 $s'$ 状态处能够达到的最大可能收益。

我们的更新策略如下：

$$
Q'(s, a) := Q(s, a) + \alpha\left[r + \gamma\max_{a' \in \Gamma(s')} Q(s', a') - Q(s, a)\right]
$$

这里 $\Gamma(s')$ 表示在状态 $s'$ 处能够选择的所有行为构成的集合，$\alpha$ 表示**学习率**。

这样我们就能看到调整估计值的逻辑。我们首先获取在新状态 $s'$ 处所有行为可能得到的最大收益 $\max_{a' \in \Gamma(s')} Q(s', a')$，乘以一个衰减 $\gamma$，之后加上采取行为 $a$ 后立刻能得到的收益 $r$ 计算出行为 $a$ 的潜在收益。之后我们将这个估计值和原先的值作差得到差距，乘以学习率 $\alpha$ 之后就得到了我们需要更新到 $Q(s, a)$ 上的增量。

实际上的 Q learning 在作出决策的时候其实也不一定完全按照最高价值选择，而是可能通过 epsilon greedy 的策略。即提前设定一个 $\varepsilon \in (0, 1)$，以 $\varepsilon$ 概率按照 Q 表最优价值选择行为，以 $1 - \varepsilon$ 概率随机选择行为。这里 $\varepsilon$ 也被称为**贪婪度**。

在学习过程的初期，我们希望机器能够随机探索环境，所以此时 $\varepsilon$ 参数会设定较小。而后期我们在已经具有较为可靠的 Q 表并且希望得到最优解的时候，就可以适当调整到较高的 $\varepsilon$。

---

如果我们分析参数 $\gamma$ 在这个学习过程中的作用，我们注意到第一步的奖励会以 $\gamma$ 衰减，而第二步的奖励会以 $\gamma^2$ 衰减，以此类推，越远的奖励衰减的次数越高。

这就说明，如果令 $\gamma = 0$，就意味着除了最近的直接奖励，其余奖励均会清空，此时机器只会关注最近的直接奖励。而如果令 $\gamma = 1$，则意味着机器会平等地对待所有可能的奖励。而一般条件下 $0 < \gamma < 1$，此时机器会按照指数衰减的方式对待从近到远的奖励。所以 $\gamma$ 也被称为**奖励衰减指数**。

## 代码示例

这里在 `${repo}/Q-learning` 下写了一个简单的通过 Q learning 学习走迷宫的小程序，这里稍微介绍一下。

其实主要关注主函数之中的主循环就可以知道 Q learning 是如何进行的了：

{% codeblock lang:python Python %}
# ${repo}/Q-learning/main.py

def update():
    for _ in range(100):
        # Initial state
        state = env.reset()

        while True:
            # Refresh the canvas
            env.render()

            # RL choose action based on state
            action = RL.choose_action(str(state))

            # RL take action and get next state and reward
            next_state, reward, done = env.step(action)

            # RL learn from this transition
            RL.learn(str(state), action, reward, str(next_state))

            # Swap state, move ahead
            state = next_state

            # Break while loop when end of this episode
            if done:
                break

    # End of game
    print("Game over")
    env.destroy()
{% endcodeblock %}

这里的 `RL` 相当于我们学习认识环境的机器，其具备的接口包括选择下一步行动（即 `RL.choose_action`）和根据环境的反馈做出学习（即 `RL.learn`）。而 `env` 则模拟了机器需要去认识的环境，其实质上是一个方形地图，地图上有两个地狱和一个代表最终结果的天堂，机器应该尝试着去学习一个绕过地狱到达天堂的路径。

其实主逻辑十分简单。我们需要训练 100 个 epoch，对于每个 epoch 我们首先初始化环境和机器所在的位置，然后在主循环中重复进行下述动作：

- 令机器选择一个行为
- 将这个行为传递给环境，令环境给出反馈（包括奖励函数、是否终止、机器即将转到的状态）
- 机器通过环境的反馈进行学习
- 令机器采取该行为

这里机器选择一个行为即我们提到的 epsilon greedy 策略，而其学习方式就是 Q 表的更新策略也已经叙述过。环境给出反馈则是简单判定机器是否走到了地狱或者天堂，并给出相应的结果。

只需要这样简单的代码，我们就可以看见一个红色方块仅依靠着环境的反馈学习到了如何登上天堂。

# Sarsa

Sarsa 和 Q learning 的策略选择方面是一致的，也就是使用 epsilon greedy 策略选择自己下一步实际的路径，但是 Sarsa 在更新 Q 表的策略上和 Q learning 是不同的。

我们观察 Q learning 的更新策略：

$$
Q'_{\rm QL}(s, a) := Q(s, a) + \alpha\left[r + \gamma{\color{red} \max_{a' \in \Gamma(s')} Q(s', a')} - Q(s, a)\right]
$$

而 Sarsa 的更新策略为：

$$
Q'_{\rm Sarsa}(s, a) := Q(s, a) + \alpha\left[r + \gamma{\color{red} Q(s', {\rm EG}(s'))} - Q(s, a)\right]
$$

这里 $a = {\rm EG}(s)$ 表示的是在状态 $s$ 处使用 epsilon greedy 策略选择行为 $a$。

除去这里更新策略的不同之外，Sarsa 在到达新状态 $s'$ 后决定此时需要采取的行动的时候，就会采取之前更新策略中的 ${\rm EG}(s')$ 直接作为下一步。

---

Sarsa 的策略意味着，其在评估新状态 $s'$ 的潜在收益的时候，并不会像 Q learning 一样贪心选择最大的可能收益，而是更为现实地选择下一步行为，并且一定会采取这一步行为。

而 Q learning 这里使用最大值贪心的更新策略也意味着其会完全沿着局部最优的路径前行，而无视可能造成的负面效果。Sarsa 则相对而言较为实际且保守。

另外，Q learning 由于可以脱离环境，所以是 off policy 的。而 Sarsa 需要边转移状态边更新 Q 表，所以是 on policy 的。

## 代码示例

这里不具体展示代码了，代码位于 `${repo}/Sarsa` 目录之下。

需要注意和 Q learning 算法不同的地方在于，在主循环里面我们除了需要保存一个 `state` 变量，我们还需要一个 `action` 变量保存即将需要采取的动作。

如果比对两种算法的效率，其实可以发现 Sarsa 算法的收敛速度明显慢于 Q learning 算法。

# Sarsa lambda

Sarsa lambda 相对于 Sarsa 的优化点在于，机器最初探索环境的时候所采取的那些行为很有可能和最终结果无关，但是在经典的 Sarsa 算法中，每一步都会以同等的权重更新 Q 表，这很有可能导致后续真正导致较高奖励函数的行为没有匹配到较高权重。

所以我们可以采取近似于回合制更新的方式取代经典的 Sarsa 单步更新策略。我们令到达最终结果的最后一步以原始权重更新 Q 表，倒数第二步需要乘以常数 $\lambda$ 再更新到 Q 表，以此类推，倒数第 $n$ 步需要以权重 $\lambda^{n - 1}$ 更新到 Q 表。

我们可以注意到 $\lambda = 0$ 的时候，只有最后一步被更新到 Q 表，这就是最经典的回合更新。而 $\lambda = 1$ 的时候就是经典的 Sarsa 单步更新。

这里的 $\lambda$ 就被称为**路径衰减指数**。

## 代码示例

这里只需要在 Sarsa 的基础上加上一个 E 表来累计每一步的 Q 表更新值即可，每走一步就需要将 E 表以 $\lambda$ 权重衰减一次。

主要观察 E 表在模型学习过程中的作用：

{% codeblock lang:python Python %}
# ${repo}/Sarsa/model.py - class SarsaLambdaTable

def learn(self, state, action, reward, next_state, next_action):
    self.check_state_exist(next_state)

    q_predict = self.q_table.loc[state, action]
    if next_state != "terminal":
        q_target = reward + self.gamma * self.q_table.loc[next_state, next_action]
    else:
        q_target = reward
        
    error = q_target - q_predict

    self.e_table.loc[state, :] *= 0
    self.e_table.loc[state, action] = 1

    self.q_table += self.lr * error * self.e_table
    self.e_table *= self.gamma * self.lambda_ # Decay
{% endcodeblock %}

前半部分和经典 Sarsa 是完全一致的，但是注意后续的两个更新。在说明具体更新之前，我们可以说明 E 表的含义，E 表实际上就是将具体值更新到 Q 表时的权重表。

首先是将当前状态对应的 E 表行清零并将当前动作对应的列设为 `1`，即更新 E 表。之后以当前权重将误差更新到 Q 表，最后对权重作出衰减。

{% note info %}
我觉得他这个代码框架存在一个问题，就是当机器走到角落的时候，实际上它的决策空间会变小，但是代码里面没有体现这一点，只是简单的让机器不动。比如机器在左上角选择向左走，那么只是简单地让机器停在左上角。

这是一个并不好的处理，因为如果此时机器还没有碰巧到达天堂产生正向激励的话，机器完全会缩在角落里来避免地狱的反向激励。

有时间给他代码改改吧。
{% endnote %}

# Deep Q Network (DQN)

在我们面对更大的解空间的时候，存储过大的 Q 表会导致内存不够使用，所以此时我们需要令 $Q(s, a)$ 使用其他的方法计算，比如说我们可以引入深度神经网络来计算 $Q(s, a)$。

这里我们可以引入这样的一个深度神经网络，该网络接受的输入是当前机器的状态 $s$，该网络给出的输出则是各种可行的行为以及其评估值，我们只需要按照最基本的 Q learning 原则根据神经网络的输出给出。

在这个给定的决策过程下，我们需要给出神经网络的训练策略。实际上我们只需要给出网络 loss 的计算方式就可以。我们思考 Q learning 之中的训练策略，我们可以得知，在经典的 Q learning 算法中我们会求出估计的 Q 值，将其与现实的 Q 值做差之后乘以学习率叠加到原先的 Q 表上。

而如果将这样的策略和神经网络联系起来，我们会发现估计的 Q 值和现实的 Q 值之间的差距实际上就表现出了 loss，我们只需要将这个 loss 对网络各个参数的梯度乘以学习率叠加到网络上就可以。

基于这样的认识，我们给出下述 loss 计算，使用平方误差的基本思想：

$$
\mathcal{L} := \left[r + \gamma\max_{a' \in \Gamma(s')} Q(s', a') - Q(s, a)\right]^2
$$

这里在状态 $s$ 下选择动作 $a$ 会转移到状态 $s'$。

---

另外，我们可以使用两种方法强化 DQN，这两种方法包括 Experience replay 和 Fixed Q targets。

前者实际上基于 Q learning 的 off policy 特性，我们可以使用以往的训练数据乃至其他机器的经验数据来训练网络。

后者的含义是使用两个同样结构但是参数不同的网络，一个使用较早的参数来给出估计的 Q 值，而且这个网络的参数几乎不会在训练过程中更新，这个网络代表了过去的经验。一个使用较新的参数来给出现实的 Q 值，这个网络会在训练过程中更新参数并且会被应用到实际场景之中。这样做的原因是取消数据的相关性和提高训练的稳定性，具体的原因后续研讨。

## 代码示例

这里我们使用 Tensorflow 实现 DQN，这里我们使用的 DQN 具体算法描述为：

- 首先初始化一个容量为 $N$ 的记忆库 $D$ 用于存放先前的经验，这里 $N$ 是先前设定的超参
- 随机初始化两个结构一致的神经网络 $Q, \hat Q$，其初始化参数分别为 $\b\theta, \b\theta^-$，初始化的时候保证 $\b\theta = \b\theta^-$
- 对模型按照下述流程训练 $M$ 个 epoch，这里 $M$ 为先前设定的超参
    - 假定初始状态 $s_1$，初始化 $s = s_1$
    - 重复下述流程直到需要中断
        - 以超参 $\varepsilon$ 使用 epsilon greedy 策略选取行为 $a := \argmax_{a'} Q(s, a'; \b\theta)$
        - 令 $r$ 表示在状态 $s$ 的条件下执行行为 $a$ 得到的直接收益
        - 令 $s'$ 表示在状态 $s$ 的条件下执行行为 $a$ 转移到的状态
        - 将描述状态转移的元组 $(s, a, r; s')$ 存入 $D$
        - 计算 loss 为 $\mathcal{L} := \left[r + \gamma\max_{a' \in \Gamma(s')} \hat Q(s', a'; \b\theta^-) - Q(s, a; \b\theta)\right]^2$
        - 反向传播 loss 的梯度，更新 $\b\theta$
        - 如果此时 $s'$ 是终止状态，则终止流程
        - 每经过 $C$ 步，令 $\b\theta^- \leftarrow \b\theta$，即令 $\hat Q$ 更新至 $Q$，这里 $C$ 是先前设定的超参
