# 一类分类法简介

> 原文：<https://dev.to/stevenbruno/an-introduction-to-one-class-classification-2m5c>

#### 问题陈述

在统计学中，可能会出现我们必须将对象分类为属于组 A 或组 b 的情况。当我们为每个对象类标记了训练数据后，问题就相当简单了——我们可以利用二进制分类算法来预测新对象所属的类。当我们有未标记的训练数据时，我们转向聚类算法。到目前为止还不错，但是我们如何解决这样的问题:我们的训练数据只包含一个类的标记对象，而其余的都是未知类的对象？突然间，问题变得不那么简单了。更糟糕的是，甚至连值得信赖的 SKLearn 估算工具 Cheatsheet 都没有给出答案。

<figure>[![estimator](img/2870193bff23284964d9c83bcef6e40a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--njtcQFPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xigp0fuk9fevxlul4r0a.png) 

<figcaption>半标签数据呢？</figcaption>

</figure>

#### 背景

当我试图构建一个模型来估计太阳系以外的恒星在其轨道上包含系外行星的概率时，我也问过自己这个问题。美国宇航局系外行星档案包含了详细描述不同恒星系统和系外行星的信息宝库。在开普勒恒星数据集内，天文学家已经确定许多恒星的轨道上确实有系外行星。然而，对于其他恒星来说，它们是否拥有任何绕轨道运行的行星还是未知的。在这种情况下，我们有一个数据样本，其中一个类被标记(恒星包含一颗系外行星)，其他所有的都没有标记(恒星可能包含也可能不包含系外行星)。对于一颗恒星没有系外行星的情况，我们没有标签，因为要确定地说一颗恒星的轨道上没有任何行星，即使不是不可能，也是极其困难的。目的是构建和训练一个模型，根据一颗新观测到的测试星与已知包含系外行星的恒星的相似性，估计该测试星在其轨道中包含系外行星的概率。我们有什么选择？

#### 解(此处跳至为 TL；博士)

我概述的场景就是所谓的[一级分类](https://en.wikipedia.org/wiki/One-class_classification)。在科学文献中有许多解释和应用，但我在这里将触及一些更受欢迎的概念。

*   **PU 学习**仅从正 P 和未标记数据中以半监督方式学习二元分类器 U. [了解更多信息](https://roywright.me/2017/11/16/positive-unlabeled-learning/)
*   **新颖性和异常值检测**决定一个新的观察值是否与现有的观察值属于同一分布(它是一个内点)，或者应该被认为是不同的(它是一个异常值)。[了解更多](https://scikit-learn.org/stable/modules/outlier_detection.html#outlier-detection)
*   一类 SVM 一类分类的 SVM 方法。[了解更多](http://users.cecs.anu.edu.au/~williams/papers/P126.pdf)

#### Python 资源

在 Python 中实现单类分类器有多种方法。关于这一点，我将让那些更有能力的人来讨论这种模型的实现细节。

*   Roy Wright 提供了一个关于 PU 学习的详细分解，并衍生出他自己的定制 Python 模型。
*   税务发展[一级 SVM](http://homepage.tudelft.nl/n9d04/thesis.pdf) 。
*   SKLearn 提供了一个[单类 svm 方法](https://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html#sklearn.svm.OneClassSVM)。

#### 结论

我希望至少我上面提供的资源能让你更好地解决你自己的一类分类问题。这是为 [ChiPy 导师项目](https://chipymentor.org/)撰写的系列博客文章的第三篇。作为我项目的一部分，我试图训练模型来估计一颗恒星在其轨道上有系外行星或在其轨道上有可居住行星的概率。将我的问题确定为一类分类问题是这个项目进展中的一个重要飞跃。我的下一步是实现一个单级 SVM，然后分析各种恒星的时间序列数据，以试图识别指示轨道行星的[分钟变暗事件](http://www.planetary.org/explore/space-topics/exoplanets/transit-photometry.html)。