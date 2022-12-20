# 将小吃和蔬菜分类

> 原文：<https://dev.to/lukaszkuczynski/clustering-snacks-and-vegetables-48an>

## 有人监督 vs 无人监督:

Udacity 的第九课[介绍机器学习](https://eu.udacity.com/course/intro-to-machine-learning--ud120)向我展示了机器学习的另一面。我可以加强我在聚类领域的知识。这是一个特别有趣的话题，因为它揭示了洞察力，这可能会激发你进一步的分析。我最近开始使用 NLP，一个可能的用例是它可以将相似的文档组合在一起，然后您可以发现它们之间的联系。监督学习是关于有标签，然后检查这些标签是否适合新获得的数据的特征。无监督学习没有比较阶段，因为开始时不知道任何标签。

## K-Means 和 sklearn

使用 K-means 算法的聚类是最广泛使用的无监督技术之一。而是找到这样的集群中心，让整个系统处于“和谐”状态。使用欧几里得距离计算距离。

在培训期间有一个很好的可视化广告，你可以看看 naftaliharris 博客上的[所做的出色工作，因为对于每一个算法，我们都必须意识到它的局限性。其中之一是 K-means 是一种爬山算法。这个事实有自己的](https://www.naftaliharris.com/blog/visualizing-k-means-clustering/)[维基百科页面，你可以查看](https://en.wikipedia.org/wiki/Hill_climbing)。因此该算法对局部极小值非常敏感。因此，选择初始点(质心)的一些特定方式会导致我们不想要的群集。这就是为什么在 sklearn 实现中，我们鼓励您进行多次聚类，然后选择最佳的聚类。

## 用例:蔬菜 vs 零食。

接下来的[你可以在这里找到](https://www.kaggle.com/panlukaszk/vegetables-vs-snacks)。我想:也许我可以在某个地方使用集群？也许一台电脑能够足够聪明地将垃圾食品与美味食品区分开来？所以我找到了这个数据集，那就是[澳大利亚食物营养数据库](http://www.foodstandards.gov.au/science/monitoringnutrients/ausnut/ausnutdatafiles/Pages/foodnutrient.aspx)。我玩了一会儿，你知道吗？它工作了。当然，如果我花更多的时间仔细分配零食和蔬菜类别，这将更有意义，如果没有这些，我会有一些异常值，例如含有大量脂肪的西红柿。无论如何，克梅恩斯能够明确区分高热量、低维生素 C 的零食和低热量、健康的蔬菜。