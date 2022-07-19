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
\newcommand{\P}{\mathbb{P}}
\newcommand{\E}{\mathbb{E}}
\newcommand{\opE}{\mathop{\mathbb{E}}}
\newcommand{\relmiddle}[1]{\mathrel{}\middle#1\mathrel{}}
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

model-free 方法包括 Q Learning、Sarsa、Policy gradients。

---

根据强化学习方法最后选择行为的依据，可以将强化学习算法分为基于概率（policy-based）和基于价值（value-based）两类。

基于概率的强化学习算法是最自然的，其会根据其感官接收到的信息，给出其下一步各个行为的概率，然后根据这个概率随机选择下一步的行为。这些算法包括 Policy gradients。

基于价值的强化学习算法则会测算各个行动的价值，并且选择价值最高的作为自己下一步的行为。这类算法包括 Sarsa、Q Learning。

基于概率的方法的一个优势在于能够根据概率分布作出连续动作的选择，而基于价值的方法不得不在选择一步动作后才能作出下一步动作的价值评估。

当然现在也有将两者结合起来的算法，即 Actor-Critic 方法，这个方法内 Actor 会基于概率给出动作，而 Critic 会给出价值评估，这样的话能够在 Policy gradients 的基础上加快训练速度。

---

根据其更新参数的策略，可以将强化学习算法分为回合制更新（Monte-Carlo update）和单步更新（temporal difference update）。

Monte-Carlo learning 和基础版本的 Policy gradients 方法都是回合制更新的，然而 Q Learning、Sarsa 和升级版本的 Policy gradients 都会基于更为现实和有效的单步更新。

---

另外还有在线算法（on policy）和离线算法（off policy）的区别。离线算法的优势就是我可以在脱离交互环境的条件下，学习先前的交互记录。而在线算法必须要在交互环境内更新参数。

最典型的在线算法是 Sarsa 和强化过后的 Sarsa lambda。而典型的离线算法为 Q Learning 和强化过的 Deep Q network。

# Q Learning

首先引入 Q 表这个概念，Q 表可以看作一个函数 $Q(s, a)$，这个函数值的含义为在状态 $s$ 的条件下，作出行为 $a$ 的潜在收益。而决策过程就是选择当前状态的所有可选行为中潜在收益最高的行为。

Q 表的更新是在进行决策之后开始的，如果我们原先在状态 $s$，并且我们通过行为 $a$ 到达新状态 $s'$，这个时候我们需要重新估算 $Q(s, a)$ 的值。这个估计值新增的部分应当包含两个部分，即我们采取这个行为立刻能得到的奖励和我们在 $s'$ 状态处能够达到的最大可能收益。

我们的更新策略如下：

$$
Q'(s, a) := Q(s, a) + \alpha\left[r + \gamma\max_{a' \in \Gamma(s')} Q(s', a') - Q(s, a)\right]
$$

这里 $\Gamma(s')$ 表示在状态 $s'$ 处能够选择的所有行为构成的集合，$\alpha$ 表示**学习率**。

这样我们就能看到调整估计值的逻辑。我们首先获取在新状态 $s'$ 处所有行为可能得到的最大收益 $\max_{a' \in \Gamma(s')} Q(s', a')$，乘以一个衰减 $\gamma$，之后加上采取行为 $a$ 后立刻能得到的收益 $r$ 计算出行为 $a$ 的潜在收益。之后我们将这个估计值和原先的值作差得到差距，乘以学习率 $\alpha$ 之后就得到了我们需要更新到 $Q(s, a)$ 上的增量。

实际上的 Q Learning 在作出决策的时候其实也不一定完全按照最高价值选择，而是可能通过 epsilon greedy 的策略。即提前设定一个 $\varepsilon \in (0, 1)$，以 $\varepsilon$ 概率按照 Q 表最优价值选择行为，以 $1 - \varepsilon$ 概率随机选择行为。这里 $\varepsilon$ 也被称为**贪婪度**。

在学习过程的初期，我们希望机器能够随机探索环境，所以此时 $\varepsilon$ 参数会设定较小。而后期我们在已经具有较为可靠的 Q 表并且希望得到最优解的时候，就可以适当调整到较高的 $\varepsilon$。

---

如果我们分析参数 $\gamma$ 在这个学习过程中的作用，我们注意到第一步的奖励会以 $\gamma$ 衰减，而第二步的奖励会以 $\gamma^2$ 衰减，以此类推，越远的奖励衰减的次数越高。

这就说明，如果令 $\gamma = 0$，就意味着除了最近的直接奖励，其余奖励均会清空，此时机器只会关注最近的直接奖励。而如果令 $\gamma = 1$，则意味着机器会平等地对待所有可能的奖励。而一般条件下 $0 < \gamma < 1$，此时机器会按照指数衰减的方式对待从近到远的奖励。所以 $\gamma$ 也被称为**奖励衰减指数**。

## 代码示例

这里在 `${repo}/Q-learning` 下写了一个简单的通过 Q Learning 学习走迷宫的小程序，这里稍微介绍一下。

其实主要关注主函数之中的主循环就可以知道 Q Learning 是如何进行的了：

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

Sarsa 和 Q Learning 的策略选择方面是一致的，也就是使用 epsilon greedy 策略选择自己下一步实际的路径，但是 Sarsa 在更新 Q 表的策略上和 Q Learning 是不同的。

我们观察 Q Learning 的更新策略：

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

Sarsa 的策略意味着，其在评估新状态 $s'$ 的潜在收益的时候，并不会像 Q Learning 一样贪心选择最大的可能收益，而是更为现实地选择下一步行为，并且一定会采取这一步行为。

而 Q Learning 这里使用最大值贪心的更新策略也意味着其会完全沿着局部最优的路径前行，而无视可能造成的负面效果。Sarsa 则相对而言较为实际且保守。

另外，Q Learning 由于可以脱离环境，所以是 off policy 的。而 Sarsa 需要边转移状态边更新 Q 表，所以是 on policy 的。

## 代码示例

这里不具体展示代码了，代码位于 `${repo}/Sarsa` 目录之下。

需要注意和 Q Learning 算法不同的地方在于，在主循环里面我们除了需要保存一个 `state` 变量，我们还需要一个 `action` 变量保存即将需要采取的动作。

如果比对两种算法的效率，其实可以发现 Sarsa 算法的收敛速度明显慢于 Q Learning 算法。

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

这里我们可以引入这样的一个深度神经网络，该网络接受的输入是当前机器的状态 $s$，该网络给出的输出则是各种可行的行为以及其评估值，我们只需要按照最基本的 Q Learning 原则根据神经网络的输出给出。

在这个给定的决策过程下，我们需要给出神经网络的训练策略。实际上我们只需要给出网络 loss 的计算方式就可以。我们思考 Q Learning 之中的训练策略，我们可以得知，在经典的 Q Learning 算法中我们会求出估计的 Q 值，将其与现实的 Q 值做差之后乘以学习率叠加到原先的 Q 表上。

而如果将这样的策略和神经网络联系起来，我们会发现估计的 Q 值和现实的 Q 值之间的差距实际上就表现出了 loss，我们只需要将这个 loss 对网络各个参数的梯度乘以学习率叠加到网络上就可以。

基于这样的认识，我们给出下述 loss 计算，使用平方误差的基本思想：

$$
\mathcal{L} := \left[r + \gamma\max_{a' \in \Gamma(s')} Q(s', a') - Q(s, a)\right]^2
$$

这里在状态 $s$ 下选择动作 $a$ 会转移到状态 $s'$。

---

另外，我们可以使用两种方法强化 DQN，这两种方法包括 Experience replay 和 Fixed Q targets。

前者实际上基于 Q Learning 的 off policy 特性，我们可以使用以往的训练数据乃至其他机器的经验数据来训练网络。

后者的含义是使用两个同样结构但是参数不同的网络，一个使用较早的参数来给出估计的 Q 值，而且这个网络的参数几乎不会在训练过程中更新，这个网络代表了过去的经验。一个使用较新的参数来给出现实的 Q 值，这个网络会在训练过程中更新参数并且会被应用到实际场景之中。这样做的原因是取消数据的相关性和提高训练的稳定性，具体的原因后续研讨。

在引入了这两个强化方法之后，loss 的具体计算方式会有所不同，具体的讨论见下方。

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
        - 从 $D$ 中抽取 $B$ 组数据回放，更新 $\b\theta$，具体流程见下述，这里 $B$ 是先前设定的超参
        - 如果此时 $s'$ 是终止状态，则终止流程，否则令 $s \leftarrow s'$，转移状态
        - 每经过 $C$ 步，令 $\b\theta^- \leftarrow \b\theta$，即令 $\hat Q$ 更新至 $Q$，这里 $C$ 是先前设定的超参

阐述具体学习过程之前，我们研讨一下引入了 Experience replay 和 Fixed Q targets 之后 loss 的计算。首先由于引入了两个神经网络，计算估计 Q 值和现实 Q 值的网络参数不一致，上述已经通过 $\b\theta, \b\theta^-$ 作出区分。

另外，由于引入了记忆库 $D$ 并且需要从中随机抽取转移对，那么最后的 loss 应该是基于 $D$ 上的均匀分布的期望，所以最后的 loss 为：

$$
\mathcal{L}_{\rm DQN}(\b\theta) := \opE_{(s, a, r; s') \sim U(D)} \left[r + \gamma\max_{a' \in \Gamma(s')} \hat Q(s', a'; \b\theta^-) - Q(s, a; \b\theta)\right]^2
$$

具体的学习流程为：

- 从 $D$ 中抽取 $B$ 组转移元组
- 对每一个元组 $(s, a, r; s')$ 计算 loss
- 计算梯度将 loss 反向传播到 $\b\theta$ 参数上，完成一次参数更新
- 根据具体情况调整 epsilon greedy 参数 $\varepsilon$

这里使用的网络的数据依赖关系表现为：

![](/uploads/note-of-rl/1.png)

现在关注具体的代码实现，我们需要注意到我们采用的网络是两层全连接层：

{% codeblock lang:python Python %}
# ${repo}/DQN/dqn.py - def _build_net

self.state = tf.placeholder(tf.float32, [None, self.n_features], name = "state")
...
with tf.variable_scope("eval_net"):
    eval_layer = tf.keras.layers.Dense(
        units = 20,
        activation = tf.nn.relu,
        kernel_initializer = w_initializer,
        bias_initializer = b_initializer,
        name = "eval_layer"
    )(self.state) # Output: (None, 20)
    self.q_eval = tf.keras.layers.Dense(
        units = self.n_actions,
        kernel_initializer = w_initializer,
        bias_initializer = b_initializer,
        name = "q_eval_layer"
    )(eval_layer) # Output: (None, n_actions)
{% endcodeblock %}

其逻辑是接受一个 `(batch_size, n_features)` 形状的输入，产生一个 `(batch_size, n_actions)` 形状的输出。`n_features` 表示需要多少个特征描述一个状态 $s$，即状态空间维数。最后的输出则是各个动作的 Q 值。

在这样的网络设计之下，我们在求取 loss 的时候需要将 `(batch_size, n_actions)` 形状的输出中每一行挑出我们实际上选取的行为的 Q 值，压缩为 `(batch_size, )` 形状的 Q 值向量，再求取平方误差。所以就有下述代码：

{% codeblock lang:python Python %}
# ${repo}/DQN/dqn.py - def _build_net

with tf.variable_scope("q_target"):
    q_target = self.reward + self.gamma * tf.reduce_max(self.q_next, axis = 1, name = "q_max_next_state")
    self.q_target = tf.stop_gradient(q_target) # Shape: (None, )

with tf.variable_scope("q_eval"):
    action_indices = tf.stack([
        tf.range(tf.shape(self.action)[0], dtype = tf.int32), # Index
        self.action # The index of action
    ], axis = 1)
    self.q_eval_wrt_action = tf.gather_nd(params = self.q_eval, indices = action_indices) # Shape: (None, )

with tf.variable_scope("loss"):
    self.loss = tf.reduce_mean(tf.squared_difference(
        self.q_target, self.q_eval_wrt_action,
        name = "error"
    ))
{% endcodeblock %}

这里 `q_target` 部分只需要直接求取每一行的最大值就可以，而 `q_eval` 部分需要根据 `self.action` 压缩网络的输出，得到 `q_eval`。最后求取平方误差即可。

## DQN Enhancement

### Double DQN

上述讨论的 DQN 其实有 overestimate 的问题，因为我们每次都会使用旧参数 $\b\theta^-$，采用最大值函数去估计 evaluated Q value。为了解决这个问题，我们需要利用另外一个网络，即参数及时更新的网络来平衡旧参数网络的估计。

传统 DQN 的 loss 计算为：

$$
\mathcal{L}_{\rm DQN} := \left[r + \gamma{\color{red} \max_{a' \in \Gamma(s')} \hat Q(s', a'; \b\theta^-)} - Q(s, a; \b\theta)\right]^2
$$

这里 $a'$ 的选择仅仅依赖于 $\hat Q$ 网络的情况，我们在 Double DQN 中可以使用 $Q$ 网络来选择 $a'$ 以控制 Q 值的估计：

$$
\mathcal{L}_{\rm DDQN} := \left[r + \gamma{\color{red} \hat Q\left(s', \argmax_{a' \in \Gamma(s')} Q(s', a'; \b\theta); \b\theta^-\right)} - Q(s, a; \b\theta)\right]^2
$$

也就是说这里令 $a'$ 是 $Q$ 网络选取出的在 $s'$ 状态下的最佳行为，而非仅通过 $\hat Q$ 网络选择，这样做的平衡就可以防止 $\hat Q$ 给出过高的 Q 值。

---

这里就不给出具体的代码实现了。

`UPDATE TODO`

### Prioritized Experience Replay

如果我们的环境的 reward 分配比较不均匀，正向反馈较难获取，但是却需要要求机器重点学习正向反馈，我们原先的 replay 策略就会失效，因为在原先的策略内，我们令各个回忆的权重是一致的，这个分配并不符合实际。

我们可以这样估计每个回忆的优先级，和 loss 的计算类似，我们用估计的 Q 值直接减去现实的 Q 值，如果这个值越大，就说明还能提升的空间就越大，从而说明这个回忆的优先级越高。上述 Q 值的差值一般称为 TD error。

---

每次选取回忆的时候，我们都需要根据 TD error 给出的优先级排序各个回忆，但如果每次都排序的话显然会浪费算力，所以我们需要使用 Sum tree 数据结构组织各个回忆。

`TODO`

# Policy Gradients

Policy Gradients 的特点在于其直接输出下一步行为本身，而不是行为的某种评分。这个特点使得 Policy Gradients 可以应对决策空间无限的情况，而 Q Learning 则无法应对无限决策空间。

Policy Gradients 基于随机策略，也就是我们需要给出在某个状态 $s$ 下我们采取行为 $a$ 的概率，这种随机策略一般用参数 $\b\theta$ 建模。

我们把基于参数为 $\b\theta$ 的随机策略 $\pi_{\b\theta}$，在状态 $s$ 下，我们采取行为 $a$ 的概率表示为 $\pi_{\b\theta}(a \mid s)$。这里 $s_t, a_t$ 表示在第 $t$ 时刻的状态和行为，后续有类似的 $r_t$。

显然这样的 $\pi_{\b\theta}$ 代表了一个行为决策空间 $\mathcal{A}$ 上的一个分布。

我们首先定义累计收益：

$$
G_t(\b\tau) := \sum_{k = 0}^{+\infty} \gamma^k r_{t + k}
$$

这里 $\b\tau$ 是一个已知的转移过程，$G_t(\b\tau)$ 就表示沿着已知的转移过程 $\b\tau$，从时刻 $t$ 开始能获得的累计收益。$\gamma$ 则是先前介绍过的奖励衰减指数，我们将未来所有的收益全部相加作为总收益。

那么我们可以基于此定义出一个行为评估函数：

$$
Q^{\pi_{\b\theta}}(s, a) := \opE_{\b\tau \sim \pi_{\b\theta}} \left[G_t(\b\tau) \mid s_t = s, a_t = a\right]
$$

定义的含义也是直观的，这个评估函数的值就是随机取出一个在第 $t$ 时刻，状态和行为分别为 $s, a$ 的转移过程 $\b\tau$，求取后续能获取的收益的期望。

另外，由于我们知道给定状态 $s$ 的时候行为 $a$ 的分布就是随机策略 $\pi_{\b\theta}$ 指定的，所以我们可以定义一个不需要给定具体行为的，仅针对状态进行评估的状态函数：

$$
V^{\pi_{\b\theta}}(s) := \sum_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s)Q^{\pi_{\b\theta}}(s, a) = \opE_{\b\tau \sim \pi_{\b\theta}} \left[G_t(\b\tau) \mid s_t = s\right]
$$

此外，我们可以注意到这个随机策略实际上定义了一个在状态空间 $\mathcal{S}$ 上的 Markov chain，那么我们就有下述稳态概率的定义：

$$
d^{\pi_{\b\theta}}(s) := \lim_{t \to +\infty} \P_{\pi_{\b\theta}}(s_t = s \mid s_0)
$$

稳态概率实际上描述了在给定初始状态 $s_0$ 的条件下，使用随机策略 $\pi_{\b\theta}$ 在状态空间 $\mathcal{S}$ 上随机转移，最终到达稳定状态的时候，位于状态 $s$ 的概率。

基于稳态概率，我们就可以给一个策略 $\pi_{\b\theta}$ 定义价值：

$$
\mathcal{J}(\b\theta) := \sum_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s)V^{\pi_{\b\theta}}(s) = \sum_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s) \left(\sum_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s)Q^{\pi_{\b\theta}}(s, a)\right)
$$

为了尽可能提高策略的价值，我们采用梯度上升的方式，所以重点就是计算上述价值函数的梯度 $\nabla_{\b\theta}\mathcal{J}(\b\theta)$。

---

首先我们对 $Q^{\pi_{\b\theta}}(s, a)$ 做一些简单的展开。

我们只需要遍历 $s$ 所有可能的下一步状态 $s'$，得到其发生转移 $s \to s'$ 的概率，而相应的价值评估也会拆分为直接收益 $r$ 和接下来到达状态 $s'$ 的收益（与 Q Learning 具有类似的结构）。基于上述思路，得到下述展开：

$$
Q^{\pi_{\b\theta}}(s, a) = \sum_{s' \in \mathcal{S}} \P_{\pi_{\b\theta}}(s' \mid s, a)\left[r + V^{\pi_{\b\theta}}(s')\right]
$$

考虑到 $r, \P_{\pi_{\b\theta}}(s' \mid s, a)$ 相对于 $\b\theta$ 都是常数，可以得到：

$$
\nabla_{\b\theta}Q^{\pi_{\b\theta}}(s, a) = \sum_{s' \in \mathcal{S}} \P_{\pi_{\b\theta}}(s' \mid s, a)\nabla_{\b\theta}V^{\pi_{\b\theta}}(s')
$$

基于此，我们做出如下推理：

$$
\begin{aligned}
\nabla_{\b\theta}V^{\pi_{\b\theta}}(s) &= \nabla_{\b\theta}\left[\sum_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s)Q^{\pi_{\b\theta}}(s, a)\right] \\
&= \sum_{a \in \mathcal{A}}\left[Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s) + \pi_{\b\theta}(a \mid s)\nabla_{\b\theta}Q^{\pi_{\b\theta}}(s, a)\right] \\
&= \sum_{a \in \mathcal{A}}\left[Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s) + \pi_{\b\theta}(a \mid s)\sum_{s' \in \mathcal{S}} \P_{\pi_{\b\theta}}(s' \mid s, a)\nabla_{\b\theta}V^{\pi_{\b\theta}}(s')\right] \\
&= \sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s) + \sum_{a \in \mathcal{A}}\left[\pi_{\b\theta}(a \mid s)\sum_{s' \in \mathcal{S}} \P_{\pi_{\b\theta}}(s' \mid s, a)\nabla_{\b\theta}V^{\pi_{\b\theta}}(s')\right]
\end{aligned}
$$

这里我们观测到了 $\nabla_{\b\theta}V^{\pi_{\b\theta}}(s)$ 结构的重复，我们将其无限展开。这里每次展开留下的常数项为 $\sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s)$。

而每展开一步需要累计的线性系数为 $\pi_{\b\theta}(a \mid s)\P_{\pi_{\b\theta}}(s' \mid s, a)$，这一系数实际上代表的是在状态 $s$ 下一步转移到 $s'$ 的概率，那么其的累计就是在若干步下到达指定状态的概率。

那么上述式子无限展开后就应当为：

$$
\nabla_{\b\theta}V^{\pi_{\b\theta}}(s) = \sum_{s_\bot \in \mathcal{S}}\left[\sum_{k = 0}^{+\infty}\P_{\pi_{\b\theta}}(s \mathop{\to}^k s_\bot) \cdot \sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s_\bot, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s_\bot)\right]
$$

这里 $\P_{\pi_{\b\theta}}(s \to^k\!\! s_\bot)$ 表示在策略 $\pi_{\b\theta}$ 下，从状态 $s$ 用 $k$ 步到达终态 $s_\bot$ 的概率。

根据定义，实际上我们有：

$$
\sum_{k = 0}^{+\infty}\P_{\pi_{\b\theta}}(s \mathop{\to}^k s_\bot) = d^{\pi_{\b\theta}}(s_\bot)
$$

那么，考虑到对策略的评估实际上就是对该策略下初态价值的评估，我们得到：

$$
\begin{aligned}
\nabla_{\b\theta}\mathcal{J}(\b\theta) &= \nabla_{\b\theta} V^{\pi_{\b\theta}}(s_0) \\
&= \sum_{s_\bot \in \mathcal{S}}\left[d^{\pi_{\b\theta}}(s_\bot) \sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s_\bot, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s_\bot)\right] \\
\end{aligned}
$$

将 $s_\bot$ 替换回 $s$ 就得到 Policy gradient theorem 的公式：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \sum_{s \in \mathcal{S}}\left[d^{\pi_{\b\theta}}(s) \sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s)\right]
$$

为了让计算机便于计算，我们对上述计算式作出进一步变形：

$$
\begin{aligned}
\nabla_{\b\theta}\mathcal{J}(\b\theta) &= \sum_{s \in \mathcal{S}}\left[d^{\pi_{\b\theta}}(s) \sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\pi_{\b\theta}(a \mid s)\right] \\
&= \sum_{s \in \mathcal{S}}\left[d^{\pi_{\b\theta}}(s) \sum_{a \in \mathcal{A}}Q^{\pi_{\b\theta}}(s, a)\pi_{\b\theta}(a \mid s)\frac{\nabla_{\b\theta}\pi_{\b\theta}(a \mid s)}{\pi_{\b\theta}(a \mid s)}\right] \\
&= \sum_{s \in \mathcal{S}}\sum_{a \in \mathcal{A}} (d^{\pi_{\b\theta}}(s)\pi_{\b\theta}(a \mid s)) \cdot (Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s))
\end{aligned}
$$

容易看出 $d^{\pi_{\b\theta}}(s)\pi_{\b\theta}(a \mid s)$ 给出了 $\mathcal{S} \times \mathcal{A}$ 上的分布：

$$
\sum_{s \in \mathcal{S}}\sum_{a \in \mathcal{A}} d^{\pi_{\b\theta}}(s)\pi_{\b\theta}(a \mid s) = 1
$$

那么：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \opE_{(s, a) \sim \pi_{\b\theta}} [Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
$$

为了计算这个期望，我们可以通过 $\pi_{\b\theta}$ 确定转移过程 $\b\tau$，之后将转移过程上的所有 $(s, a)$ 代入：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
$$

将之前定义的评估函数代入：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [G_t\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
$$

## 代码示例

这里使用 Reinforce 方法实现 Policy Gradients，这是最为基本的一种实现方式，是一种回合制更新策略。

首先我们使用两层全连接层作为随机策略，其参数为 $\b\theta$，网络整体结构为：

![](/uploads/note-of-rl/2.png)

之后我们需要在随机策略 $\pi_{\b\theta}$ 指导下得到的一整个回合信息：

$$
s_1, a_1, r_1; s_2, a_2, r_2; s_3, \cdots; s_T, a_T, r_T; {\rm terminal}
$$

在将状态、行为、奖励列表传入网络之前，我们需要对奖励列表根据参数 $\gamma$ 作出衰减并累计，同时将其均值归零标准差归一，这样就完成了 reward 的规范化。

下面考虑我们之前计算的梯度：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [G_t\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
$$

这里上述序列就是一个简单的抽样，我们用该抽样近似表示期望，这样就能够得到更新为：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \frac{1}{T}\sum_{i = 1}^T [G_i \nabla_{\b\theta}\ln \pi_{\b\theta}(a_i \mid s_i)]
$$

考虑到 $G_{i}$ 对 $\b\theta$ 是常数，那么：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \nabla_{\b\theta}\frac{1}{T}\sum_{i = 1}^T [G_{i}\ln \pi_{\b\theta}(a_i \mid s_i)]
$$

梯度是容易实现的，我们只需要计算出下述式子（即目标式）：

$$
\frac{1}{T}\sum_{i = 1}^T [G_{i}\ln \pi_{\b\theta}(a_i \mid s_i)]
$$

然后将其传入优化器，交由优化器推算其梯度并附加到 $\b\theta$ 上即可。

基于这个式子，我们观察代码中 loss 的计算：

{% codeblock lang:python Python %}
# ${repo}/Policy-gradients/pg.py - def _build_net

all_action = tf.keras.layers.Dense(...)

with tf.name_scope("loss"):
    # To maximize total reward (log_p * R) is to minimize -(log_p * R)
    # And the tf only have minimize(loss)
    neg_log_prob = tf.nn.sparse_softmax_cross_entropy_with_logits(
        logits = all_action,
        labels = self.tf_action_list) # This is negative log of chosen action

    loss = tf.reduce_mean(neg_log_prob * self.tf_reward_list) # Reward guided loss
{% endcodeblock %}

这里用到了 `tf.nn.sparse_softmax_cross_entropy_with_logits` 函数，这个函数接受的参数中 `logits` 形状为 `[batch_size, n_action]`，`labels` 形状为 `[n_action]`，`labels` 参数中的项代表该 batch 给出的分类结果。返回值的形状则为 `[n_action]`。

对于每一个 batch，该函数首先将 `logits` 对应行取 softmax，并取出序号为 `labels` 参数指定的项，取其负对数作为该 batch 的结果。

比如对于某 batch，其 `logits` 为 $\{L_j\}_{1 \leq j \leq N}$，这里 $N$ 为 `n_action`，而该 batch 的 `labels` 为 $M(1 \leq M \leq N)$，那么最后对该 batch，该函数返回：

$$
R = -\ln\left(\frac{e^{L_M}}{\sum_{j = 1}^N e^{L_j}}\right)
$$

而我们也发现我们也是通过 softmax 来定义动作的概率：

{% codeblock lang:python Python %}
# ${repo}/Policy-gradients/pg.py - def _build_net

self.all_action_prob = tf.nn.softmax(all_action, name = "action_prob")
{% endcodeblock %}

也就是说：

$$
\pi_{\b\theta}(a_i \mid s_i) = \frac{e^{L_{a_i}}}{\sum_{j = 1}^N e^{L_j}}
$$

所以 `tf.nn.sparse_softmax_cross_entropy_with_logits` 的返回值实际上就是下述数列构成的向量：

$$
-\ln\pi_{\b\theta}(a_1 \mid s_1), -\ln\pi_{\b\theta}(a_2 \mid s_2), \cdots, -\ln\pi_{\b\theta}(a_i \mid s_i), \cdots, -\ln\pi_{\b\theta}(a_T \mid s_T)
$$

将这个数列和规范化后的 reward 数组逐项相乘，求取所有积的平均值就得到了目标式的相反数。这里求出其相反数的目的是通过优化器最小化负梯度，得到最大化梯度从而实现梯度上升。

这样我们就给出了 Policy gradients 的基本实现。

# Actor Critic

我们注意到 Q Learning 无法处理决策空间无限的问题，而 Policy Gradients 又仅能完成回合更新，效率低。为了结合这两者的优点，这里介绍 Actor Critic 网络。

Actor Critic 的基本思路是，利用一个基于概率的 Actor 网络随机给出行为，利用一个基于价值的 Critic 网络给出 Actor 网络行为的评价。具体而言，Actor 网络不断进行梯度上升，而 Critic 网络会判定本次上升是否合理，其会根据其学习到的规则来衰减本次 Actor 的梯度上升。

单步更新还有一个好处，如果一个完整的转移过程 $\b\tau$ 中既需要上升参数，又需要下降参数，这两个都需要细节学习的内容会因为沿着路径累计而消失，从而两者均没有学习到。

## 数学推理

这里我们聚焦于 Actor Critic 的基本数学原理，我们思考 Policy Gradients 的梯度计算：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [Q^{\pi_{\b\theta}}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
$$

我们下面将其扩展为 Actor 网络的行为。

现在由于引入了 Critic 网络，所以 Actor 的更新也变成了在线单步更新。也就是说我们在逐探索转移过程，在某一时刻到达状态 $s$，并且根据 Actor 网络 $\pi_{\b\theta}$ 选择了行为 $a$，那么此时只需要完成下述单步更新即可：

$$
\b\theta \leftarrow \b\theta + \alpha_{\b\theta}Q^{\b w}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)
$$

这里 $\alpha_{\b\theta}$ 为 Actor 网络 $\pi_{\b\theta}$ 的学习率，这里 $\b w$ 是 Critic 网络的参数。

注意到这里 $Q$ 的参数是 $\b w$ 而非 $\b\theta$，这是因为我们需要将 Actor 的评价权力交给 Critic 网络，用 Critic 的输出来控制 Actor 参数的更新。

而 Critic 的数学原理就是 Q Learning 的 TD Error 思想。观察我们对一个状态的价值评估函数：

$$
V^{\b w}(s) = \opE_{\b\tau \sim \b w} \left[G_t(\b\tau) \mid s_t = s\right]
$$

而另外一方面：

$$
G_t(\b\tau) = \sum_{k = 0}^{+\infty} \gamma^k r_{t + k + 1} = r_{t + 1} + \gamma\sum_{k = 0}^{+\infty} \gamma^k r_{t + k + 2} = r_{t + 1} + \gamma G_{t + 1}(\b\tau)
$$

所以：

$$
\begin{aligned}
V^{\b w}(s) &= \opE_{\b\tau \sim \b w} \left[G_t(\b\tau) \mid s_t = s\right] \\
&= \opE_{\b\tau \sim \b w} \left[r_{t + 1} + \gamma G_{t + 1}(\b\tau) \mid s_t = s\right] \\
&= r_{t + 1} + \gamma\opE_{\b\tau \sim \b w}\left[G_{t + 1}(\b\tau) \mid s_t = s\right] \\
&= r_{t + 1} + \gamma\opE_{\b\tau \sim \b w}\left[G_{t + 1}(\b\tau) \mid s_{t + 1} = s'\right] \\
&= r_{t + 1} + \gamma V^{\b w}(s')
\end{aligned}
$$

这里 $s'$ 为转移过程中 $s$ 的下一个状态。这说明了 $r_{t + 1} + \gamma V^{\b w}(s') - V^{\b w}(s)$ 构成了一个无偏估计，从而可以用此修正对 $V^{\b w}$ 的估计。而这里 $r_{t + 1} + \gamma V^{\b w}(s') - V^{\b w}(s)$ 就被称为 TD Error。

上述推理对 $Q^{\b w}$ 依然有效，与之对应的 $r_{t + 1} + \gamma Q^{\b w}(s', a') - Q^{\b w}(s, a)$ 也可以称为 TD Error。

从而 Critic 网络的更新策略为首先计算 TD Error：

$$
\delta_t \leftarrow r_{t + 1} + \gamma Q^{\b w}(s', a') - Q^{\b w}(s, a)
$$

之后用 TD Error 更新 Critic 网络参数：

$$
\b w \leftarrow \b w + \alpha_{\b w}\delta_t\nabla_{\b w}Q^{\b w}(s, a)
$$

这也就是最经典的 Actor Critic 算法。

# Advantage Actor Critic (A2C)

我们注意到直接让 Critic 学习 Q 值存在一个问题，那就是 Q 值均值很大，但是波动很小。在均值很大的背景下，较小的波动的分布可能被网络忽略，所以相比较于直接使用 Q 值，我们不妨设立 Baseline 函数，替换为使用 Q 值和 Baseline 的差值。这个差值就是 Advantage 函数。

我们首先需要在数学上论证引入 Baseline 并不影响无偏性，也就是说需要证明：

$$
\begin{aligned}
\nabla_{\b\theta}\mathcal{J}(\b\theta) &= \opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [Q^{\b w}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)] \\
&= \opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [(Q^{\b w}(s, a) - b(s)) \nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
\end{aligned}
$$

事实上，我们只需要令 Baseline 函数和行为 $a$ 无关，就可以保证上述关系成立。证明思路为考虑下面的式子，如下，首先展开数学期望：

$$
\begin{aligned}
&\opE_{\b\tau \sim \pi_{\b\theta}} \frac{1}{|\b\tau|} \sum_{(s, a) \in \b\tau} [b(s) \nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)] \\
=& \opE_{(s, a) \sim \pi_{\b\theta}} [b(s) \nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)] \\
=& \int_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s) \int_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s) [b(s) \nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)] {\rm d}a {\rm d}s \\
\end{aligned}
$$

根据：

$$
\pi_{\b\theta}(a \mid s) \cdot \nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s) = \nabla_{\b\theta}\pi_{\b\theta}(a \mid s)
$$

得到下述：

$$
{\rm LHS} = \int_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s) \int_{a \in \mathcal{A}} b(s) \nabla_{\b\theta} \pi_{\b\theta}(a \mid s) {\rm d}a {\rm d}s \\
$$

考虑到 $b(s)$ 和 $a$ 的无关性，继续展开得到：

$$
\begin{aligned}
{\rm LHS} &= \int_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s)b(s) \nabla_{\b\theta}\left[\int_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s) {\rm d}a\right] {\rm d}s \\
&= \int_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s)b(s) (\nabla_{\b\theta} 1) {\rm d}s \\
&= 0
\end{aligned}
$$

据此显然就能得到无偏性依然保持。

我们一般选取的 Baseline 函数就是该状态的评价函数 $V^{\b w}(s)$，所以此时我们定义 Advantage 函数：

$$
A^{\b w}(s, a) := Q^{\b w}(s, a) - V^{\b w}(s)
$$

之前我们得到过下述结论：

$$
Q^{\b w}(s, a) = \sum_{s' \in \mathcal{S}} \P_{\b w}(s' \mid s, a)\left[r + V^{\b w}(s')\right] = \opE_{s' \sim \b w} \left[r + V^{\b w}(s')\right]
$$

那么我们不妨把 Advantage 函数写为：

$$
A^{\b w}(s, a) := r + V^{\b w}(s') - V^{\b w}(s)
$$

而 Critic 就需要学习 Advantage 函数，相应的 Actor 的梯度计算更新为：

$$
\nabla_{\b\theta}\mathcal{J}(\b\theta) = \opE_{(s, a) \sim \pi_{\b\theta}} [A^{\b w}(s, a)\nabla_{\b\theta}\ln \pi_{\b\theta}(a \mid s)]
$$

## 代码示例

这里采用这样的网络结构：

![](/uploads/note-of-rl/3.png)

Critic 网络的结构很简单，其接受一个状态，输出这个状态的价值，即 Advantage 函数的作用。而其 loss 的计算即为 TD Error 的平方。这样我们训练 Critic 的方法就很明确，只需要不断在状态转移过程中计算 TD Error 后平方并将其反向传播即可：

{% codeblock lang:python Python %}
# ${repo}/Actor-Critic/ac.py - class Critic def __init__

with tf.variable_scope("critic_squared_td_error"):
    self.td_error = self.reward + self.gamma * self.next_state_value - self.state_value
    self.loss = tf.square(self.td_error)
{% endcodeblock %}

Critic 引导 Actor 的方式为将 TD Error 传递给 Actor，与梯度求积后进行反向传播：

{% codeblock lang:python Python %}
# ${repo}/Actor-Critic/ac.py - class Actor def __init__

with tf.variable_scope("action_value"):
    log_prob = tf.log(self.action_prob[0, self.action])
    # TD Error - scalar
    self.action_value = tf.reduce_mean(log_prob * self.td_error) # Advantage (TD_error) guided loss

with tf.variable_scope("actor_train"):
    self.train_op = tf.train.AdamOptimizer(lr).minimize(-self.action_value)
{% endcodeblock %}

# Deep Deterministic Policy Gradients (DDPG)

DDPG 的主要思想是将 DQN 的双网络思想借鉴到 Actor Critic 结构中。在 DDPG 中，Actor 具有两个相同结构的网络，Critic 也具有两个相同结构的网络。

在具体介绍 DDPG 之前，我们首先简单介绍 Deterministic Policy Gradients，即 DPG。这里 Deterministic 的含义就是区别于一般的 Policy Gradients 最后输出的是行为空间 $\mathcal{A}$ 上的概率分布，DPG 会直接给出某个状态下应当采取何种行为。其决策函数我们标记为：

$$
a = \mu_{\b\theta}(s)
$$

我们用符号 $\mu$ 表示确定性策略，与随机策略 $\pi$ 区分。

我们首先观察随机策略 $\pi_{\b\theta}$ 下的评价函数表达：

$$
\mathcal{J}_{\pi}(\b\theta) = \sum_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s) \left(\sum_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s)Q^{\pi_{\b\theta}}(s, a)\right)
$$

或者还可以用积分表示为：

$$
\mathcal{J}_{\pi}(\b\theta) = \int_{s \in \mathcal{S}} d^{\pi_{\b\theta}}(s) \int_{a \in \mathcal{A}} \pi_{\b\theta}(a \mid s)Q^{\pi_{\b\theta}}(s, a) {\rm d}a {\rm d}s
$$

两者实际上都表达出了同样的含义，即策略的评价函数就是 Q 值的数学期望：

$$
\mathcal{J}_{\pi}(\b\theta) = \opE_{(s, a) \sim \pi_{\b\theta}} Q^{\pi_{\b\theta}}(s, a)
$$

那么在确定性策略下，策略的评价函数应当定义为：

$$
\mathcal{J}_{\mu}(\b\theta) := \opE_{s \sim \mu_{\b\theta}} Q^{\mu_{\b\theta}}(s, \mu_{\b\theta}(s))
$$

其积分表达式为：

$$
\mathcal{J}_{\mu}(\b\theta) = \int_{s \in \mathcal{S}} d^{\mu_{\b\theta}}(s)Q^{\mu_{\b\theta}}(s, \mu_{\b\theta}(s)) {\rm d}s
$$

求和表达式类似，这里略去。

这里梯度的推理则较为简单，直接使用复合函数的导数法则即可：

$$
\begin{aligned}
\nabla_{\b\theta}\mathcal{J}_{\mu}(\b\theta) &= \int_{s \in \mathcal{S}} d^{\mu_{\b\theta}}(s) \nabla_{\b\theta}Q^{\mu_{\b\theta}}(s, \mu_{\b\theta}(s)) {\rm d}s \\
&= \int_{s \in \mathcal{S}} d^{\mu_{\b\theta}}(s) \nabla_{a} \left.Q^{\mu_{\b\theta}}(s, a)\right|_{a = \mu_{\b\theta}(s)} \nabla_{\b\theta}\mu_{\b\theta}(s) {\rm d}s \\
&= \opE_{s \sim \mu_{\b\theta}} \left[\nabla_{a} \left.Q^{\mu_{\b\theta}}(s, a)\right|_{a = \mu_{\b\theta}(s)} \nabla_{\b\theta}\mu_{\b\theta}(s)\right]
\end{aligned}
$$

基于此，我们在 DPG 的基础上引入 DQN 的双网络思路。我们记 Actor 网络中在线更新网络的参数为 $\b\theta$，目标网络的参数为 $\b\theta^-$。Critic 网络中在线更新网络的参数为 $\b w$，目标网络的参数为 $\b w^-$。初始化时应当令 $\b\theta = \b\theta^-, \b w = \b w^-$。

DDPG 的算法流程完全基于 DQN 算法，这里简单介绍如下：

- 首先初始化一个容量为 $N$ 的记忆库 $D$ 用于存放先前的经验，这里 $N$ 是先前设定的超参
- 随机初始化结构一致的 Critic 网络 $Q, \hat Q$，其初始化参数分别为 $\b\theta, \b\theta^-$，初始化的时候保证 $\b\theta = \b\theta^-$
- 随机初始化结构一致的 Actor 网络 $\mu, \hat\mu$，其初始化参数分别为 $\b w, \b w^-$，初始化的时候保证 $\b w = \b w^-$
- 对模型按照下述流程训练 $M$ 个 epoch，这里 $M$ 为先前设定的超参
    - 初始化随机数发生器 $\mathcal{N}$
    - 假定初始状态 $s_1$，初始化 $s = s_1$
    - 推演下述状态转移 $T$ 次，这里 $T$ 为先前设定的超参，下述过程的 $t$ 表示目前转移的次数
        - 使用 Actor 网络选取行为 $a_t := \mu_{\b\theta}(s_t) + \mathcal{N}(t)$
        - 令 $r_t$ 表示在状态 $s_t$ 的条件下执行行为 $a_t$ 得到的直接收益
        - 令 $s_{t + 1}$ 表示在状态 $s_t$ 的条件下执行行为 $a_t$ 转移到的状态
        - 将描述状态转移的元组 $(s_t, a_t, r_t; s_{t + 1})$ 存入 $D$
        - 从 $D$ 中抽取 $B$ 组数据，更新 $\b\theta, \b w$，具体流程见下述，这里 $B$ 是先前设定的超参
        - 令 $\b\theta^- \leftarrow \tau\b\theta + (1 - \tau)\b\theta^-$，这里 $\tau$ 是先前设定的超参
        - 令 $\b w^- \leftarrow \tau\b w + (1 - \tau)\b w^-$，这里 $\tau$ 是先前设定的超参

具体需要说明的是如何更新 $\b\theta, \b w$。首先观察 Critic 网络，其几乎完全就是 DQN 更新方式。下述为 DQN 的 loss 计算：

$$
\mathcal{L}_{\rm DQN}(\b\theta) := \opE_{(s, a, r; s') \sim U(D)} \left[r + \gamma\max_{a' \in \Gamma(s')} \hat Q(s', a'; \b\theta^-) - Q(s, a; \b\theta)\right]^2
$$

而 DDPG 中 Critic 的 loss 则为：

$$
\mathcal{L}_{\rm DDPG(Critic)}(\b w) := \opE_{(s, a, r; s') \sim U(D)} \left[r + \gamma\hat Q(s', \hat\mu_{\b\theta^-}(s'); \b w^-) - Q(s, a; \b w)\right]^2
$$

显然，仅仅是将通过贪心获取 $s'$ 状态下的下一行为修改为通过 Actor 网络来获取 $s'$ 状态下的下一行为，这样的设计使得 Critic 更为现实化。

Actor 网络的梯度则先前已经证明：

$$
\nabla_{\b\theta}\mathcal{J}_{\mu}(\b\theta) = \opE_{s \sim \mu_{\b\theta}} \left[\nabla_{a} \left.Q(s, a; \b w)\right|_{a = \mu_{\b\theta}(s)} \nabla_{\b\theta}\mu_{\b\theta}(s)\right]
$$

这里仅仅将严格的 Q 值函数替换为 Critic 网络给出的估计值。

可以看出 DDPG 和 DQN 不同的地方仅在于：

- 选取行为是通过 Actor 网络完成的，而非使用 epsilon greedy 策略，使用神经网络一定程度上也保证了行为的随机性
- 选取行为的时候加入了全局随机数发生器增加探索未知状态的可能
- 每一回合均更新目标网络的参数，但是使用 Soft update，即通过衰减系数 $\tau$ 进行更新，而非 DQN 中完全同步
- loss 的计算融入了 Policy Gradients 的思路

这种能够打乱学习顺序的方式，很好地解决了传统 Actor Critic 中由于经常抽样到相同的转移路径导致反复学习无用知识的问题。

## 代码示例