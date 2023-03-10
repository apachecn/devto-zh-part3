# 2019 年是打字稿年吗？

> 原文：<https://dev.to/nickytonline/is-2019-the-year-of-typescript-18p2>

Jest 知识库中的这条评论，[https://github . com/Facebook/Jest/pull/7554 # issue comment-454358729](https://github.com/facebook/jest/pull/7554#issuecomment-454358729)，在过去几天里一直在 Twitterverse 上流传，似乎获得了很多关注。

> ![Mohsen Azimi profile image](img/57363d5df492ffa2210b8a9bd1626b10.png)莫森阿兹米@莫森 _ _ _ _![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)Woah！[@ typescriptlang](https://twitter.com/typescriptlang)
> [github.com/facebook/jest/…](https://t.co/f7mPnGOAtP)2019 年 1 月 16 日上午 04:40[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1085396444201275392)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1085396444201275392)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1085396444201275392)

一个脸书项目正在迁移到 [TypeScript](http://www.typescriptlang.org) (TS)的事实是一件大事，考虑到他们也在 [Flow](https://flowtype.org) 之后，后者是 TS 的直接竞争对手。

其他著名的 JS 开发者也加入了进来。有些人更喜欢骂人，所以提前道歉。😉

> ![Jamie 🏳️‍🌈 profile image](img/5f34ee3e55c667f3ddb9c58276c592cf.png)杰米🏳️‍🌈[@ Jamie builds](https://dev.to/jamiebuilds)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我支持 TypeScript...你也应该这样。
> 
> 流量就是不在乎社区...我说我在脸书的心流团队工作过。
> 
> 脸书应该完全降流。即使在这一点上，对他们来说也是一项糟糕的投资[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1064649666275340288)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1064649666275340288)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1064649666275340288)

> ![Kent C. Dodds profile image](img/94f207e09e64133620a88b3ae5533c42.png)肯特 c .多兹[@肯特多兹](https://dev.to/kentcdodds)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我现在用 TypeScript 已经有~1 周了。从流中迁移。
> 
> 气息。的。新鲜。空气。我会在一两个星期后写博客。不过这真的很棒。20:41PM-2018 年 12 月 20 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1075853845048188929)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1075853845048188929)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1075853845048188929)

> ![Ryan Florence profile image](img/12147af33d64e864028a13dd24d7f3c6.png)瑞安佛罗伦萨[@ ryanflorence](https://dev.to/ryanflorence)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)边注。TypeScript 正在接管。它在 2018 年获得了迅速的采用，令人尴尬的是我不知道它。如果你在找工作，如果我的研讨会参与者是任何一种体面的随机样本，你的下一个代码库将有 50%的机会使用它。2018 年 10 月 30 日上午 02:32[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1057097944082591744)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1057097944082591744)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1057097944082591744)

> ![Guillermo Rauch profile image](img/848f754a2ed68f9fa41d092cde1ed46b.png)Guillermo Rauch@ rauchg![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)TypeScript:将巩固 raw JS 作为原型语言的角色，并赋予它某种增加安全性、正确性、增强团队协作的增量路径。".大多数框架和平台都支持“ts”。奖金:[assemblyscript.org](https://t.co/vuSQFdVNsG)2018 年 12 月 29 日 20:08 点[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1079106970953560065)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1079106970953560065)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1079106970953560065)

这是心流的最后一颗钉子吗？又是一年 TypeScript 采用率继续上升，成为 TypeScript 年吗？

现在甚至有了一个推特账户。不难猜到是谁，但我还是觉得挺搞笑的。

> ![Horse TS profile image](img/ede013cc5a9949fd40dce49ab57a1e9c.png)马 TS@马 _ts![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)打字稿神圣的狗屎我已经看见光了2019 年 1 月 01:43AM-15[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1084989367670603776)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1084989367670603776)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1084989367670603776)

“因此，如果你一直坚持己见(老实说，像我一样)，2019 年绝对是学习 TypeScript 的一年。”、 [@kball](https://dev.to/kball) 引自[https://Zen dev . com/2019/01/15/frontend-development-topics-to-learn-in-2019 . html](https://zendev.com/2019/01/15/frontend-development-topics-to-learn-in-2019.html)

在下面的评论中分享你的想法，如果你在 dev.to 上写了任何文章，请不要脸地插入你的 ts 帖子。我会不要脸地先插入。

[![nickytonline](img/6883bc3afaaae319b5553cb8ecf6b599.png)](/nickytonline) [## 考虑使用 TypeScript

### 尼克·泰勒 10 月 8 日 175 分钟阅读

#typescript](/nickytonline/why-you-might-want-to-consider-using-typescript-6j3)

诺德伍德主题公司在 [Unsplash](https://unsplash.com/search/photos/this-is-the-year?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片