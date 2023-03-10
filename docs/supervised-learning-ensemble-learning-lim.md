# 监督学习:集成学习和 AdaBoost

> 原文：<https://dev.to/swyx/supervised-learning-ensemble-learning-lim>

*这是我学习[佐治亚理工学院/Udacity 机器学习课程](https://www.udacity.com/course/machine-learning--ud262)的系列课堂笔记中的第六篇。课堂教材是汤姆·米切尔的[机器学习。](https://www.cs.ubbcluj.ro/~gabis/ml/ml-books/McGrawHill%20-%20Machine%20Learning%20-Tom%20Mitchell.pdf)*

## 直觉

[集成学习](https://en.wikipedia.org/wiki/Ensemble_learning)的目的是学习一组特征的权重，这些特征与你试图分类的任何事物都有某种未知的关系。

例如，将电子邮件分类为垃圾邮件/非垃圾邮件可以使用一些因素，如提到伟哥或加密货币或来自尼日利亚的钱。集合模型采用所有这些小因素，并学习权重，以便得出更广泛的结果。

例如，如果我们试图以不同的方式对礼物进行分类，例如金融垃圾邮件不同于 NSFW 垃圾邮件，这可能会有所帮助。换句话说，有些规则对于数据的某些子集更好。因此，根据上下文的不同，有些规则自然会比其他规则更好，但最终我们只是希望有一个总体规则来剔除垃圾邮件。因此，在这种情况下，我们的整体学习将学习一些接近或逻辑的东西。如果规则具有一些偶然的依赖性，即如果规则 1 和规则 5 都是活动的，则该邮件是垃圾邮件，那么集成学习近似于 and 逻辑。诸如此类。

我把这里的规则描绘成有意识地挑选出来的东西。但是要更深入一层，我们应该**将学习者应用于数据子集**，并让它挑选规则。

## 打包/自举聚合

挑选子集实际上是一个比看起来更大的假设。我们如何挑选和分离子集是有争议的，我们是否应该挑选一个子集假设数据是可分离的。

均匀随机地挑选数据实际上非常有效(参见[随机森林](https://en.wikipedia.org/wiki/Random_forest)与我们已经研究过的决策树)。以标准的 [UCI 住房数据](https://archive.ics.uci.edu/ml/machine-learning-databases/housing/)为例，从 5 个数据点的 5 个随机子集取 3 阶多项式的简单平均系综，胜过在整体上训练的 3 阶多项式。(“beats”在这里意味着它在样本外测试/验证集中做得更好)。这里的直觉是通过显著降低噪声的影响来实现更好的泛化/更少的过拟合，因为噪声在随机子集中可能不太普遍。这被称为**打包/自举聚合**。

然而，您确实倾向于丢失您可能已经获得的相互关系，并且由于较小的样本大小，来自每个子集的规则具有相应较低的统计能力。

## Boosting/AdaBoost

我们应该将每一轮子集选择的重点放在“最难”的例子上，而不是一致地随机选择子集。

我们可以使用“加权”平均值，而不是通过简单的平均来组合多个规则。

为了定义我们所说的“努力”和我们如何“权衡”，我们需要重新审视错误的定义。通常，这是错误分类的数量(在离散分类用例中)或偏离模型的平方(在连续回归用例中)。然而，有些错误并不经常发生，因此我们没有太多的机会对它们进行训练。一些状态的主导地位可能导致机器学习忽略重要的、更罕见的状态。因此，如果你的电子邮件中有 99%是垃圾邮件，那么一个简单的垃圾邮件分类器可以学习将你的所有邮件分类为垃圾邮件，并且有相当好的 1%的错误率。

因此，在我们的集成学习中，我们应该尝试逐步将注意力集中在那些与精心处理的数据子集相比“更难”正确的例子上。

专注于分类情况，这意味着修改每一轮的分布，专注于模型与我们的训练数据不一致的地方(也称为“最难的例子”)。形式上，你可以这样表示:

[![https://sw-yx.tinytake.com/media/951a43?filename=1548621831322_27-01-2019-10-43-44.png&sub_type=thumbnail_preview&type=attachment&width=509&height=248&&salt=MzI2MTczMl85NzcxNTg3](img/2cf10656dfbf7fa9a3cef193c28ec12c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RVtV3yhD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sw-yx.tinytake.com/media/951a43%3Ffilename%3D1548621831322_27-01-2019-10-43-44.png%26sub_type%3Dthumbnail_preview%26type%3Dattachment%26width%3D509%26height%3D248%26%26salt%3DMzI2MTczMl85NzcxNTg3)

其中`y`和`h(x)`代表训练数据和模型，为+1 或-1。因此，将它们与一个常数`alpha`项相乘，让我们在**不同意**的情况下，将`Dt`的重要性提高(或**提升**)一个正指数，如果**同意**的情况下，将重要性降低。关于自适应增强(AdaBoost)算法的更全面的解释，请点击此处阅读 Robert Schapire 的综述文件。 ( [alt 链接](https://www.cs.princeton.edu/courses/archive/spring07/cos424/papers/boosting-survey.pdf))

最后一个假设总结了迄今为止所有训练模型的结果，并且只使用结果的符号。

## 虚弱的学习者

一个**弱学习者**是这样一个学习者，当它试图学习你关于任何数据分布的假设规则时，它会比机会做得更好。这意味着，与没有信息增益的机会相比，它将获得某种程度的信息增益(正如我们在[我们的决策树章节](https://dev.to/swyx/machine-learning-classification-learning--decision-trees-1mbh)中所定义的)。这包括决策树、感知器和任何其他你可以想象的分类/回归模型。这使得 Boosting 成为建立在现有算法之上的元机器学习算法。

对任何数据分布的强调**是很重要的——这意味着你的规则需要分离状态的可能空间，使得弱学习者在任何分布下都有可能比机会*做得更好。([查看这个答案了解更多](https://www.youtube.com/watch?time_continue=187&v=lj-IO4uuVR8))所以尽管有这个名字，这是一个相当重要的条件，然而它对我们的使用来说是足够弱的。***

因此，助推依赖于作为假设的每一步的弱学习者。他们不需要每次都将数据集一分为二。他们只需要在对数据集的某些部分进行分类时比随机做得更好——之后，我们可以进一步细化和改变分布，以关注剩余的分歧。因此，AdaBoost 是一种算法，用于将“强”分类器构建为“简单”“弱”分类器的线性
组合。

实际的**推进**——分歧的上推——实际上并不一定要发生在这个方法起作用的时候，但是它确实通过不成比例地关注分歧而加速了趋同。

我们在这里已经做了很多理论——所以请检查这个可视化的例子，以确保你在推进和假设形成中抓住了潜在的直觉。

[https://www.youtube.com/embed/u1MXf5N3wYU](https://www.youtube.com/embed/u1MXf5N3wYU)

正如您在这里看到的，集成学习将非常简单的规则组合在一起以映射复杂空间的能力非常有用。在某种程度上，这类似于二分搜索法将 O(N)问题简化为 O(log N)问题——通过每次逐步分割数据集，我们可以确保最终的错误率——我们不能分类错误的东西——很小，并且几乎总是可以通过增加一层来改善(不仅仅是随机的)。

## 升压很少过拟合

过度拟合是模型中经常出现的问题(我们之前已经讨论过很多次了)——训练误差持续下降，但是测试/验证误差开始上升。奇怪的是，对于助推模型，这种情况不会发生。在我们的 SVM 讨论之后，我们将在下一章讨论更多，但是沙皮雷的各种谈话和论文([这里](http://media.nips.cc/Conferences/2007/Tutorials/Slides/schapire-NIPS-07-tutorial.pdf)和[这里](http://rob.schapire.net/papers/explaining-adaboost.pdf))讨论了这一点的一些理论支持。

## 我们系列的下一个

Schapire 还将 Boosting 扩展到了多类情况，这种情况具有独特的挑战性。

关于这个主题的进一步说明[可以在这里](https://storage.googleapis.com/supplemental_media/udacityu/367378584/Intro%20to%20Boosting.pdf)找到，这里有关于 AdaBoost 的[特定幻灯片。](https://github.com/pushkar/4641/raw/master/downloads/adaboost_matas.pdf)

希望这是对集成学习和 AdaBoost 的一个好的介绍。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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