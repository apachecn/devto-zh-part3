# 熨斗博客#2 -水果圈

> 原文：<https://dev.to/nerdyandnatural/flatiron-blog-2-fruit-loops-4o4m>

因此，熨斗的第一个月是新信息的旋风。所有这些数组，循环，迭代，OOR。所有这些信息都让我觉得头昏脑胀(明白吗？)

但我觉得我没有很好地保留这些信息。我一直在接受挑战，但我觉得我需要靠自己“建设”。自己动手做东西对我保留信息帮助很大。

所以我决定开始建造。我从简单开始:让 ruby 在提示时生成水果。我称之为水果圈(Twitter 上有人给了我这个想法)。下面是代码(你也可以在这里查看它

[![](img/9861776de7a5a2d7d839f645ccf9fa39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0AHIqMua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/begg9l5qoml1y1tlau2l.png)

因此，代码会询问你是否想要一些水果。如果你回答是，代码会从数组中随机选择一个水果给你一些；然后程序就结束了。如果你说不，程序就会说拜拜，结束。如果你说了其他的话(比如问它一个问题)，它会循环并再次问你这个问题，直到你回答是或否。

这是对数组和循环一个很好的回顾。但是我很难使用 regex 来做这件事。你看，我希望它能够选择“是”和“不是”的各种答案，而不仅仅是“是”和“不是”。但是再多学一点，下次我可能会把它写下来。

我还启动了一个名为“免费水果星期五”的迷你项目。这里是代码(这里也是[这里是](https://repl.it/@SarahWilliams1/MeagerMintySuperuser)

[![](img/cef9b39cf6b21ecb733c7f8459a0f377.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dyljj1Sv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkpwsthnvl0l7ceyr1o1.png)

这还没有完成，代码还需要修改。但想法是这样的:今天是免费水果星期五，市场会向每位顾客赠送 500 份水果。但是，他们如何能够正确地将水果分发给顾客并保持跟踪呢？生成器首先询问顾客的姓名，然后询问他们想要多少块水果。然后它会随机给出#个随机水果。这个过程一直重复，直到用完为止，然后生成器会说没有水果了。

我使用了与之前实验中相似的方法，但我无法阻止循环无休止地生成，需要找出如何让它回到起点。我在考虑更进一步，使用 OOR。但是看起来我最终还是把数组和循环放下了。

Ruby 也没那么差。也许下一个帖子我会做高级 OOR。