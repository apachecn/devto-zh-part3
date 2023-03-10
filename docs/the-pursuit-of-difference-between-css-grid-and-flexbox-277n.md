# CSS grid 与 flexbox 的差异追求

> 原文：<https://dev.to/li/the-pursuit-of-difference-between-css-grid-and-flexbox-277n>

昨天我碰到了这个问题的讨论:

> ![Chris Coyier profile image](img/7518b2bf949206c97dd304820e17cda7.png)克里斯·科伊尔[@克里斯·科伊尔](https://dev.to/chriscoyier)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)对于所有对 CSS grid 和 flexbox 都有所了解的人来说，你最喜欢用什么方式来解释它们的区别呢？2019 年 1 月 25 日下午 15:53[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1088827201468813312)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1088827201468813312)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1088827201468813312)

看起来最好的答案来自瑞秋:

> ![Rachel Andrew profile image](img/a8f6b9fd9267b82c01ca1acf1c8bd45a.png)瑞秋安德鲁@瑞秋德鲁![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@克里斯科伊尔](https://twitter.com/chriscoyier) Flexbox 是针对一维布局的。一行或一列。网格用于二维布局。行和列。2019 年 1 月 25 日下午 15:55[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1088827732874747910)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1088827732874747910)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1088827732874747910)

这是一个非常直截了当的回答，我当时就想“哦，好吧。”继续前进。

* * *

# 🤔

但是昨晚当我躺在床上的时候，我又开始思考这个问题:我只是不认为这是真的。

当我想得更多的时候，我意识到这也是关于组织和父母与孩子之间的关系:**在 flexbox 中，孩子决定如何组织布局以及它自己的空间比例；与此同时，在 grid 中，父母决定事物如何排列以及每个孩子应该占据多大比例的空间。**

意识到这一点后，我平静地睡着了。🌝

* * *

今天早上，我迫不及待地想与社区分享我的发现。
我在找上面的推文嵌入的时候，看到了该线程的另一个分支，结论和我一样！🤓

> ![Chris Coyier profile image](img/7518b2bf949206c97dd304820e17cda7.png)克里斯科伊尔[】@克里斯科伊尔](https://dev.to/chriscoyier)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我并不是 1D vs 2D 对网格 vs flexbox 的区分的最大粉丝，只是因为我发现我日常使用网格最多的是 1D，这很棒。这是一个很大的区别，尽管 2D 布局在网格中是可能的，但在 flexbox 中却不是。2019 年 1 月 25 日 16 点 11 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1088831796819648512)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1088831796819648512)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1088831796819648512)

> ![Rachel Andrew profile image](img/a8f6b9fd9267b82c01ca1acf1c8bd45a.png)瑞秋安德鲁@ rachelandruw![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Chris coier](https://twitter.com/chriscoyier)无论是要指定从父代向下，还是从子代向上，都有一个更微妙的区别。我经常解释说，当你有一堆不同大小的东西，而你只是想要一个合理的比例布局时，flexbox 是最好的。2019 年 1 月 25 日下午 16:20[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1088833882592555011)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1088833882592555011)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1088833882592555011)

所以结论是 flexbox 和 grid 的区别是*不仅仅是*一维还是二维，还有**是父母还是孩子应该指定内容的大小和比例。**

* * *

最后但同样重要的是，如果你不知道选择和使用哪一个，这里有一种方法可以帮助你决定:

> ![Rachel Andrew profile image](img/a8f6b9fd9267b82c01ca1acf1c8bd45a.png)瑞秋安德鲁@ rachelandruw![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@克里斯科伊尔](https://twitter.com/chriscoyier)如果你开始用一个宽度约束你所有的 flex 项目，那么通常情况下使用网格会更容易。2019 年 1 月 25 日下午 16:21[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1088834299128958982)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1088834299128958982)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1088834299128958982)

干杯，再见！😆