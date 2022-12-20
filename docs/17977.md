# 回归、离群值和啤酒

> 原文：<https://dev.to/lukaszkuczynski/regression-and-outliers-and-beer-57k8>

## 分布

这完全是关于数据分发的。在应用任何机器学习算法之前，你必须回答这个问题。我知道我想知道的吗？当一个人想要保持注意力集中时，需要清楚地记住这个问题。那么你的数据分布是怎样的呢？在[机器学习介绍](https://mena.udacity.com/course/intro-to-machine-learning--ud120)的第 7 课中，学生被给予几个介绍性的任务来理解数据分布是连续的还是离散的。离散分布可以比作包含人名或汽车品牌等元素的分类数据集。连续变量与某个范围内可以有任何值的变量有关，比如年龄或某物的体积。

## 回归参数

回归是一种寻找连续分布模式的方法。给定 X 值(一个或多个要素)，您会发现它会产生什么结果。当人们说:事情发生得越多，价值增长得越多时，有时会谈到回归。线性回归就是要有两个数字之间的关系。比如你想知道气温对啤酒消费量的影响有多大。对于这项研究，请查看本条目的下一部分。回归在数学上意味着什么，有很多很好的解释(例如，本博客[T1 或者更简单的](https://eli.thegreenplace.net/2016/linear-regression/)[这里](http://www.stat.yale.edu/Courses/1997-98/101/linreg.htm))，所以我不会在这篇文章中详述。但是简单来说，我可以告诉你，我学到了回归的两个主要参数。这些是**斜率**和**截距**。因为回归是一个数学公式，所以可以用下图表示

```
Y = a + bX 
```

在`scikit-learn`中，这两个参数很容易获取，我将在下一节展示。

## Sklearn 是**合体** ting

### 简单例子

将数据导入数据科学工具箱后，您可以利用“scikit-learn”的强大功能。它已经内置了主要的机器学习算法，所以你只需要遵循一个简单的流程:

*   数据准备
*   选择 X 和 y
*   拟合您的数据
*   算法评估
*   可视化(可选)

使用 sklearn，这样做很简单，如下所示(不包括导入):

```
x = np.array([1,2,3,6])
y = np.array([2,4,6,12])
plt.scatter(x,y)
reg = LinearRegression()
X = x.reshape(-1,1)
reg.fit(X, y)
print("Score is %f" % reg.score(X, y))
a = reg.coef_
b = reg.intercept_
print("Regression calculated for equation y=ax+b. Params are a (slope)=%.1f, b (intercept)=%.1f" % (a,b)) 
```

完整的笔记本请参考我的 [Kaggle 内核](https://www.kaggle.com/panlukaszk/the-simpliest-linear-regression-ever)。通常我们不会使用训练值(X 和 y)来评分，但是这个片段只是为了演示的目的。

### 真实故事:Kaggle 和啤酒消费

我在想，哪个数据集将是线性回归的一个很好的可视化。这就是我如何在圣保罗 [数据集](https://www.kaggle.com/dongeorge/beer-consumption-sao-paulo)中得出*啤酒消费量的。我利用`scikit-learn`和内置于`pandas`的`matplotlib`集成进行了一些计算。如果你对数据是如何准备的和我的步骤感兴趣，请参考我的 [Kaggle 笔记本](https://www.kaggle.com/panlukaszk/beer-in-saopaolo)。*

## 离群值

本课程的第八课讲述了异常值。这是一个重要的因素，尤其是在计算线性回归时。大多数情况下，你只需要看一看做散点图的数据就可以了。这是*啤酒消费*数据集中的方式。我没有看到任何重大异常，所以没有使用*异常值剔除*技术。但是最好记住我们确实拥有它们，在需要的时候利用它们。在这一课中，我看到了在处理*安然*数据时剔除异常值是多么实用。

## 总结

Udacity 机器学习入门的第 7 课和第 8 课将我的注意力集中到了连续分布上。我有机会处理真实世界的数据，结果刷新了我的 [Kaggle 账户](https://www.kaggle.com/panlukaszk)。这再次证明了 Python 是数据分析的主要语言。当然，我会继续更新 [GitHub repo](https://github.com/lukaszkuczynski/ud120-projects) 的内容，在学习这门课程的时候，它可以让我对自己的编码有所了解。