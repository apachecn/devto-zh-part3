# 任何对开源的贡献都是有价值的

> 原文：<https://dev.to/nickytonline/any-contribution-to-open-source-is-valuable-57d3>

我们经常听到人们这么说，但我想重申一下。你对开源的任何贡献都是有价值的贡献。当然，我们都喜欢在项目中添加功能或修复 bug，但是更新文档仍然是值得的。周末的时候想起了这个。

我的网站，[iamdeveloper.com](https://www.iamdeveloper.com)，目前正在使用[盖茨比](https://gatsbyjs.org)，一个很棒的基于 React 的静态网站生成器。

> ![unknown tweet media content](img/b5e1cbd64929414c6df4c14e566d0b8f.png)![amberley profile image](img/91ad34b97ffc6253ea2ba172088e233b.png)amberley@ amberey![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)(姗姗来迟)生活更新:高兴地说 [@gatsbyjs](https://twitter.com/gatsbyjs) 是(正式)我的果酱！🚀
> 
> 跃跃欲试加入这样一支伟大的乡亲队伍💫2018 年 9 月 20 日下午 15:22[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1042796116255879168)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1042796116255879168)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1042796116255879168)

对于我博客上的推文，我在大约一年前安装了 [gatsby-plugin-twitter](https://www.gatsbyjs.org/packages/gatsby-plugin-twitter/) 。对我来说效果很好，但后来我看到了这条推文。

> ![unknown tweet media content](img/09dc805ae5a9955f83fdcaea03c5c171.png)![Benedicte Raae profile image](img/f2d8932341449faf9568c93b28f19eb7.png)Benedict e Raae[@ Raae](https://dev.to/raae)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我有生以来的第一个 [@npmjs](https://twitter.com/npmjs) 包！是我👊
> 
> 一个 [@gatsbyjs](https://twitter.com/gatsbyjs) 插件，让你轻松地在你的网站上嵌入 twitter、instagram、youtube、vimeo、codepen、soundcloud、reddit 等等！
> 
> 请尝试并回报:D
> 
> 示例站点:[gatsby-remark-oembed.netlify.com](https://t.co/own82Sko24)
> NPM:[npmjs.com/package/@raae/…](https://t.co/NrTiMRd3jQ)20:38PM-2019 年 1 月 18 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1086362206005006336)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1086362206005006336)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1086362206005006336)

我检查了 npm 包，[@ raae/Gatsby-remark-o embed](https://www.npmjs.com/package/@raae/gatsby-remark-oembed)，然后按照链接找到了存储库和演示。它看起来很棒，速度也很快，支持的远不止 Twitter。我安装了这个包，复制了代码片段添加到我的 gatsby-config.js 中，一切就绪。

我启动了我的开发环境，我得到了关于插件找不到的错误。

```
Error: Unable to find plugin "gatsby-remark-oembed". Perhaps you need to install its package? 
```

Enter fullscreen mode Exit fullscreen mode

我有点困惑，反复阅读文档中的东西，然后意识到，由于作者发布了这个包，npm 包的名称不是同一个名称，它是一个[范围的包](https://docs.npmjs.com/misc/scope)，即`@raee/gatsby-remark-oembed`而不是`gatsby-remark-oembed`(就像文档中的代码片段)。我很高兴我知道发生了什么。我让插件在本地运行，然后我发布了我的网站，一切顺利。我也很高兴将它迁移到 TypeScript，但我们将在另一篇文章中讨论。

液体错误:内部

在这一点上，我想，如果我花 15-20 分钟试图弄清楚发生了什么，我相信其他人会欣赏我的发现，所以我发布了一个 PR 来更新文档。它被合并了，我觉得把这 6 个字符`@raae/`添加到文档中的代码片段就像我修复一个 bug 或者给一个项目添加新功能一样好。

对开源的每一个贡献都是有价值的。

液体错误:内部

感谢感谢 [@raae](https://dev.to/raae) ！

斯拉瓦·鲍曼在 [Unsplash](https://unsplash.com/search/photos/community?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片