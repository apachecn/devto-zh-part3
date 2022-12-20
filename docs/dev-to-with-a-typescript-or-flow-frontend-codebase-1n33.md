# 使用类型脚本或流前端代码库开发？

> 原文：<https://dev.to/nickytonline/dev-to-with-a-typescript-or-flow-frontend-codebase-1n33>

大声喊出[@韵](https://dev.to/rhymes)对这篇文章的评价。👏

在 dev.to 代码库开源之前，我在私有存储库中处理它，并在那里创建了一个问题，后来这个问题被复制到了公共存储库中(感谢 [@maestromac](https://dev.to/maestromac) ！).

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 考虑在前端代码库中使用 Flow 或 TypeScript# 383](https://github.com/thepracticaldev/dev.to/issues/383)

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2018-08-17T15:05:41Z">Aug 17, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/383)

[@nickytonline](https://dev.to/nickytonline) 评论[孙 2018 年 4 月 29 日](https://github.com/thepracticaldev/dev.to_core/issues/258)

我并不是说我们需要这样做，但是是的，我是 JavaScript 中静态类型的转换者，所以我在这方面确实有一些偏见。

在几个有大型 JS 代码库的项目中工作过之后，我可以肯定地说，它消除了很多愚蠢的错误，改进了 dx，并且它让开发人员更清楚地看到代码库中的契约和事物的形状。我甚至[在 TypeScript 的上下文中写过关于它的](https://dev.to/nickytonline/why-you-might-want-to-consider-using-typescript-6j3)。

我提出这个建议的原因有两个。第一个原因是上面的一切，第二个原因是它可能(不保证)激起开发人员在前端为 OSS 做贡献的兴趣，而不是在一个不使用 TypeScript 或 Flow 的项目上。

我已经使用 Preact 和 TypeScript，自从他们的上一个版本以来，这种支持似乎很稳固，但是对于 Flow，我不确定，因为我还没有真正使用过 Flow。我把这个问题抛给了 Twitterverse，【https://twitter.com/nickytonline/status/990768742178152448[。](https://twitter.com/nickytonline/status/990768742178152448)

第三个建议是，如果这不是每个人都要做的，如果您使用 VS 代码，您仍然可以从 TypeScript 中进行一些类型检查，而不会设置整个 TypeScript 构建管道。如果在文件的顶部添加一个`// @ts-check`注释，就会得到类型检查。参见[https://code . visual studio . com/docs/languages/JavaScript # _ type-checking](https://code.visualstudio.com/docs/languages/javascript#_type-checking)。

我想知道人们对此有什么想法？

* * *

@benhalpern 评论[2018 年 05 月 01 日星期二](https://github.com/thepracticaldev/dev.to_core/issues/258#issuecomment-385752020)

我赞成这件事。我认为我们正接近冲刺阶段的尾声，我和我们都没有回到起点的心态，但我们现在正在接近终点。这绝对是我想参加的会议。

* * *

[@nickytonline](https://dev.to/nickytonline) 评论[2018 年 07 月 09 日周一](https://github.com/thepracticaldev/dev.to_core/issues/258#issuecomment-403661166)

[@ben](https://dev.to/ben) ，这可能是开源代码库之前讨论的好事情。

看看 Flow 和 TS，我可能更倾向于 TypeScript。不仅仅是因为这是我已经专业使用了相当长一段时间的东西，还因为我认为类型的生态系统更大，它有更多的采用/工具。

作为参考，我的博客文章[考虑使用 TypeScript](https://dev.to/nickytonline/why-you-might-want-to-consider-using-typescript-6j3) 提到了一些使用 ts 的大型/流行项目，例如 Slack、MobX、LinkedIn、RxJS 等。

即使新的 Preact 组件目前只是 JS，您也可以进入混合模式，慢慢地将东西转换为 ts，同时仍然让 JS 存在于 TS 中，因为有效的 JS 就是有效的 TS。这是我们目前正在做的一个大项目，我们正在慢慢地转换到 TS。

这也是一个很好的方法，可以进行一些关于为代码库做贡献的现场会议。也许是几节关于 TS 的课。

* * *

[@nickytonline](https://dev.to/nickytonline) 评论[2018 年 8 月 16 日星期四](https://github.com/thepracticaldev/dev.to_core/issues/258#issuecomment-413723511)

[@maestromac](https://dev.to/maestromac) ，有机会的话能不能把这个问题迁移到公开回购上？不着急，因为我还要休假一周。谢了。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/383)

对于新接触类型的人，这里有一篇 Preethi Kasireddy 关于类型的文章。

[![iampeekay](img/f7d3dea50f5b348bbd6b0329ecd76c66.png)](/iampeekay) [## 为什么在 JavaScript 中使用静态类型？(第一部分)

### preethi kasi Reddy 12 月 8 日 1610 分钟读取

#javascript #types #flow](/iampeekay/why-use-static-types-in-javascript-part-1)

另外，这是最近的一集，来自关于 JavaScript 类型的[脚本和风格](https://scriptandstyle.com)播客，关于 JavaScript 的[静态类型](https://scriptandstyle.com/4d5e9839)。

## 打字稿

我自己偏爱[打字稿](https://www.typescriptlang.org)。我以前在这里写过。

[![nickytonline](img/6883bc3afaaae319b5553cb8ecf6b599.png)](/nickytonline) [## 考虑使用 TypeScript

### 尼克·泰勒(他/他)10 月 8 日 175 分钟阅读

#typescript](/nickytonline/why-you-might-want-to-consider-using-typescript-6j3)

对于那些对类型感兴趣的人来说，似乎有向 TypeScript 的转变。我在这里写了一点

[![nickytonline](img/6883bc3afaaae319b5553cb8ecf6b599.png)](/nickytonline) [## 2019 年是打字稿年吗？

### 尼克·泰勒(他/他)1 月 16 日 192 分钟阅读

#discuss #typescript #javascript #flow](/nickytonline/is-2019-the-year-of-typescript-18p2)

React 播客上也有一集很棒的内容是关于贾里德·帕尔默的打字稿。

> ![chantastic profile image](img/806eec03de9fc8db6e9fb63b3e5ad0f3.png)尚塔蒂奇[@尚塔蒂奇](https://dev.to/chantastic)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我喜欢和 [@jaredpalmer](https://twitter.com/jaredpalmer) 聊被炒作的技术。在探索新技术时，他务实、深思熟虑、有条不紊。
> 
> 如果你想在 React 应用中成功探索 [@typescriptlang](https://twitter.com/typescriptlang) ，这是一个很好的起点。[twitter.com/ReactPodcast/s…](https://t.co/6EFANvmrM8)2019 年 3 月 28 日下午 17:36React 播客 @ReactPodcast📣新的 React 播客！📰擅长打字💬打字稿。这是什么？我如何在我的 React 应用程序中获得一些？而它又在哪里胜过了 Flow、Reason、Elm 等语言呢？🎙 [@jaredpalmer](https://dev.to/jaredpalmer) 和 [@chantastic](https://dev.to/chantastic) 🏷#reactjs @typescriptlang @code🔗https://t.co/vbohHjYhPP🤗[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1111321292966264832)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1111321292966264832)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1111321292966264832)

带着 TypeScript 在某个操场上兜一圈:

*   非官方游乐场

[![nickytonline](img/6883bc3afaaae319b5553cb8ecf6b599.png)](/nickytonline) [## 增强的打字游戏场

### 尼克·泰勒(他/他)2 月 26 日 191 分钟阅读

#typescript](/nickytonline/an-enhanced-typescript-playground-49j6)

*   [官方打字稿游乐场](https://www.typescriptlang.org/play/)

## 流量

关于类型，Flow 是前端的另一个选项，尽管我自己从未使用过。

如果你想了解心流，这里有一些链接。

*   [用 Flow 编写更好的 JavaScript】](https://www.sitepoint.com/writing-better-javascript-with-flow)
*   [流程简介](https://pusher.com/sessions/meetup/js-monthly-london/flow)
*   [Flow:JavaScript 的静态类型检查器](https://dzone.com/articles/flow-a-static-type-checker-for-javascript)
*   老实说，我真的没有在 dev.to 上找到很多关于 Flow 的帖子，但请随意查看 [flowtype 标签](https://dev.to/t/flowtype)。

乘气流在[REPL](https://flow.org/try/)兜一圈

# 其他选项

我已经把范围缩小到了 TypeScript 和 Flow，因为它们是最受欢迎的，但也可以带其他人来讨论，例如 Elm，Reason。(感谢在 Twitter 上插话 [@magellol](https://dev.to/magellol) ！)

> ![Thomas Lefebvre profile image](img/e4bcfc4ca940eec66b5685b1cf4a22e6.png)托马斯列斐伏尔@ thomlbvr![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Nicky tonline](https://twitter.com/nickytonline)好听。我认为提到 Elm 和 Reason 与 TS 的比较也是很有趣的。这几天一直在看榆树，感觉资源轻了很多。2019 年 04 月 08 日下午 13:20[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1115243007928877057)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1115243007928877057)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1115243007928877057)

## 香草 JS🍦

如果你真的不想看到代码库转换成使用类型，那也没关系。

[![same old stuff gif from giphy.com](img/9749aa5024b767df0f9d76e3a9ee8252.png)](https://i.giphy.com/media/xT5LMOwBto9xvEC3nO/giphy.gif)

静态类型是否会引起开发社区中正在或正在考虑为前端代码库做贡献的人的兴趣？请在这里的评论中自由讨论和/或跳到 [GitHub 问题](https://github.com/thepracticaldev/dev.to/issues/383)并在那里发表评论。

<center>

[![Debate animated gif from giphy.com](img/313ec60647f97525302be5c6e1c004c4.png)](https://i.giphy.com/media/Wv493An4dA0xi/giphy.gif)T3】

</center>