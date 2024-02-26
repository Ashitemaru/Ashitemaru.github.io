---
title: 2024 春季学期记录
date: 2024-02-26 13:07:01
category:
    - 【生活记录】日记
mathjax: true
---

虽然有预感这一学期可能和之前一样每天都是无聊的重复，但是还是开个日记，以免真的有什么想要记录的时候却苦恼于没有地方写。

<!-- more -->

# 2024.02.26

这一学期的第一天，然而今天就要处理掉不少工作：

- THUInfo 一些信息需要提交掉
- 软工那边需要拉一些人
- TON 实验的打表还没做

虽然都是一些很零碎的小工作，可能一个下午也就做完了，但不知道为什么最近我的拖延症越来越明显，以往是有那么点拖延但也不至于现在这样。

---

忙活了一整天，软工那边似乎暂且不需要我参与什么，THUInfo 那边迅哥哥也暂且不急，所以今天就几乎全用来跑 TON 的实验，然后因为接到了第二天有腾讯 HR 面试的通知，还稍微做了点准备。

TON 这边实验的想法很简单，因为学长想要收集一些真实网络拥塞控制的情况来做 RL 训练，然而 RL 训练时现跑网络发包会导致训练大幅度延迟，所以不得不去思考怎么缩时间。那么，有个想法就是打表，针对所有可能的参数把 baseline CC 的 performance 都打表打出来，训练的时候直接读取就行。

然而参数空间不得不说还是挺大的，所以可能需要多机打表，目前讨论确定使用 50 台机子打表，那么就需要一个中心机来 assign task，而这就是我需要做的。

最初我觉得直接在 SSH 后面附上命令就可以了，然而如果直接使用 `os.system("ssh [name]@[ip] '[cmd]')` 的方式，就和串行没有什么区别了，而且这一方法需要保持 SSH 连接，以我现在的知识来说，我还真不知道如何让中心机和其他 50 台机子同时保持 SSH 连接。

所以最终采用的方式是让中心机发指令给边缘机，令其开一个 tmux 后台运行，之后就可以直接断开 SSH 了。

不过说起来简单，操作的时候事情还不少。首先边缘机上没装 tmux，需要手动装，然而要装就需要 sudo，sudo 又需要手动输密码，怎么在代码里面模拟终端交互行为当时卡了有段时间。不过最后还是找到了 paramiko 这个库，其 `invoke_shell` 方法可以模拟终端，大概类似于：

{% codeblock lang:python Python %}
# Connect to remote server
client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(ip, username="[name]", password="[password]", port=22)

# Run script
channel = client.invoke_shell()
channel.send("cd ~/pantheon && tmux new-session -d -s ton '[command]'\n")
output = channel.recv(65535).decode("utf-8")
{% endcodeblock %}

整体用起来还是比较舒服的。

然而就在我以为一切都已经解决了的时候，我发现 loss rate 一直为零，这意味着我有参数没有设置正确。这个时候，我得知了一个令我震惊的东西：**Python2 的除法对整数是向下取整的**。写惯了 Python3 里面默认返回浮点数的除法，这个 bug 我完全没有注意到，找了大概有一整个晚上才发现。

不过就算这样还是有无法理解的地方，那就是现在依然存在有的时候程序随机爆炸，打表打到一半突然死亡，这个更是我不知道原因到底在哪里，明天和贾老师联系一下再来思考。