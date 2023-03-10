# 探索性数据分析:真实数据集上的熊猫框架

> 原文：<https://dev.to/strikingloo/exploratory-data-analysis-pandas-framework-on-a-real-dataset-og3>

今天我们将利用 [Python 的 Pandas 框架](http://www.datastuff.tech/data-science/exploratory-data-analysis-with-pandas-and-jupyter-notebooks/)进行数据分析，利用 Seaborn 进行数据可视化。

有时，当面临一个数据问题时，我们必须首先深入数据集并了解它。它的属性，它的变量分布——我们需要沉浸在这个领域中。

作为一个审美感很差的极客程序员，我发现每当我需要理解某个观点时，Seaborn 都是一个非常棒的可视化工具。

它在幕后使用 Matplotlib，但用默认的样式值设置图形，使它们看起来比我能做的漂亮得多。我们将查看一个数据集，我将尝试让您直观地了解如何查看不同的要素。谁知道呢，也许我们真的会从中得到一些启示！

#### 不能做一个没有鸡蛋的煎蛋:数据集。

在这个分析中，我使用了 [120 年奥运会](https://www.kaggle.com/heesoo37/120-years-of-olympic-history-athletes-and-results#athlete_events.csv)数据集，你可以点击链接下载或阅读更多信息。

我从 Kaggle 免费下载了它，如果你需要一个数据集来尝试一些新的机器学习算法，温习一些框架的 API，或者只是找点乐子，这是一个很棒的网站。

我将只使用“运动员事件”CSV 文件，该文件包含自 1900 年以来每届奥运会上的每名参赛者，以及他们的出生国、是否获得奖牌等。

一个有趣的事实是,“奖牌”一栏有 85%是空的，所以平均来说只有大约 15%的奥运会运动员真正获得了奖牌。除此之外，一些运动员还获得了不止一枚奖牌，这表明，在已经很少的奥运级别运动员中，有更少的人真正获得了奖牌。更值得称赞的是他们！

#### 探索性数据分析的第一步:数据集是什么样的？

首先，在我试图收集任何见解之前，我想对数据集有一个更好的直觉。有多少数据丢失了？有多少不同的列？我喜欢从这些问题开始。

我使用 Jupyter 笔记本进行分析，但是我将为我运行的每一行相关代码添加代码片段，以便您能够理解。

尽管如此，我还是在这个库中提供了这个笔记本[,以防你自己想看一眼，想有一个起点。](https://github.com/StrikingLoo/Olympics-analysis-notebook/)

我首先要做的是加载熊猫的数据，并检查它的大小。