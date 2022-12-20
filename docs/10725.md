# 什么时候应该选择 IQueryable 或 IEnumerable？

> 原文：<https://dev.to/imlaguiar/when-should-one-choose-iqueryable-or-ienumerable-2927>

首先:我很抱歉在这个讨论中缺少参考资料，我应该保存我浏览的链接。

浏览 WWW 时，我发现了一些关于这些接口的有趣文章，这个主题引起了我的注意，因为我意识到作为一个经验不足的开发人员，我倾向于只使用 IEnumerable。但是通读之后，首先似乎只有 IQueryable 应该在查询外部数据源时使用，这是有意义的，因为接口直接在数据源上应用过滤器，而不是将所有数据都存储到内存中。

但是后来，我发现[这篇文章](http://www.codetunnel.io/should-you-return-iqueryablet-from-your-repositories/)说不应该使用 IQueryable，因为它会破坏可测试性和其他一些原因。这让我迷失在两个界面之间，不知道什么时候使用哪个，当然我会更深入地研究，但首先我想听听这个可爱的社区意见。

所以我的网络粉丝们，你们对此有什么想法？