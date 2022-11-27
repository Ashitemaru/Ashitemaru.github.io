---
title: Next.js 框架摸索实录
date: 2022-11-24 17:10:09
category:
    - 【杂谈】开发
---

在出软工小作业的时候遇到了 React Router 配置和跨域问题，虽然这两个都不是什么大问题，我也不是不会解决，但是要是出到作业里面，势必会导致我要写好多文档还要写很多讲解。这个时候 yfgg 告诉我 Next.js 框架好之又好，昨天稍微试了一下感觉确实不错，所以就吃了安利迁移到了 Next.js 框架。但是今天接着写的时候，还是发现不少细节是需要注意的，这里就当备忘了。

<!-- more -->

# 初始配置

由于 Next.js 自己带了不少初始配置，所以加东西的时候不一定每个都需要自己从头再来，完全可以用官方模板。

我出的小作业采用 Next.js + TypeScript + ESLint + Jest，这一整套模板也正在编写，到时候可以开放给同学们直接下载。

# CSR 还是 SSR

Next.js 框架的一大优势就在 SSR。然而说实话我自己也没有特别明白 SSR 之类的概念，所以昨晚花了点时间学了一下。

最早期的前后端耦合应用实际上就采用 SSR，即 **S**erver **S**ide **R**ender，服务端渲染。当时严格而言就没有前端这个概念，服务器直接将渲染好的 HTML 扔给浏览器就结束了。

而随着前后端分离开发概念的出现，Vue、React 等框架逐渐采用 CSR，即 **C**lient **S**ide **R**ender，客户端渲染。CSR 中，数据交互大致可以描述为：

- 浏览器向前端服务器请求模板 HTML（或者说空缺关键数据的 HTML），这些 HTML 中携带获取关键数据的脚本
- 浏览器渲染模板 HTML，执行获取数据的脚本，向后端服务器请求关键数据
- 浏览器获得关键数据后，调整 DOM 展示最终页面

CSR 比较大的问题有两点：

- 如果后端服务器响应慢或者数据量大，模板 HTML 在页面上留存时间长，表现为 Loading 界面或者白屏，严重削弱用户体验
- 爬虫难以处理这类网页，对搜索引擎的优化（**S**earch **E**ngine **O**pimization，简称 SEO）困难，导致网页宣传效果不佳

但就开发环节而言，开发需要不断处理白屏问题，不断在组件中设置 `refreshing` 状态实际上比较浪费开发时间。

而现代优化过的 SSR 则相应做出了部分解决，其基本思路为：

- 浏览器向前端服务器请求页面
- 前端服务器向后端服务器请求关键数据，获取数据后根据自己已有的模板 HTML 渲染出完整 HTML 发回浏览器
- 浏览器直接渲染出完整页面

首先，浏览器不会收到缺失数据的 HTML，这样就从根源上解决白屏问题。进一步，由于服务端的算力和内网传输速度一般高于客户端算力和外网传输速度，所以渲染出最终 HTML 的时间相应缩短。另外，由于客户端能够直接得到完整 HTML，所以 SEO 也是简单的。

然而 SSR 显然也是有问题的：

- 服务器端压力极大，对于多并发情况处理无力
- 针对每个用户都需要重新渲染完整 HTML，重复计算问题严重

其中第二个问题实际上比较重要，如果某个网页和客户端具体信息无关，也就是说任何客户端都收到相同内容，那么这种 SSR 实际上不如 CSR。CSR 可以通过 CDN 缓存模板 HTML，而 SSR 此时对每个用户的请求，每次都需要完整渲染 HTML。

所以这也是 SSG，**S**erver **S**ide **G**eneration，服务端生成提出的契机。针对与客户端无关的、更新速度慢的页面，SSG 会在构建时直接生成页面并缓存在前端服务器，浏览器发出请求后会直接返回缓存页面，从而解决上述问题。

显然，CSR、SSR、SSG 各有利弊。就目前你清的教学、科协各个成员的掌握情况看，CSR 系列的开发明显占优势。另外考虑到软工课程不少类似于后台管理页面等更重视数据时效性、用户交互体验的应用，CSR 更应当重点讲解。

然而 SSR 不能因此完全废弃不讲，因为其针对用户无关页面的白屏加载问题绝对是重要的一种解决方案。SSR 依然是企业官网、个人博客等网页的首选。

所以目前依然是将传统的 CSR 布置为作业，然后让他们强制将 API 睡眠，体验白屏问题后让他们叙述 SSR 的好处，用此作为思考题。

# `useEffect` 重复调用问题

这是一个老问题，说实话也和 Next.js 没有多少关系。如果在 React 中写下述代码：

{% codeblock lang:typescript TypeScript %}
const App = () => {
    useEffect(() => {
        console.log("useEffect called!");
    }, []);

    return <p> Test page. </p>;
};
{% endcodeblock %}

会发现控制台里出现了两次 `useEffect called!` 的输出。

虽然严格而言不会出现很大的问题，然而由于这种 `useEffect` 常常用于获取页面的初始数据（CSR 常用手法），这个问题可能导致重复请求后端数据，对于用户鉴权等需求甚至可能是危险的，后端如果不去做相应处理甚至可能打穿后端（我软工还真的差点出锅，然而当时也没在意）。

然后我搜索引擎大作业里面采用了非常丑陋的写法解决了：

![](/uploads/note-of-next/1.jpeg)

今天才知道这个是 React 的严格模式的锅。严格模式下，在 dev 条件下 React 会有意重复调用这些钩子来提示可能的危险。

也就是说打穿后端实际上就是 React 告诉你，你这有可能出锅，你自己不处理好不怪我。

当然，release 之后 React 就不会有意重复调用了。

所以解决方式就两种：

- 好好写能够处理并发的代码，React 都告诉你会出事了
- 关掉严格模式，让 React 闭嘴

我选择了后者。

参考：<https://juejin.cn/post/7105652180501135367>。

# `useRoute` 与注水

使用动态路由的时候，我按照 React 的开发习惯写了下述代码：

{% codeblock lang:typescript TypeScript %}
const route = useRouter();

useEffect(() => {
    console.log(route.query.id);

    // Use route.query.id to fetch data
}, []);
{% endcodeblock %}

令人惊讶的是，这里输出了 `undefined`。

事实上这是 Next.js 预渲染的锅，原文是这样的：

{% note info no-icon %}
Pages that are statically optimized by **Automatic Static Optimization** will be hydrated without their route parameters provided, i.e `query` will be an empty object (`{}`).

After hydration, Next.js will trigger an update to your application to provide the route parameters in the `query` object.
{% endnote %}

简而言之，ASO 是一种预渲染优化，在不获取路由信息的条件下先弄出来一个页面给浏览器，然后再注水（即向 `query` 注入路由信息）并触发组件更新，也就是说页面获取到路由信息并不是一开始，从而 `useEffect(*, [])` 是拿不到路由信息的。

了解这一点后，代码就应该写为：

{% codeblock lang:typescript TypeScript %}
const route = useRouter();
const query = route.query;

useEffect(() => {
    if (!route.isReady) {
        return;
    }

    console.log(route.query.id);

    // Use route.query.id to fetch data
}, [route, query]);
{% endcodeblock %}

应该让 `useEffect` 检测 `route` 和 `query` 的变动，在注水完成后才能触发数据获取。