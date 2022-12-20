# 如何快速搜索你所有的回购一次

> 原文：<https://dev.to/lubekpl/how-to-quickly-search-all-your-repos-at-once-2j6g>

使用微服务可能是一件痛苦的事情，尤其是当一个更改可能需要多个地方进行更新时。您必须检查各种存储库中您需要更改的代码片段。在那种情况下你能做什么？

1.  在你最喜欢的编辑器中一次打开你所有的回复
2.  使用 GitHub 的搜索
3.  打开你的每一个回购和手动搜索
4.  按照下面的说明，开始使用[猎犬](https://github.com/etsy/hound)

### 什么是猎犬，如何在本地设置？

> Hound 是一个速度极快的源代码搜索引擎。核心内容基于 Russ Cox 的这篇文章(和代码):正则表达式与三元组索引匹配。Hound 本身是一个与 Go 后端对话的静态 React 前端。后端为每个存储库保留一个最新的索引，并通过一个最小的 API 来回答搜索。
> 
> *   从[https://github.com/etsy/hound](https://github.com/etsy/hound)

它是这样工作的！

[![preview](img/1ffc4750b54d654e90d225eb1c9c87c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9EPwv0SB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/404uq3bmfwyyvao3sx9l.gif)

你应该这么做:

1.  转到你想保存猎犬配置的文件夹，对我来说是`~/code/hound`
2.  创建一个`config.json`文件，指向你的本地库，就像这个