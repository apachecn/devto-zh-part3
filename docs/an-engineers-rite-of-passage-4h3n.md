# 工程师的成年礼

> 原文：<https://dev.to/molly/an-engineers-rite-of-passage-4h3n>

对每个工程师来说，记录生产是一个必经之路。无论是向所有用户提供完整的 500 页，还是中断后台处理，在您职业生涯的某个时刻，您都会停止生产。当它发生的时候，特别是第一次，它可能是粗糙的！至少，我知道这是为了我。

### 我的第一次停产

在被 Kenna 聘用后不久，我正在处理一个要求我向数据库中的一个表添加一列的票据。当时我们使用的是简单的 Rails，所以我所做的就是为新专栏编写一个迁移并发布一个 PR。PR 被批准了，我马上就合并了。在我两年多的经验中，我运行的每一次迁移都只需要几秒钟。这有什么不同呢？我相信你们中的一些人可以看到这将走向何方😉

[![](img/82bba7770a31a3ea980e67866799aa9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MMrnaVD2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2t241ru6imfham0xc4sf.gif)

不幸的是，迁移并没有花费几秒钟。你看，我添加列的表是我们数据库中最大的表！几亿行那么大。一旦迁移开始，整个表就会自动锁定，并保持锁定 3 个小时。我还应该提到，这个表有很多写操作。原本打算更新表格的乔布斯开始四处发飙。这是一场灾难。幸运的是，那时我们还很小，所以我们一直等着。迁移完成后，我们重试失败的作业，并从另一端顺利完成。

现在生意可能很好，但我崩溃了。几天后，我的老板想和我谈谈。我想肯定是这样了，我要被解雇了。但相反，我的老板问我停电后过得怎么样。我回答说我还在坚持。他继续向我保证，这不仅仅是我的错，并提醒我还有其他人批准了这份公关。他还说，他本可以做得更好来防止它。他解释说，这只是有时会发生的事情，我还在学习，所以我不应该为此责备自己。

[![](img/bee3c46b8bf13ee96563a054ad90d7e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P6S557xP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/31uyr6hz01ipzzxlzovu.gif)

听到这些正是我所需要的。从我第一次停机到现在已经 3 年了，在这段时间里，我不止一次不同程度地停止了生产。那会让我成为一个糟糕的工程师吗？不会吧！每次发生这种情况，我都会从经验中吸取教训，并采取措施防止它再次发生。我也提醒自己，这并不是世界末日。幸运的是，我和一群很棒的人一起工作，通常几个月后，我们会回过头来看看我们的错误，然后笑一笑。

### 你的停产故事是怎样的？

你当时是怎么处理的？你从中学到了什么？让我们分享一些战争故事，这样下一次工程师停止生产时，他们可以阅读这个帖子，并被提醒他们并不孤单！！！🤗

[![](img/6046a3f3bccb66f759ef557ad646b1a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--swhbMF70--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kh5cdz2g74rzvnla4a1i.gif)