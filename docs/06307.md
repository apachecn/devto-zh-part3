# 我对机器学习的尝试——风格转换和拼写

> 原文：<https://dev.to/nprimak/my-foray-into-machine-learning-with-style-transfer-and-spell-1lp1>

机器学习和人工智能已经在我的雷达上好几年了，但更多的是作为一个概念和“我应该知道的事情”，而不是我觉得我有空闲时间或技能去真正钻研的东西。然而，在过去的几个月里，我对机器学习的态度发生了变化，因为我看到公众可以获得新的更简单的工具。

一种这样的工具叫做拼写([https://www.spell.run/](https://www.spell.run/))。上周，我花了几天时间试图在我的笔记本电脑上设置 Tensorflow，使用正确的依赖关系来运行风格转换算法(参见 http://genekogan.com/works/style-transfer/[的一些例子)。作为一个认为自己长大后会成为艺术家的人，学习如何将我欣赏的艺术家的艺术风格转移到我的摄影作品中似乎是我能做的最酷的事情之一。然而，在经历了几个层次的依赖地狱并意识到我的机器可能需要几天或几周的时间来训练一个单一的艺术品后，我决定外包依赖管理的单调和沮丧以及 GPU 拼写的费用/速度。](http://genekogan.com/works/style-transfer/)

Spell 的伟大之处在于注册完全免费，当你运行命令时，它们会在 Spell 的许多 CPU 和 GPU 上远程运行，因此你不必一次让你的机器运行几天。当你使用他们的 GPU 时，他们确实会向你收费，但价格非常合理，而且你只需支付 GPU 实际训练你的模型的时间(我支付的是每小时 0.90 美元，训练模型需要 8 个小时)。他们有很好的文档和非常用户友好的设计，只是为了增加交易的甜头。

Spell 也有很棒的教程，其中一个实际上包括了我一直在尝试执行的风格转移训练。在这里为你自己看:[https://learn.spell.run/transferring_style](https://learn.spell.run/transferring_style)

经过一番辩论后，我决定用爱德华·蒙克的一幅画(不，不是《呐喊》)来训练我的模特。我对它并不像我希望的那样满意，因为我没有调整你训练模型时提供的颜色和抽象的任何参数。尽管如此，当我最终将这种风格转移到工作中时，这仍然是一个令人兴奋的时刻，我更加兴奋地继续尝试它。

如果你对自己尝试 AI 和机器学习感兴趣，我强烈推荐尝试一下 Spell。

下面是我用于风格转换的自拍照，以及蒙克绘画和最终图像(也是封面图像，惊喜:D)。

[![Photo I applied the style to](img/3416ad7ab3b527cd0368c5dc8d93e41d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SJCDxdCC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8yq93hb3rj0tz7xm10d.jpg)

[![Edvard Munch painting that AI was trained on](img/35a739d01d75d3723dc56e5fda059636.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yrHqaq2V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2h4liwd55cx1xf8cl5n9.jpg)

[![Final result](img/6de852263350706e5f1d09314d2a1266.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1vPWbCG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/64cyk5s17dpijwtw95nd.jpg)

如果你喜欢这篇文章，可以考虑在 Twitter 上关注我 [@nadyaprimak](https://www.twitter.com/nprimak) 或者如果你需要更多进入科技行业的技巧，你可以现在就阅读我的书[平装本或 Kindle 版本的《踏入 T4》。](https://www.amazon.com/Foot-Door-Getting-Industry-Programmer-ebook/dp/B0813XNFNF)