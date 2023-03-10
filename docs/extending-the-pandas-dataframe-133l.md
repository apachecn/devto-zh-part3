# 扩展熊猫数据框架

> 原文：<https://dev.to/pj_trainor/extending-the-pandas-dataframe-133l>

Pandas 为处理表格数据提供了许多方便的工具。熊猫`DataFrame`是多功能的，但是有些操作只能在一个命令中完成。我会就如何扩展`DataFrame`以更好地适应您的工作流程提供一些建议。另外，当我说`DataFrame`时，我指的是熊猫馆的[类。](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)

假设您有一个`students`的集合，并且您想要选择 Frizzle 女士班上所有得“A”的学生。

这里有一种在熊猫身上实现的方法:

```
(students.query("grade == 'A'")
         .query("teacher == 'Frizzle'")) 
```

但是如果我们有一个自定义的方法呢？

```
students.select_by_grade_and_teacher("A", "Frizzle") 
```

> **注意**:通过将操作括在括号中，你可以在多行上链接几个熊猫操作，因为它们都返回`DataFrame` s。这被称为方法链接。

## 延伸是什么意思

我想做的就是给标准熊猫`DataFrame`添加自定义方法。自定义方法非常有用，因为它们:

*   ♻️简化了可重复多行逻辑
*   `DataFrame`中内置方法的⛓链

所以**扩展只是意味着给`DataFrame`** 增加额外的方法；这些方法可以返回另一个`DataFrame`，或者其他任何东西。

> **注意**:我们可以写一个函数，但是这不适合方法链接的工作流程，会产生更多的代码行

## 如何扩展`DataFrame`

既然我们想在不丢失旧方法的情况下给`DataFrame`添加新方法，我们应该考虑子类化`DataFrame`。也就是说，定义一个继承自`DataFrame`类的新类。

这伴随着一些警告。我们需要确保:

*   🏠它保留了`DataFrame`中的所有方法
*   🤝当一个自定义方法被调用时，它返回我们的类，而不是`DataFrame`(即不是子类)

第二点确保我们可以继续使用自定义方法进行方法链接。

下面的类就是这么做的:

```
import pandas as pd

class ExtendedDataFrame(pd.DataFrame):
    def __init__(self, *args, **kwargs):
        # use the __init__ method from DataFrame to ensure
        # that we're inheriting the correct behavior
        super(ExtendedDataFrame, self).__init__(*args, **kwargs)

    # this method is makes it so our methods return an instance
    # of ExtendedDataFrame, instead of a regular DataFrame
    @property
    def _constructor(self):
        return ExtendedDataFrame

    # now define a custom method!
    # note that `self` is a DataFrame
    def select_by_grade_and_teacher(self, grade, teacher):
        return (self
                .query("grade == @grade")
                .query("teacher == @teacher")) 
```

在这个 [repl.it](https://repl.it/@PjTrainor/extending-pandas-dataframe) 中查看它的运行情况

[https://repl.it/@PjTrainor/extending-pandas-dataframe?lite=true](https://repl.it/@PjTrainor/extending-pandas-dataframe?lite=true)

> **注意**:熊猫文档中有一页[是关于扩展](https://pandas.pydata.org/pandas-docs/stable/development/extending.html)的，但是它非常高级，包括了许多其他主题

## 何时延长

对于数据探索来说，这是一个有用的模式，尤其是在 Jupyter 笔记本中，或者任何有代码完成的环境中。您可以使用它来:

*   ⏲缩短高度重复的任务
*   👩🏽‍🎓作为与特定数据集打包在一起的实用程序，与您的团队共享...尤其是如果他们不是像你这样的熊猫专家
*   📊构建在标准功能之外绘制图的方法

## 最后的想法

我不建议在的制作中使用这种模式，因为它没有得到熊猫的官方认可。此外，您冒着与当前/未来的`DataFrame` API 冲突的风险。

我将留给你一个我经常使用的扩展`DataFrame`。它有一些更高级的特性，所有的方法都以我的首字母开头，`pj_`(所以在代码补全中更容易找到)。

[链接到我的数据框](https://gist.github.com/trainorpj/488a1c8809fc66a197835deaa62cfa86)

随时问任何问题，如果你发现有趣的东西，让我知道！