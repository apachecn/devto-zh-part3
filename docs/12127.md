# 数据产品的语义版本化

> 原文：<https://dev.to/korenmiklos/semantic-versioning-for-data-products-296h>

在我的一个研究项目中，我研究了由外国首席执行官管理的匈牙利公司相对于由国内首席执行官管理的公司的表现。我需要将公司业绩数据与经理国籍数据合并。后一种数据是我们在自己的研究实验室中根据经理姓名收集的。

我最近注意到一个偶然的数据，使得我们在 20 世纪 90 年代早期将一些匈牙利名字归类为外国名字。一旦发现，就相对容易修复。现在，我想确保我的团队为经理国籍使用更新、更好的数据产品，而不是旧产品。

进入语义版本化:我要发布，参考`manager-db-1.0.1`。

## 什么是语义版本化？

正如您可能从软件开发中了解到的，语义版本化是一组关于如何对您的版本进行编号的规则:

> 给定主要版本号。小调。补丁，增加:
> 
> 1.  主要版本当您进行不兼容的 API 更改时，
> 2.  当您以向后兼容的方式添加功能时，次要版本
> 3.  补丁版本当你做向后兼容的 bug 修复。

我认为完全相同的规则可以应用于数据产品。修正数据中的错误是一个补丁，因此我增加了补丁号。向数据表中添加新列是添加功能，因此应该增加次要版本。但是什么时候增加主版本呢？一个数据产品的 API 是什么？

我的分析师同事的工作在两个方面依赖于我的数据产品:

*   他们的代码依赖于我的数据文件的模式。如果我删除或重命名一个列，或者改变它的含义，它们的代码可能会停止运行。模式就像代码的编译时依赖。
*   它们的输出依赖于我的数据文件的内容。如果我添加新行，它们的统计分析可能会产生不同的数字。内容就像代码的运行时依赖。

因此，模式应该比内容受到更多的保护。分析师习惯于迭代工作和不断变化的结果。但他们讨厌破译密码。所以模式是我为我的数据产品提供的公共契约，我应该在每次以不兼容的方式更改模式时增加主版本。

## 这在实践中是如何运作的？

[![Photo by Lars Blankers on Unsplash](img/22a513feb2831c0fb2deb18ad772c9be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P90nsdsl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ni0175aisq0f3btkn0ro.jpg)

以这个简单的 csv 食物表为例。

```
---food-1.0.0.csv
food,category,diet_quality_score
apple,fruit,2
tomato,vegetable,2 
```

在我将它发布为 food-1.0.0.csv 之前，我必须显式声明它的模式，例如，在 Cerberus 中:

```
{'food': {'type': 'string'}, 'category': {'type': 'string', 'allowed': ['fruit', 'vegetable']}, 'diet_quality_score': {'type': 'integer'}} 
```

然后我意识到，从科学上讲，西红柿是一种水果，而不是蔬菜。这是补丁，发布号为`food-1.0.1.csv`:

```
---food-1.0.1.csv
food,category,diet_quality_score
apple,fruit,2
tomato,fruit,2 
```

读了更多关于营养的东西，我把胡萝卜加入到我的数据集中。这是添加的功能，所以新名称是`food-1.1.0.csv`。请注意，修补程序编号被重置为零。

所有下游代码仍然与这个新数据集兼容。我的团队将会看到新版本，并期待运行时的变化，但他们可以安全地重用他们的旧代码。

```
---food-1.1.1.csv
food,category,diet_quality_score
apple,fruit,2
tomato,fruit,2
carrot,vegetable,1 
```

是时候打破 API 了。我加了油炸火星棒，既不是水果也不是蔬菜。

```
---food-2.0.0.csv
food,category,diet_quality_score
apple,fruit,2
tomato,fruit,2
carrot,vegetable,1
deep fried mars bar,fried stuff,-10 
```

这真的是违反 API 吗？数据集具有相同的列，并且每个列的含义与以前相同。但是我承诺在第二栏只吃水果或蔬菜，我违背了这个承诺。如果你的代码依赖于这个承诺，它也会被打破。因此我取消了主要版本。

你承诺了多少，你为实现承诺付出了多少努力，这是一个判断的问题。但是语义版本化让您能够在数据分析团队中有效地传达您的承诺。