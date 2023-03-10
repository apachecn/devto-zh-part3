# 堆栈溢出开发者调查(2019 版)中你可能错过的一些东西

> 原文：<https://dev.to/remotesynth/some-things-you-may-have-missed-in-the-stack-overflow-developer-survey-2019-edition-4l3b>

Stack Overflow 开发人员调查收到了 90，000 多份回复，是针对开发人员社区的最大规模调查。这并不意味着结果完全反映了开发人员社区(StackOverflow 自己也承认这一点)，但它们是衡量社区某些方面的有用指标——从社区本身的构成变化到兴趣和工作重点。

去年，我看了看[给我留下深刻印象的一些项目](https://dev.to/remotesynth/some-things-you-may-have-missed-in-the-stack-overflow-developer-survey-40lo)，在最近发布了[今年的结果](https://insights.stackoverflow.com/survey/2019#developer-profile-_-social-media-use)之后，我想做同样的事情。

请注意，这些只是打动我的项目。我邀请你在评论或你自己的帖子中分享你自己的见解。

## 今年方法论有些变化

这些年来，StackOverflow 的社区受到了相当多的批评。去年，他们开始公开回应这些批评。我不是来评判这些努力在更大范围内的成败，但它确实影响了他们处理调查结果的方式。

首先，他们预先承认了这个问题:

> 尽管我们的调查覆盖面广，能够提供有价值的结论，但我们承认，我们的结果并不能均衡地代表开发人员社区中的每个人。我们还有进一步的工作要做，以使 Stack Overflow 成为我们想要的欢迎、包容和多样化的平台，这反映在我们的调查样本中。

其次，他们决定发布加权结果，试图纠正人口统计的偏差。因此，你得到的结果就像美国未加权的开发者角色...

[![developer type unweighted](img/c973b562a976d6fb9cf2802adb6ae949.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--naNof9EJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pwxy4ozljubx7bhgwa6a.png)

...并加权:

[![developer type weighted](img/f240564fdaa4db3011a04bfd57aa9d06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z-VCdZP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cjm6m4quffzlwitfpdkn.png)

在某些情况下，结果变化不大，但在其他情况下，它可以提供有趣的见解。例如，上面你会看到前 5 项的总百分比有一些微小的变化。然而，在此之后，加权实际上改变了顺序，designer 移动到数据库管理和开发运维之上，即使总体百分比变化仍然相对较小。

这种偏差很大一部分是与性别相关的。是的，整个行业的数字都很糟糕(T2 的一些数据显示，女性在整个科技行业中的比例不到 20%，许多报告显示这些数字还在下降)，但 StackOverflow 的回答是，整体而言男性超过 91%，甚至在美国女性也只有 11.7%。

[![gender makeup](img/87d97208c39784275b0e598b3cc36089.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AW-ZjP2y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18nj8hfj8bz2xnzpgxxh.png)

## 哇！Visual Studio 代码！

多达 50.7%的受访者在他们的开发环境中使用 Visual Studio 代码。接下来最接近的是完整的 Visual Studio，为 31.5%。

[![developer environments](img/463b0674fb08622b25751815909a56e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BFafENew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f23vdi1q37hqlf90o2sq.png)

当将 web、移动和 SRE/DevOps 分开时，Visual Studio 代码的受欢迎程度只会增加，移动是唯一一个几乎没有占据头把交椅的类别(仅落后于 Android Studio，但令人惊讶的是，高于 XCode)。

### 网络技术仍然占据主导地位

不过，这可能不应该是一个巨大的惊喜，因为 StackOverflow 似乎是由 JavaScript/前端开发者主导的。

[![most popular technologies](img/644f674ac61302d0db886d36abe874c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O5WKQuph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q9oia1kplybhtrr88blv.png)

尽管如此，网络技术仍然处于工资等级的底端，尤其是在美国。

## 大多数开发人员受雇于小型企业

不包括个体经营者，StackOverflow 上有整整 58.8%的开发者[在少于 500 名员工的公司工作](https://insights.stackoverflow.com/survey/2019#work-_-company-size)。

[![company size](img/c20341b7dbbfc47b77e6a4de86874c74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--27MRHyRV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2dq929oo8v308zpweode.png)

### 最喜欢自己的工作

超过 65%的人喜欢他们目前的工作，至少有一点喜欢。即使很多人不一定喜欢他们目前的工作，但至少对他们的职业选择还是有些满意的——近 3/4 的开发人员对他们的职业选择感到满意。

[![job satisfaction](img/afa802dbc2dcfa38b643a2490295eb59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5hARcZ_o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evvao6wl6g6gnyxzdw49.png)

或许他们在工作中如此快乐，是因为他们不是(也不太想成为)管理者。😉

[![want to be managers](img/0008e1b2c41d694e5475fd139ad803c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E1yTd0Ko--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06ffi2fitme1avwk1fxj.png)

## PHP 不付费

尽管 Scala、Clojure 和 Go 的开发人员不管他们有多少年的经验都获得了丰厚的报酬，但是 PHP 开发人员不管他们有多少年的经验，都获得了最低的报酬(看起来是很多)。

[![pay correlated to experience](img/7e0f1091322ec820d0fa0a42f6304fdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gopWmuqs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9vbnfq55mce6u35fjbk.png)

尽管如此，PHP 仍然很受欢迎，是第八大最受欢迎的语言，占所有受访者的 26.4%，仍然超过专业开发人员的 25%。

## 开发者爱 Reddit

社交媒体是我在 DevRel、社区管理和市场营销中的重要角色，所以我对社交媒体的使用很感兴趣。

[![social media](img/0198dc98161dfd7e96e8f6b2b1a41b35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--osIjNGXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1dfzw24zjvvc1a2abzcw.png)

所有受访者和美国受访者之间的差异非常显著。Reddit 仍然领先很多，但 Twitter 从第五名跃升至第二名。

[![social media in the US](img/242135f0227b4d7e66d8c0639cba3e5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B1jfeXaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9kmvvykeleu42nqspj5.png)

不过，特维奇明显缺席了。也许它没有被作为一个选项包括在调查中，考虑到最近现场编码流的流行，这是令人惊讶的。

## 什么打动了你？

你认为这次调查怎么样？无论您是否是 StackOverflow 用户，与您的体验相比如何？我很想听听不同的观点。