# 新功能:组织通知！

> 原文：<https://dev.to/devteam/new-feature-organization-notifications-4fp8>

组织现在可以接收通知了！

如果您是某个组织的成员，您将收到关于您组织帖子的任何新评论或反应以及您组织的任何新关注者的通知。您可以在您的[通知页面](https://dev.to/notifications)中找到它们。

基本细节和技术实现可以在拉取请求中找到:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 组织通知 #1762](https://github.com/thepracticaldev/dev.to/pull/1762) 

[![Zhao-Andy avatar](img/d67fb0a65ffe2dea4c9a16a59bb7e448.png)](https://github.com/Zhao-Andy) **[Zhao-Andy](https://github.com/Zhao-Andy)** posted on [<time datetime="2019-02-07T23:06:15Z">Feb 07, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/1762)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]功能

## 描述

这为组织添加了通知！<g-emoji class="g-emoji" alias="tada" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f389.png">🎉</g-emoji>

实施细节:

*   任何组织的`member`成员都可以查看组织的通知。
*   任何查看组织通知的组织成员都会将其标记为`read`。
*   路线有:`/notifications/org/:org_id`和`/notifications/comments/:org_id`。这为属于多个组织的人保持了灵活性。
*   没有针对`posts`的过滤器，因为组织无法关注人员(因此无法接收新帖子的通知)。
*   组织可以在其组织帖子上接收新的评论和反应通知，以及新关注者的通知。
*   组织通知不计入钟形图标。
*   通知可以属于用户**或组织**。非此即彼，不可兼得。我可以添加一个数据库约束，但这需要一些定制的 SQL。不确定*是否完全*必要。

我忘记了一个功能——我写这篇文章的时候想起来了——组织可以向他们的追随者发送新帖子通知。如果我们需要，我可以把这个加进去。我认为这不会花太多时间在 T2。刚加了这个<g-emoji class="g-emoji" alias="+1" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44d.png">👍</g-emoji>

## 相关车票&单据

解决#1222

## 手机&桌面截图/录音(如有 UI 改动)

### 移动查看

[![Mobile View](img/61dc2f11aec33c76678d41b49668463b.png)](https://user-images.githubusercontent.com/17884966/52448716-c49c0900-2b02-11e9-8f97-95fe7bb4b6e8.png)

### 桌面视图

[![Desktop View](img/6b62e6215628b6002087b1404f2cfcf6.png)](https://user-images.githubusercontent.com/17884966/52448682-ae8e4880-2b02-11e9-81e1-9161c7c9fb85.png)

## 【可选】哪个 gif 最能描述这个 PR 或者给你的感觉？

[![Tada!](img/dc4aa1e256c6fc11697a6c902de58887.png)](https://camo.githubusercontent.com/a907508891f497c54415bf7ae39f41b5f74828a7/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f7978354367326f4e49466d6d594e764641422f67697068792e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/1762)

编码快乐！