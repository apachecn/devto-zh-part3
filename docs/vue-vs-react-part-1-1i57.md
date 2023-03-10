# vue vs React:2019 年为你的项目选择最好的工具。第一部分

> 原文：<https://dev.to/codicacom/vue-vs-react-part-1-1i57>

现在，每个企业家的目标都是在短期内建立一个顶级的网络解决方案。为了实现这个目标，编码人员现在有机会从许多 JavaScript 库和框架中进行选择。但是，我们总是看到开发者争论什么 web 开发工具更好。

在 Codica，我们认为 [Vue.js](https://vuejs.org/) 和 [ReactJS](https://reactjs.org/) 是前端开发中发展最快的工具，原因有很多。我们需要认识到这两种热门工具都有优点和缺点。

本文的目的是从许多不同的角度比较 Vue 和 React:从一般信息到技术特性。今天，我们想为您提供一篇文章的第一部分，考虑诸如**成熟度、社区和受欢迎程度、开发速度和成本**等重要问题。

## 我们为什么要对比 Vue 和 React？

最初，比较这两个工具的主要原因是尤雨溪(Vue 创建者)使用 React 作为创建新框架的灵感来源。

我想，如果我能提取 React 中我真正喜欢的部分，然后构建一个真正轻量级的东西，而不涉及所有额外的概念，会怎么样？我也很好奇它的内部实现是如何工作的。我开始这个实验只是想复制这个最小的特性集，比如声明性数据绑定。基本上 Vue 就是这样开始的。
尤雨溪

准确的说，React 和 Vue 是高度相似的。我们甚至可以从 [Vue.js 官方文档](https://vuejs.org/v2/guide/comparison.html)中确定。主要的相似之处是:

*   虚拟 DOM
*   反应和组件结构
*   JavaScript 用法

乍一看，这些 web 开发工具的相似之处多于不同之处。现在让我们涵盖不同的方面，看看细节上的异同。

## 成熟

Vue 和 React 分别由一家大公司和一位专业编码人员创建。因此，它给了我们持续的信心和巨大的社区援助，以防我们需要帮助。

为了方便起见，以下是关于这两种工具的一般信息:

[![](img/6789b072653f55fbd896ce1e8fb75b0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sLTysR5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/40toibmwdnkqfm0d9xwm.png)

ReactJS 由脸书于 2013 年构建并发布，目前仍在 WhatsApp 和 Instagram 等他们自己的产品中使用。它指的是 [GitHub 上最受欢迎的 JS 框架之一，在撰写本文时有大约 128，231 颗星。](https://github.com/collections/front-end-javascript-frameworks)

[![](img/6c4ac6c48e2c4e7eb583d17254f09721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QqSB-XAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ln22yxgdt6wx9x7qrq4i.png)

Vue.js 是由谷歌前创意技术专家[尤雨溪](https://twitter.com/youyuxi)于 2014 年创建的。在谷歌工作后，他总结了他对 Angular 和 React 的看法和知识，然后在相同的功能内构建了一个轻量级框架。

到目前为止，Vue 也是 web 开发中最受欢迎的框架之一，在 GitHub 上有 [137，343 颗星。](https://github.com/collections/front-end-javascript-frameworks)

[![](img/86ef15392f0e1f3274839bb8e0e0a885.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JDUvWdUk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ewaqa8t14zf0o3mofu8r.png)

另外，让我们检查一下 GitHub Stars 的对比历史:

[![](img/d9a19ff27a622249f358c42e246854b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fe0LQAFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xpbp5ek0xqrrt4yx9n7w.jpg)

总之，这些 web 开发工具有足够的历史、支持和声誉。这些事实带来了继续比较的兴趣。

## 社群和人气

在 StackOverflow 上，你可以在 [#reactjs](https://stackoverflow.com/questions/tagged/reactjs) 中找到 138.031 个问题，以及为 React 开发者创建的大约 50.000 个 npm 包。实际上，React 社区比 Vue.js 的要大得多。

相反，Vue 是一个渐进的轻量级框架，让你有机会简单快速地构建解决方案。也更容易学，因为学习曲线低很多。

考虑到 Vue.js 的受欢迎程度，你可以在 [#vue.js](https://stackoverflow.com/questions/tagged/vue.js) 内的 StackOverflow 上找到大约 35000 个问题，以及为 Vue 开发者创建的大约 15000 个 npm 包。

为了了解这些工具的受欢迎程度，我们应该看看由 JavaScript State 开发的 2017 / 2018 最受欢迎的 JS 框架调查图表。

[![](img/72fa718461a79c1cfa6c62da5d2a4fd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T72Rq8Or--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v5d3dopriut54v12h9e4.png)

来源:2017.stateofjs.com

[![](img/b7e4709b18147a38d4db691e9aaefd00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h0OVLC0H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/twg7gdjeurihro24mcri.png)

来源:2018.stateofjs.com

总而言之，React 往往是最受欢迎的 JS 工具，拥有最大的知识库、支持和社区。

## 开发速度和成本

**发展速度**

我们的经验表明，就开发速度而言，Vue.js 和 React 非常相似。原因是有许多额外的库、框架和包可以帮助您快速创建产品。

一般来说，一切都取决于开发人员的编程背景和技能，这给了他们向 ReactJS 或 Vue 倾斜的机会，但实际上，类似任务的时间估计大致相等。

**成本**

不得不说，开发成本取决于许多条件:编码背景、资历水平、位置等。为了对一些数字有个概念，我们将两种开发工具的平均成本从 **Codementor** 平台中加入。

ReactJS 程序员平均速率:

[![](img/cfad10d084199d74c66a2dfe749f446c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WI3b7b7m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rppwk5we6vwq5jrpzbkv.jpg) 
来源: [codementor.io](https://www.codementor.io/freelance-rates/reactjs-developers)

Vue 程序员平均比率:

[![](img/e8ec49e40e74e7fecf73fe1ac0f00053.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_j1LDHS---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ur1taid51ezmrvg9l32m.jpg)

资料来源:[代码导师。我](https://www.codementor.io/freelance-rates/vue-developers)

## 结论

在这篇文章中，我们讨论了诸如**成熟度、社区、受欢迎程度以及开发速度和成本**等比较点。在下一部分，我们将比较 React 和 Vue 的技术方面。

敬请关注，点击此处阅读全文版本:[Vue vs React:2019 年为你的项目选择最佳工具](https://www.codica.com/blog/react-vs-vue-2019/)。