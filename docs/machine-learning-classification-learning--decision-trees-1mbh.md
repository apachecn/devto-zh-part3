# 监督学习:分类学习和决策树

> 原文：<https://dev.to/swyx/machine-learning-classification-learning--decision-trees-1mbh>

*这是我学习[佐治亚理工学院/Udacity 机器学习课程](https://www.udacity.com/course/machine-learning--ud262)的系列课堂笔记中的第二篇。课堂教材是汤姆·米切尔的[机器学习。](https://www.cs.ubbcluj.ro/~gabis/ml/ml-books/McGrawHill%20-%20Machine%20Learning%20-Tom%20Mitchell.pdf)*

## 分类学习

以下是分类中需要定义的一些有用术语:

*   **实例**(或输入):定义你的输入空间的值，例如，组成你的图片的像素，信用评分，其他原始数据值
*   **概念**:实际将输入映射到输出(或将输入空间映射到定义的输出空间)的函数，将实例转换为某种输出，如真或假。您也可以将它们视为集合中的定义成员，例如“热狗”与“非热狗”
*   **目标概念**:我们努力寻找的概念——“实际答案”。
*   假设类:你愿意考虑的所有概念(也就是所有分类函数)的集合
*   **样本**(或**训练集**):所有输入**与**配对的集合，其标签为正确输出。
*   **候选**:你*认为*可能是目标概念的概念
*   测试集(Testing Set):看起来像是训练集，但是是你用来测试候选概念的。它们不应该和训练集一样，因为那样你就没有表现出**概括**的能力。

就像在学校一样，我们通过给学生一些他们以前没见过的新问题来测试他们对概念的理解。如果他们也能解决这些问题，那么他们就被认为已经学会了目标概念。

## 决策树:表示法

决策树看起来像你在计算机科学中看到的任何一棵树:

[![https://cdn-images-1.medium.com/max/1200/0*Yclq0kqMAwCQcIV_.jpg](img/5ff8e19384e5ae21a2d3132a1f0a2340.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ixcJOjTl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/0%2AYclq0kqMAwCQcIV_.jpg)

每个**决策节点**代表一个问题(“我饿吗？我有 25 美元吗？”)关于一个**属性**和**边**的结果是这个问题的完整答案(这里我们展示了一个二元的是/否，但是你可以推广到更多的离散值)。最后的结果是一个**输出**，在这里你实际上决定采取行动。

## 决策树:学习

决策树因为其[分支因子](https://en.wikipedia.org/wiki/Branching_factor)而极具表现力。著名的游戏[二十个问题](https://en.wikipedia.org/wiki/Twenty_Questions)就说明了这一点。你问每个问题的目的是缩小可能性的空间。所以先问**更一般的问题**是这里的最优算法，我们现在将学习如何定义它。

下面是解决二十题的最佳算法:

```
1\. Pick the best attribute to split the tree on 
    - ("Best" = splits the possible decision space exactly in half)
2\. Ask the next question
3\. Follow the answer path
4\. Go to 1\. until you have an **output** (or answer, in the case of the Twenty Questions game) 
```

这几乎就像我们需要的广义决策树。

## ID3 算法

[迭代二分法 3 算法](https://en.wikipedia.org/wiki/ID3_algorithm)是一种自上而下的学习算法，被发明用来通过编程**从数据集**中生成决策树*。粗略的伪代码是:

```
Loop:
    Select best attribute and assign to A
    Assign A as decision attribute for Node
    Foreach value of A
        create a descendant of Node (aka Leaf)
    Sort training examples to Leaves
    If examples are perfectly classified: break;
    Else Iterate over Leaves 
```

如果你注意的话，你会发现其中一些步骤比其他的要复杂得多。最大的一条是“选择最佳属性”。这通过选择具有最高“增益”的属性来完成。

**熵**

这个术语通常用于热力学，但也用于信息论。我们这里指的是香农熵，“一个信息源的不可预测性或信息内容的度量”。

[![https://wikimedia.org/api/rest_v1/media/math/render/svg/f96cf5194b9102f383a05c04c8994e7af8b161fb](img/aadc923a6befbc4d789d36fbcac91235.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---TKnkZXl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wikimedia.org/api/rest_v1/media/math/render/svg/f96cf5194b9102f383a05c04c8994e7af8b161fb)

直觉比确切的公式更重要，你可以参考二元情况下的图表形式:

[![https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/Binary_entropy_plot.svg/400px-Binary_entropy_plot.svg.png](img/fe36fc728033d4720d938ea8b746df0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---PHTnvW_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/Binary_entropy_plot.svg/400px-Binary_entropy_plot.svg.png)

也就是说，如果给我们一个所有热狗的数据集，它的熵将为零，因为它已经根据我们对热狗的关心程度进行了完全排序。同样，如果它不全是热狗，它也将是零熵的。但是，如果它是同样混合的，我们将不得不开始询问关于热狗的本质的问题，以便尝试以有用的方式细分我们的数据集。

**信息增益**

因此，信息增益是应用特定分类属性前后总熵的减少:

[![https://pbs.twimg.com/media/DxdymmTUcAApsgY.jpg:large](img/f8027a73c82cfa2e6c050563694eb964.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S8huuPCn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DxdymmTUcAApsgY.jpg:large)

因此，ID3 依赖于在每步之后迭代和计算每个属性的增益，并挑选最大的增益作为“最佳”属性进行分割。

**阅读更多**

在这个 PDF 中有[更全面的解释。](https://storage.googleapis.com/supplemental_media/udacityu/5414400946/ID3%20Algorithm%20for%20Decision%20Trees.pdf)

## ID3 偏置

偏见有两种形式(不是负面意义上的！)ID3 固有的。

*   限制偏差-我们将考虑的所有可能假设的集合。在决策树的例子中，我们已经决定排除所有的连续函数。
*   偏好偏差——我们更喜欢假设集中的哪种假设。这是我们在上一篇文章中讨论的归纳问题的核心。

ID3 偏向于“顶部良好的分割”和“较短的树而不是较长的树”。

## 决策树:挑战

*   **连续属性**:如果它们真的出现了，你如何扩展你的模型来处理它们？您可以通过建立截止点来处理**连续属性**，例如 A 组年龄< 10，B 组年龄 10 < =年龄< 20，等等，但是这些类别是任意的，如果不作弊和查看您的测试集，可能没有用。
*   **缩放**:可以用决策树表示属性的所有布尔组合:AND、OR、XOR 等等。然而，节点的数量呈指数级增长——确切地说是——所以要做出任何决定，你都必须问和回答许多问题。这是决策树模型的问题。
*   **Stopping/Noise** :既然可以重复属性，那么如果你的数据有一些杂色，会不会陷入死循环？(一些无效或不一致的标签)
*   **过度拟合**:树对于问题来说太大了。您可以通过检查一个[交叉验证集](https://en.wikipedia.org/wiki/Training,_validation,_and_test_sets#Cross-validation)来缓解。如果错误足够小，就停止扩展树(这里首先扩展广度很重要)。你也可以在完成最后一棵树后尝试 [**修剪**棵树](https://en.wikipedia.org/wiki/Decision_tree_pruning)。

## 懒惰决策树

正常的决策树是*急切的*，意味着所有的计算都是提前完成的。一个更好的方法可能是使用[懒惰决策树](http://robotics.stanford.edu/~ronnyk/lazyDT.ps)，它根据一个特定的例子动态构建假设。在这里，您是在用预测时间换取训练时间，但是收益可能不成比例，因为您可能会更早地获得确定的、零散的特征(有关详细信息，请参见本文)。

## 我们系列的下一个

希望这是对决策树的一个很好的高层次的概述。我正在计划更多的初级读本，希望您能就以下方面提供反馈和问题:

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