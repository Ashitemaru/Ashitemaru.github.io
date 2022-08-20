---
title: 摸索 NexT 注入实录与实现振假名渲染
date: 2022-08-20 18:14:14
category:
    - 【杂谈】开发
---

由于日语文本中经常出现振假名（可以理解为国内小学课本上汉字上的拼音），但是 Markdown 并没有方便的振假名语法，所以只能摸索了。

<!-- more -->

首先是我们注意到 HTML 是有振假名相关的标签的，但是实在是太长了：

{% codeblock lang:html HTML %}
<ruby> WRITTEN <rp>(</rp><rt> READ </rt><rp>)</rp></ruby>
{% endcodeblock %}

那么为了解决这个问题，有两种选择：

- 定义一种新的 Markdown 语法，在将 Raw Markdown 传入解释器之前先做一步正则替换，将自定义的语法替换为 HTML 标签
- 在渲染后的 HTML 标签中找到所有打过标记的节点，将其替换为 `<ruby>` 标签

我一开始是摸索第一种解决方式的，然后遍历了一波 Hexo 的 Markdown Parser 的文档，没有找到如何解决这个问题。

我之所以想到这个解决方案，是因为之前有一个 VSCode 的 Markdown 插件提供了一个 `onWillParseMarkdown` 的函数，用户可以通过其参数获得 Raw Markdown，然后自行处理后返回交给解释器。

但是 Hexo 似乎并没有给我开这个后门。

---

于是只能思考第二种解决方案，这个时候我想到了 NexT 注入。

因为先前编写过 Twikoo Recent Comments，就是现在我的网站侧边栏上展现的最近评论，其就是在 `sidebar.njk` 内注入一个指定 `class` 的容器，然后在 `head.njk` 内部利用 jQuery 首先拉取最近评论然后再处理为列表项塞到指定容器中的。

那么我们考虑到，Markdown 的代码块会被渲染为 `<code>` 标签，我们完全可以利用此特性。而我们为这些需要替换的标签打的标记就是其以 `@` 开头并且用空格分为三块，我们识别到这个标记就进行替换。

我们只需要注入这样一段代码：

{% codeblock lang:javascript JavaScript %}
function replaceRuby() {
    $('code')
        .filter((_, node) => {
            var list = $(node).text().split(' ');
            return list.length === 3 && list[0] === "@";
        })
        .replaceWith((_, text) => {
            var list = text.split(' ');
            var written = list[1];
            var read = list[2];
            return $(`<ruby>${written}<rp>(</rp><rt>${read}</rt><rp>)</rp></ruby>`);
        });
}
{% endcodeblock %}

这样我们就可以用类似：

{% codeblock lang:markdown Markdown %}
`@ WRITTEN READ`
{% endcodeblock %}

的语法书写振假名。

效果的话可以看下述歌词的渲染效果：

{% note info no-icon %}
`@ 言葉 ことば`より`@ 単純 たんじゅん`な`@ 方法 ほうほう`で、いますぐ`@ 地獄 じごく`に`@ 落 お`としてくれよ。さよならはあんたも`@ 知 し`っている、`@ 笑 わら`ってくれよ、`@ 笑 わら`ってくれよ、いますぐに。
{% endnote %}

总之，这样就可以很方便地写振假名了，之后应该会逐步在涉及到日语的地方把之前的旧写法更换成新写法。

---

另外还有一个小细节，似乎部署后访问字体会出现 403，根据 [相关 Issue 的提示](https://github.com/theme-next/hexo-theme-next/issues/613)，应该是 Referrer 的问题。