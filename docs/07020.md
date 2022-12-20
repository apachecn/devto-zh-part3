# 回归的回归测试

> 原文：<https://dev.to/korenmiklos/regression-testing-for-regressions-5f9j>

好吧，这是一个令人困惑的标题。“回归”和“检验”在统计学中都有正式的定义。“[回归测试](https://en.wikipedia.org/wiki/Regression_testing)”是一个软件工程术语，用于确保对代码的更改不会在其行为中引入任何不想要的变化。

作为数据科学家，我们一直在从事回归测试。假设我估计，在 1992 年至 2014 年间的匈牙利制造企业中，外国经理相对于国内经理提高了 15%的企业生产率。然后，供应商发送一年的额外数据。我首先要检查的是我的估计值是如何变化的。或者我们想出一个新的算法来消除经理名字的歧义。结果如何变化？

[![](img/3ecae8b20429138a879913b019182761.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RDSLEAKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AvxRjo5gLPQPcrmkY) 
照片由[五玄土](https://unsplash.com/@oriento?utm_source=medium&utm_medium=referral)[上的 ORIENTO](https://unsplash.com/?utm_source=medium&utm_medium=referral)un splash

给定一个统计估计量(记住，[一切都是函数](https://dev.to/korenmiklos/everything-is-a-function-4171))

```
estimate = function(data) 
```

我们经常尝试不同的样本、数据清理方法、特征工程和统计算法，看看我们的估计值如何变化。我们更喜欢稳健的发现，而不是那些对我们方法中的微小变化非常敏感的发现。

有些测试是正式的，有些是非正式的。每门统计学课程都告诉你如何计算标准误差、置信区间以及如何进行假设检验。所有这些都是为了测试我们分析中的一个敏感性来源:抽样中的随机变化。

假设我对 1000 名经理进行了研究。我估计外国经理的业绩溢价是 15.0%，但在另一个 1000 人的样本中，可能是 14.8%。或者在另一个样本中是 16.1%。标准误差(比如 1.5%)和置信区间(比如 12.1-17.9%)告诉我，在随机抽取的不同样本中，我的估计值将如何变化。(事实上，我们可以仅从一个样本中计算出这一点，这是 frequentist 统计学最聪明的一招。“品茶的女士”给了统计思想史一个很好的概述。)

[**品茶的女士|大卫·萨尔斯堡|麦克米伦**T3】](https://us.macmillan.com/excerpt?isbn=9780805071344)

但在大多数应用中，我们很少担心采样变化。事实上，我的经理研究使用了大约 300 万匈牙利经理的数据。我更担心对不同数据清理程序和不同统计方法的稳健性。因此，和其他人一样，我参与了各种特别的健壮性测试。

#### 我们如何才能使这种测试更具可重复性？

至少，我们应该记录我们所做的每一步。我有时会在我的 git repo 中创建新的分支，名字类似于`experiment/narrow-sample`。例如，如果我使用一个更窄的样本定义，这些通常只是几个提交，我从中了解到我的结果会如何变化。然后我回到我的`master`分支，留下这些短分支晃来晃去。我留下了测试记录，但是我不确定这是 git 分支的正确用法。

我们也可以自动化其中的一些测试。机器学习中的交叉验证就是这种自动化测试的一个例子。我们可以在简单的[单元测试](https://en.wikipedia.org/wiki/Unit_testing)中添加各种断言。例如，如果两个 Stata 命令可以用来估计同一个模型，我可以

```
assert e(rmse) == old_mse 
```

当我切换到新命令时。这将检查两个估计器中的剩余均方误差是否相同。除非进行的回归是相同的，否则不太可能(尽管不是不可能)达到完全相同的 MSE。

但是，如果我期待一些变化，只是变化不大，我该怎么办呢？如果我的点估计是相似的，但是我的标准误差爆炸了怎么办？(一个应用微观经济学家的噩梦。)

我认为非常需要对统计估计的正式描述(一种“统计语法”)和一个比较它们的框架，如下所示:

```
assert estimate1.coefficient.similar(estimate2.coefficient)  
assert estimate1.coefficient.significant() == estimate2.coefficient.significant() 
```

我们应该测试什么样的价值观？点估计，标准误差？p 值？应该如何比较？我意识到我给出的问题比答案多，但我强烈地感觉到这是应用统计学(又名数据科学)可以改进的地方。