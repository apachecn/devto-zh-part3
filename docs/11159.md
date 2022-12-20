# 如何使用 Dask 数据帧在 Python 中运行并行数据分析

> 原文：<https://dev.to/strikingloo/how-to-run-parallel-data-analysis-in-python-using-dask-dataframes-53ko>

有时候，你打开一个包含 Python 的熊猫的大数据集，试图获得一些指标，整个事情就可怕地冻结了。Dask Dataframes 也许能解决你的问题。

如果你从事大数据工作，你知道如果你使用熊猫，你可能会为一个系列的简单平均值等待整整一分钟，我们甚至不要调用 *apply* 。这只是几百万行的数据！当你达到数十亿时，你最好开始使用 Spark 或其他东西。

不久前我发现了这个工具:一种加速 Python 中数据分析的方法，而不必获得更好的基础设施或转换语言。如果你的数据集很大，它最终会感到有限，但它比普通的熊猫要好得多，可能正好适合你的问题——特别是如果你没有做大量的重新索引。

#### Dask 是什么？

Dask 是一个开源项目，它为你提供了对 NumPy 数组、Pandas 数据帧和常规列表的抽象，允许你使用多核处理对它们并行运行操作。

这里有一段直接来自教程的摘录:

> Dask 提供了模拟 NumPy、lists 和 Pandas 的高级数组、Bag 和 DataFrame 集合，但可以在不适合主存的数据集上并行操作。对于大型数据集，Dask 的高级集合是 NumPy 和 Pandas 的替代方案。

听起来就很牛逼！我开始尝试本文的 Dask 数据框架，并对它们运行了几个基准测试。

关于 Dask 在并行机器学习中的另一个用途，[点击这里查看这篇文章](http://www.datastuff.tech/machine-learning/k-means-clustering-unsupervised-learning-for-recommender-systems/)。

#### 阅读文档

我做的第一件事是阅读官方文档，看看在 Dask 的而不是常规数据帧中到底推荐做什么。以下是[官方文件](http://dask.pydata.org/en/latest/dataframe.html)的相关部分:

*   操作大型数据集，即使这些数据集不适合内存
*   通过使用多个内核加速长时间计算
*   使用标准的 Pandas 操作(如 groupby、join 和时序计算)对大型数据集进行分布式计算

接下来，它列出了一些使用 Dask 数据帧时速度非常快的东西:

*   算术运算(乘法或加法)
*   常见聚合(平均值、最小值、最大值、总和等。)
*   调用 *apply(只要它沿着索引-也就是说，不在 groupby('y ')之后，其中' y '不是索引-)*
*   调用 value_counts()、drop_duplicates()或 corr()
*   用 *loc* 、 *isin* 过滤，行选择