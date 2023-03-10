# 如何编写这个 SQL 选择最佳方式？

> 原文：<https://dev.to/darksmile92/how-to-write-this-sql-select-the-best-way-63c>

嘿伙计们，

我知道我的主页上有一些 SQL 专家，我想找到为我的任务编写 SQL 语句的最佳方式。

让我们直接进入我的结构。我将画出表格结构的草图，并且只包含我的案例所需的字段。

**表头表(HDR)** :

| 博米德 | RecId |
| --- | --- |
| firstcom | 0515151848480 |
| SecndBOM | 0525091647216 |

**线条表(线条)**:

| 博米德 | ItemId | RecId |
| --- | --- | --- |
| firstcom | 柯里泰姆雷 | Nine hundred and eighty-four million nine hundred and eighty-six thousand five hundred and forty-one |
| firstcom | 番茄酱瓶 | Five hundred and fifteen million one hundred and five thousand six hundred and eighty-five |
| firstcom | 橡皮人 | Five hundred and forty-one million nine hundred and eighty thousand eight hundred and ninety-four |
| firstcom | 特沙班德罗 | Nine hundred and seventy-six million five hundred and twelve thousand and fifty-eight |
| SecndBOM | 副产品 12 | Eight hundred and ninety-six million four hundred and fourteen thousand nine hundred and eighty-six |
| SecndBOM | 番茄酱瓶 | Three hundred and twenty-four million eight hundred and fifty-eight thousand seven hundred and fifteen |
| SecndBOM | 冷冻标签 | Three hundred and twenty-one million fifty-four thousand eight hundred and seventy-nine |
| SecndBOM | 磁控管 | Nine hundred and eighty million four hundred and eighty-nine thousand five hundred and forty-one |
| SecndBOM | 木棍 | Two hundred and sixty-one million four hundred and fifty-six thousand six hundred and ninety-six |

**关系:** HDR (1) - (N)线

任务:
我只需要将`LINE`中的第一个 *(可能是单个)* `BOMId`的**项组合到这个`BOMId`中。**

例如:
当我搜索`ItemId`的“KetchupBottle”&&“rubber duckey”的组合时，我想检索`FirstBOM`。这个语句也不应该返回`SecndBOM`，因为“KetchupBottle”在那里，而不是“RubberDuckey”。

我试着把它分组，但是运气不好。

有点难以用语言解释...希望这可以理解！

## 你将如何为此编写一个选择？