# 监督学习:基于实例的学习和 K 近邻

> 原文：<https://dev.to/swyx/supervised-learning-instance-based-learning-and-k-nearest-neighbors-kge>

*这是我学习[佐治亚理工学院/Udacity 机器学习课程](https://www.udacity.com/course/machine-learning--ud262)的系列课堂笔记中的第五篇。课堂教材是汤姆·米切尔的[机器学习。](https://www.cs.ubbcluj.ro/~gabis/ml/ml-books/McGrawHill%20-%20Machine%20Learning%20-Tom%20Mitchell.pdf)*

## 什么是“基于实例”？

回想一下，监督学习[近似于函数](https://dev.to/swyx/machine-learning-an-overview-216n)。然后，在不参考实际数据的情况下，通过在函数中插入值来进行预测。

另一种方法是将所有原始数据(“所有实例”)放入数据库，当查询时，查找相应的输出。不用花时间做任何学习，所以又快又简单。

然而，当然，我们会在**一般化**上失败(例如，如果我们被问及接近但不完全是我们有数据的东西)，并且我们容易**过度拟合**(例如，如果我们的数据有噪声)。

因此实例学习查看最近的邻居来决定任何被查询的点应该是什么。确切地说，是最近的邻居。

## `K`最近的邻居

鉴于:

*   训练数据`D = {Xi, Yi}`
*   距离度量`d(q, x)`(代表你的领域知识——一种量化一个事物与另一个事物相似性的方法)
*   邻居的数量，`k`(也依赖于你的领域知识
*   一个查询点，`q`

算法很简单——给定`D`，根据你的距离度量`d(q, x)`找到`k`个最近邻居(K-NN)。

你可以通过这种方式进行**分类**和**回归**:

*   分类:基于`Yi`的投票——你的观点会被最多邻居分类
*   回归:`Yi`的平均值。

除了简单的投票或简单的平均将所有邻居的权重相同之外，您还可以通过亲近程度对他们进行加权，这样更亲近的邻居更重要。

## 懒惰 vs 渴望

您可以将这些算法松散地分为**学习**和**查询**阶段:

*   K-NN 的学习阶段计算复杂度为`O(1)`，而线性回归为`O(N)`。
*   K-NN 的查询阶段计算复杂度为`O(log N)`，而线性回归为`O(1)`

因此，更多的工作由线性回归前置，使它成为一个“热切”的学习算法，而 K-NN 在查询时做更多的工作，使它“懒惰”。

## [Python 中的](#in-python)

对于一些轻量级的编程实践，尝试解决这里的问题:
[https://www.youtube.com/embed/eBt8vTvmsV4](https://www.youtube.com/embed/eBt8vTvmsV4)

这里有一个通过欧几里德距离和曼哈顿距离计算最近邻的示例 Repl 解决方案。

注意，真正的答案([基于隐藏函数](https://www.youtube.com/watch?time_continue=319&v=X8tm6x2k_gQ))是`18`，K-NN 无法通过这些方法接近它。

## KNN 的偏见

KNN 的偏好偏见(它关于什么构成好的假设的信念)是:

*   局部性-假设附近的点是“相似的”
*   平滑度-使用平均值
*   所有特征同等重要

## 维度的诅咒

> 随着**特征**或**维度**数量的增长，我们需要**精确概括的数据量**呈指数级增长！

[更多关于维基百科](https://en.wikipedia.org/wiki/Curse_of_dimensionality)

## 一种混合的方法

除了欧几里德/曼哈顿加权/未加权平均方法来估计最终结果，我们还可以将 KNN 与回归相结合来创建[局部加权回归](https://en.wikipedia.org/wiki/Local_regression)，以实现两全其美。这不仅限于回归——在定义的最近邻中，您可以使用我们到目前为止介绍的任何方法，比如神经网络或决策树。

## 最优数据结构

我们还可以为 kNN 考虑比简单的查找表更有效的数据结构。球树在这方面很有前途。

## 我们系列的下一个

希望这是对基于实例的学习和 K 近邻的一个很好的介绍。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

*   [概述](https://dev.to/swyx/machine-learning-an-overview-216n)
*   监督学习
    *   [决策树](https://dev.to/swyx/machine-learning-classification-learning--decision-trees-1mbh)
    *   [回归](https://dev.to/swyx/supervised-learning-regression-4d17)
    *   [神经网络](https://dev.to/swyx/supervised-learning-neural-networks-mpo)
    *   [基于实例的学习(K 最近邻)](https://dev.to/swyx/supervised-learning-instance-based-learning-and-k-nearest-neighbors-kge)
    *   [集成学习(AdaBoost)](https://dev.to/swyx/supervised-learning-ensemble-learning-lim)
    *   [内核方法&支持向量机](https://dev.to/swyx/supervised-learning-support-vector-machines-3mgk)
    *   [计算学习理论](https://dev.to/swyx/supervised-learning-computational-learning-theory-160h)
    *   [VC 尺寸](https://dev.to/swyx/supervised-learning-vc-dimensions-10b)
    *   [贝叶斯学习](https://dev.to/swyx/supervised-learning-bayesian-learning-403l)
    *   [贝叶斯推理](https://dev.to/swyx/supervised-learning-bayesian-inference-4l72)
*   无监督学习
    *   [随机优化](https://dev.to/swyx/unsupervised-learning-randomized-optimization-4c1i)
    *   [信息论](https://dev.to/swyx/unsupervised-learning-information-theory-recap-4iem)
    *   聚类-2 月 25 日这一周
    *   功能选择-3 月 4 日开始的一周
    *   功能转变-3 月 11 日开始的一周
*   强化学习
    *   马尔可夫决策过程-3 月 25 日的一周
    *   “真实”RL-4 月 1 日开始的一周
    *   博弈论-4 月 15 日的一周