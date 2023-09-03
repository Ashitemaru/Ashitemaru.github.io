---
title: 用 Rust 写一个 Web 应用踩坑记录
date: 2023-07-24 14:53:03
category:
    - 【杂谈】开发
---

本来就有点想学一下 Rust 语言，但是一直也想不出来用来干什么，于是只能从最简单的地方开始了，就是写一个 Web 应用。目前大概计划使用 Yew 作为前端框架而 Actix 作为后端框架，至于什么时候能写完，那就看我的心情了。

<!-- more -->

# 安装和体验 Yew 框架

整体上安装 Yew 是很简单的，但是运行的时候还是出现了一些问题，主函数里面 panic 了。经典一波翻找 Google 之后找到了一条 issue 链接：

- https://github.com/koute/cargo-web/issues/251

这里面魔改了 Cargo Web，但是比较令我害怕的是这个魔改版在 2021 年就停止更新了。然而除了用这个版本之外，我也没找到这一问题的其他解决方案，于是只能提心吊胆安装了这个魔改版的 Cargo Web。

---

在这之后就是让我最为暴躁的一件事情。昨晚我配置完 Yew 环境之后直接去睡觉了，结果今天起床发现 `cargo install` 命令完全用不了了，一直提示权限不足。我本来以为这个挺好解决的，然而即使我重新设置了文件夹运行权限，这个问题也一直存在。我在 Rust 社区里面翻箱倒柜也找不到任何相关提示，直到我找到某一个 issue 的回帖下面提到安装东西的时候最好把 360 关掉。

当时我还没太当回事情，因为 360 毕竟是知名流氓软件，而且我自己也没装。然而转念一想，不对，我电脑里有之前美团实习的时候被迫装的卡巴斯基。我立刻就去翻卡巴斯基的日志，果不其然，给我找到了下面的东西：

![](/uploads/rust-web/1.png)

也就是说，我所有由 Cargo 发起的执行全都被卡巴斯基给拦截了，然后还删掉了一堆东西。

这我怎么能忍？但是还真就没办法，因为我很早就想删掉这个卡巴斯基，然而就是删不掉。删不掉的原因也很简单，美团让我安装的时候莫名其妙要求了设置密码，现在想要结束卡巴斯基的运行的话，必须要通过这个密码，而一年前的密码显然我早忘掉了。之前删不掉最多就是哀叹一下，忍着呗，现在是真的忍不下去了。于是立刻 Google 翻丢失密码下删卡巴斯基的方式，发现大家基本都是两条路，一条是通过官方提供的 Password Reseter 重置密码，一条是通过官方提供的 Remover 强制删去。

但是，令我震惊的是，这两条路我都没走通。Password Reseter 在我这里疯狂报错表示无能为力。最不能忍的是，简介里面写着**无需密码**的**官方** Remover 打开后，居然要求我提供密码。

我顿时恼火了，因为互联网差不多已经被我翻了个遍，差不多就这两条路，还有一条改注册表的野路子试了一下也没成功。于是，走投无路的我只能给客服发邮件请求帮助。

在这段期间里，有位同学提醒我，你既然在他运行后无法终止，有什么办法让他就运行不起来呢？这个时候我突然莫名其妙想到一个怪方法。我打开卡巴斯基主程序所在目录，把他主程序给重命名了。结果，在我重启之后，这玩意果然没能运行起来，因此我的 Cargo 开始正常工作。

然而这显然不是长久之计，如果客服实在没法给出什么比较好的方案，我应该要考虑重装系统了。

---

在折腾完上面一大通之后，我又开始摸索 Yew。结果发现，我一开始参考的 Yew 的文档居然是 14.4 的爆炸老版本的，于是赶快切换到 20.0 最新版文档，结果发现写法基本上已经被推翻重来了。

现在 Yew 文档里面推荐的 startup code 大概是：

{% codeblock lang:rust Rust Yew %}
use yew::prelude::*;

#[function_component(App)]
fn app() -> Html {
    html! {
        <h1>{ "Hello world!" }</h1>
    }
}

fn main() {
    yew::Renderer::<App>::new().render();
}
{% endcodeblock %}

另外，Yew 似乎采用了 Trunk 将 Rust 代码编译为 WASM，而废弃了先前使用的 Cargo Web（当然这也是我猜的），于是先前提心吊胆的 2021 老版本 Cargo Web 也可以心安理得卸载掉了。

---

研究了一下 Yew 的 startup code，感觉确实和 React 很像。一个组件就是接受属性并产出 HTML 结构的函数，然后主流程就是渲染根组件挂到 `index.html` 的 `<body />` 下。

除此之外，就是 `#[function_component(...)]` 这个语法实在是不是特别适应。

之后简单摸索了一下如何自己写一个组件。果然还是从可以调颜色和尺寸的正方形开始比较有意思：

{% codeblock lang:rust Rust Yew %}
use yew::prelude::*;

#[derive(Properties, PartialEq)]
pub struct SquareProps {
    pub size: usize,
    pub color: String,
}

#[function_component(Square)]
pub fn square(props: &SquareProps) -> Html {
    html! {
        <div style={format!(
            "background-color: {}; width: {}px; height: {}px;",
            props.color,
            props.size,
            props.size,
        )} />
    }
}
{% endcodeblock %}

这里看得出来，写法和 React 也几乎是一样的。作为对比，React 的话我会这么写：

{% codeblock lang:typescript TypeScript React %}
import React from "react";

interface SquareProps {
    size: number;
    color: string;
}

export default function Square(props: SquareProps) {
    const squareStyle = {
        backgroundColor: props.color,
        width: props.size,
        height: props.size,
    };

    return <div style={squareStyle} />;
};
{% endcodeblock %}

其实就已经看出来了，代码结构几乎是一模一样的。首先定义参数的格式，之后定义一个函数写渲染逻辑就行了。然而 Yew 相较于 React，对 CSS 或者说内嵌的 `style` 字段支持并不好。目前这种规模的组件看起来用 `format!` 宏先顶一下没啥问题，但感觉后面真的写起来，还是得去找格式表支持。
