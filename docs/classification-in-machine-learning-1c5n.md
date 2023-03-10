# 机器学习中的分类-第 4 部分

> 原文：<https://dev.to/apoorvadave/classification-in-machine-learning-1c5n>

在前几篇文章中，我们讨论了机器学习和回归技术的基础。这里我们将讨论分类，它也是一个类似于回归的监督问题。但是我们预测的不是连续值，而是离散值。它用于对不同的对象进行分类。这里的输出变量是一个“类别”，如红色或蓝色，是或否。举个例子，我们需要将电子邮件分类为垃圾邮件或不是垃圾邮件。将会有一组用于分类电子邮件的功能。根据这些特征的值，每封电子邮件被分类到其中一个类别中。一旦该模型在看到的数据/标记的数据上被训练，该模型预测未知数据的标签。

[![1](img/248394236b6d88da34255c3bc65833ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dzbbEGB4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0ixwrkads4zjv9drdqt.PNG)

让我们从分类算法的类型开始😃

## SVM

在 SVM 或支持向量机中，我们通过用最优超平面分离类别来区分类别。最优超平面是具有最大余量的平面。

[![2](img/53fcc017bc22962416611dfaba8ea48c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A7aVRb81--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sz6h5k9mcajgfmlqqznu.png)

在该图中，可能有多个超平面分隔这些类，但是如上所示，最佳平面是具有最大余量的平面。定义超平面边缘的最接近超平面的点称为支持向量。因此，即使删除数据集中的所有其他点(除了支持向量)，超平面的位置也不会改变。这是因为平面到支持向量的距离保持不变。 ***总而言之，在 SVM，我们想要一个具有最大间隔的最优超平面来分隔类别。*T3】**

很多时候，数据不是线性可分的，因此不可能定义线性超平面。在该图中，数据点属于内环和外环两类。正如我们所看到的，这些类不是线性可分的，即我们不能定义一条直线来分隔它们。然而椭圆形或圆形的“超平面”可以很容易地将这两类分开。要素 x 和 y 可用于创建定义为 z = sqrt(x +y)的新要素 z。

[![3](img/77044d9e192950918cd0f4525cb83672.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8T6Hye32--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rxo67sgej4og3v7zybm9.png)

将核函数应用于每个数据实例，以将原始非线性观察值映射到高维空间中，在该空间中它们变得可分离。这些功能可以是不同的类型。例如线性、非线性、多项式、径向基函数(RBF)和 sigmoid。

## 决策树

决策树有一个类似流程图的结构。这些分类器通过在每一步识别分裂节点来工作。

[![4](img/3aa889016ecccb7a8181cc7032f89e94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pGO5vkFP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x8wymsyy1edf7ox1es7j.png)

这种分裂是由信息增益决定的。**具有最大信息增益的属性被识别为分裂节点。**信息增益越多，熵越少。熵代表数据的同质性。当一个集合只包含一个类的实例时，它的熵为零。

下面简要描述了构建决策树分类器的步骤:

1.  计算目标变量的熵。
2.  然后，数据集根据不同的属性进行分割。计算每个分支的熵。然后按比例相加，得到分裂的总熵。从分割前的熵中减去得到的熵。结果是熵的信息增加或减少。
3.  选择具有最大信息增益的属性作为分裂属性，并且在每个分支上重复该过程。
4.  熵为 0 的分支表示叶节点。熵大于 0 的分支需要进一步分裂。

决策树非常容易过度拟合。为了完美地拟合训练数据，有时会在很大程度上进行拆分。这导致分类器失去其泛化能力。并且该模型在测试数据集(看不见的数据)上表现不佳。

为了处理这种过度拟合，使用的方法之一是**修剪**。它可以从根或叶开始，并涉及到砍掉决策树的一些分支，从而降低复杂性。

**减少错误剪枝:**从叶子开始，每个节点用它最流行的类代替。如果不降低精度，则保留这种变化。

**成本复杂度修剪:**在这种情况下，总体目标是最小化成本复杂度函数。创建子树序列，其中 Tn 是仅由根节点组成的树，T0 是整个树。在步骤 I，通过从树 i-1 中移除子树并用叶节点替换它来创建树。在每一步中，选择最小化成本复杂度函数的减少的子树，因此它是树的最弱的链接。

*丢失标签的处理*也包含在决策树算法本身中。在寻找分裂的候选时，丢失的标签不会产生任何信息增益。因此可以放心地忽略它。现在考虑一个例子，性别是分裂节点，这里有两个可能的值男性和女性。现在，如果一个实例在性别列中缺少值，我们应该如何决定该实例属于哪个分支(有趣吧？:p)。这种情况的处理方法是将缺少值的实例发送到所有子节点，但权重减少。如果有 10 个实例具有“男性”, 30 个实例具有“女性”, 5 个实例具有缺失值，那么所有 5 个缺失的实例将被发送到男性和女性子节点，对于“男性”节点，权重乘以 10/40，对于“女性”节点，权重乘以 30/40。

当在预测时，我们遇到决策树中的一个节点，该节点测试变量 A，并且对于该变量，我们有一个缺失值，那么所有的可能性都被探索。

[![3](img/7f599e3070004556df63ed1fcc895351.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sb6bhytP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ktaveiru9wkx1w5k81jy.jpeg)

## 逻辑回归

逻辑回归是最常用的分类算法之一。不要被回归这个术语搞糊涂了。这是一种预测违约概率的分类方法。考虑一个例子，我们需要预测一封电子邮件是垃圾邮件还是非垃圾邮件(默认类别是垃圾邮件)。这是一个二元分类问题。当我们应用逻辑回归时，预测电子邮件是否是垃圾邮件的概率(范围在 0 到 1 之间)。如果该值接近于 0，则意味着该电子邮件是垃圾邮件的概率接近于 0，因此该类被指定为非垃圾邮件。采用阈值来确定类别。如果概率值小于阈值，则电子邮件被识别为非垃圾邮件或垃圾邮件(因为主要类别是垃圾邮件)。

通过逻辑回归预测的值可以是-无穷大到+无穷大之间的任何值，因此对预测值应用 sigmoid/逻辑函数以将其压缩在[0，1]之间。

[![4](img/b895162fe1ffc24169774066f92eb9e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LJGVJmV1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vqefpxo5tjtnm13f6mp.png) 

<figure>

<figcaption>物流功能</figcaption>

</figure>

[![5](img/6b0b6d1ce81da75bf4c054099f8100ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G-RhhO_j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3lvj6zpnsfuyjjh98k6v.png)

上述方程是逻辑回归的一种表示，其中 RHS 是一个线性方程(类似于线性回归), b0 和 b1 为系数，X 为输入特征。正是这些系数在训练过程中被学习。左边的输出变量(y)是默认类别的赔率。赔率的计算方法是事件发生的概率与非事件发生的概率之比。p(X)是违约类的概率。

使用成本函数最大似然估计来计算系数。这种最小化算法用于优化我们的训练数据的系数的最佳值。最佳系数将产生一个模型，该模型将为默认类别预测非常接近 1 的值(例如垃圾邮件),并为其他类别预测非常接近 0 的值(例如非垃圾邮件)。

应用逻辑回归时要记住的几点:

1.  离群值和错误分类的数据应该从训练集中删除。
2.  移除高度相关的特征，因为这会导致模型过度拟合。

## 朴素贝叶斯

朴素贝叶斯是一种基于贝叶斯定理的分类算法。它有两个主要假设:I)每个特征独立于另一个特征，ii)每个特征被赋予相同的权重/重要性。

[![6](img/b4e7046178292924281190d28bc71a45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m-_y9Rzl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ceatxhf34vggk5rrozgu.png) 

<figure>

<figcaption>贝叶斯定理</figcaption>

</figure>

我们所说的 P(A|B)是指，给定事件 B，事件 A 的概率为真。一些流行的朴素贝叶斯分类算法是:

1.  高斯朴素贝叶斯
2.  多项式朴素贝叶斯
3.  伯努利朴素贝叶斯

## 随机森林

在这种分类算法中，它建立了决策树的集合，这有助于给出更准确的预测。每个决策树都有一个对输入变量进行分类的投票，多数类被分配给输入。由于涉及具有不同特征集的多个决策树，可以避免过拟合问题。

构建随机森林分类器的步骤是:

1.  选择 K 个随机特征，其中 k
2.  确定 n，其中 n 是通过寻找最佳分裂节点而创建的决策树分类器的数量。重复步骤 1 和 2，创建几个分类树。
3.  为了预测一个输入变量，从每个决策树中选取投票，并给该类分配最多的投票。
4.  随机森林通常在各种分类问题中表现良好。它能够处理缺失特征和分类特征。

## kn

KNN 代表 K 最近邻。这里的“K”是对输入变量进行分类时要考虑的最近邻的数量。k 个最近邻被识别，并且多数类被分配给输入变量。

[![7](img/56a91f3be44109f599f7e307a206e607.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GYFQ7hKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/750rv6elvh9de4unqf7q.png) 

<figure>

<figcaption>KNN 算法</figcaption>

</figure>

在试图识别小圆的类别时，当 K=3 时，考虑 3 个最近的变量。由于红十字标记占大多数，因此小圆圈类被设置为红十字。

唷！如果你设法读完了整篇文章，并坚持到了这一步，你做得很棒😄。这篇文章是对分类算法的简单解释。你必须深入挖掘这些算法背后的数学知识，以便更好地理解。如果您有任何反馈和补充，请随时发表评论。下一篇文章将是一个分类项目，类似于我们为回归所做的。敬请关注更多内容！😸