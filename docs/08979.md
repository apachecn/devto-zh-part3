# Python 的 random.choices 非常棒

> 原文：<https://dev.to/rpalo/python-s-random-choices-is-awesome-46ii>

我正在通读 David Kopec 的 Python (附属链接)中的[经典计算机科学问题，并且正在研究他的遗传算法示例。他描述了轮盘赌式的选择过程。](https://amzn.to/2FvzVDI)

一些背景:这不是一个关于遗传算法的帖子，但是想象一下。假设你有一群处于繁殖过程中的候选人。轮盘式的选择过程意味着你创造一个巨大的轮盘，上面有所有的候选人，每个候选人的楔形大小取决于他们的“健康”分数:他们有多少想要的结果。然后，您旋转滚轮，根据这些重量进行随机选择。

好吧，回到我的话题。

我通读了那段描述，并在精神上准备实现它。我们需要得到每个候选人的适应度，计算出他们如何累积，标准化到从 0 到 1 的范围，然后计算出如何随机选择。实现起来并非不可能，但这需要几行或更多的代码。酷豆。

然后我得到了作者的解决方案。

我就坐在那里。盯着它。

你知道那种感觉吗，当你看到一些事情让你很生气，因为你很努力地工作，然后有人进来用简单的方法做了它？但同时，你也对它印象深刻，因为它太棒了？

[![Dang... But... Alright, that's fair.](img/ca7ee87a715d3644931d56e76ac1e162.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kF-EYuLR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/hxdCpt2.gif)

差不多吧。

给你。看看就知道了。我稍微调整了一下，以显示更多的上下文:

```
from random import choices

def pick_parent_candidates(population):
    fitnesses = [candidate.fitness() for candidate in population]
    return tuple(choices(population, weights=fitnesses, k=2)) 
```

只是...它只是做事情。

反正， [`random.choices`](https://docs.python.org/3/library/random.html#random.choices) 挺酷的。事实上，整个`random`模块有很多被低估的功能，可以节省你大量的时间和代码。