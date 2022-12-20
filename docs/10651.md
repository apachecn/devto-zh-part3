# 数据分析:Kaggle 数据集上的熊猫和海牛

> 原文：<https://dev.to/strikingloo/data-analysis-pandas-and-seaborn-on-a-kaggle-dataset-h6k>

好奇心和直觉是数据科学家最有力的工具。第三个可能是熊猫。

在我的[上一篇文章](http://www.datastuff.tech/data-analysis/exploratory-data-analysis-pandas-seaborn/)中，我向你展示了如何:

*   了解数据集有多完整。
*   画出一些变量。
*   看看一段时间内的趋势和倾向。

为此，我们在 Jupyter 笔记本上使用 [Python 的 Pandas 框架](http://www.datastuff.tech/data-science/exploratory-data-analysis-with-pandas-and-jupyter-notebooks/)进行数据分析和处理，并使用 Seaborn 框架进行可视化。

在前一篇文章中，和在这篇文章中一样，我们使用了来自 Kaggle 的 [120 年奥运会数据集。我们观察了一段时间内女性的参与情况、运动员的体重和身高分布以及其他变量。

然而，我们确实调查了每位运动员参加哪项运动的数据。

这一次，我们将关注数据集的体育列，并通过统计数据分析收集一些关于它的见解。](https://www.kaggle.com/heesoo37/120-years-of-olympic-history-athletes-and-results#athlete_events.csv)

我能想到的几个问题是:

*   什么运动适合胖人？高个子呢？
*   哪些运动比较新，哪些比较老？有什么运动实际上失去了奥运会的青睐，不再被玩了吗？
*   有没有一些运动总是相同的队伍赢？最多样化的运动呢，获胜者来自许多不同的地方？

和以前一样，我们将使用[这个 Github 项目](https://github.com/StrikingLoo/Olympics-analysis-notebook)进行分析，你可以派生它并添加你自己的分析和见解。

让我们潜进去吧！

### 体重和身高

对于我们的第一个分析，我们将看看哪些运动有最重和最高的运动员，哪些运动有最轻或最矮的运动员。正如我们在上一篇文章中看到的，身高和体重都很大程度上取决于性别，我们对男运动员的数据比对女运动员的多。因此，我们将对男性进行分析，但同样的代码只需切换“性别”过滤器就可以对两者进行分析。