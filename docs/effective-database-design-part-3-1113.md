# 有效的数据库设计:第 3 部分

> 原文：<https://dev.to/adammc331/effective-database-design-part-3-1113>

这是关于数据库设计和规范化系列的第三篇文章。在我们的[上一篇文章](https://dev.to/adammc331/effective-database-design-part-2-2ben)中，我们讨论了第一范式，并朝着正确的数据库设计迈出了第一步。我们了解到，我们应该有一个主键，原子值，没有重复组。我们还学会了如何分离我们的实体以避免冗余。

现在我们可以更进一步，确保我们遵守第二范式。

## 第二范式

如果一个表在 1NF 中，那么它在 2NF 中，并且没有部分依赖。这意味着非主键列(不属于键或可能的键的列)完全依赖于主键。

在只有一个主键列的表中，这将始终成立，所以我们不需要强调这一点。但是，如果我们有一个组合键，我们可以打破这个规则。

例如，让我们将上一部分中的教师列添加到我们的`student_classes`表中。假设史密斯女士教数学，杰克逊女士教编程，詹姆斯女士教历史。

| 学生 id | class_id | 教师 |
| --- | --- | --- |
| one | one | 史密斯（姓氏） |
| one | Two | 杰克逊 |
| Two | three | 詹姆斯 |

教师列仅由 class_id 确定，*而不是由(student_id，class_id)的整个主标识符确定。*

## 问题

在我们讨论解决方案之前，让我们来看看这可能导致的问题。考虑一下普林斯是否也学过编程:

| 学生 id | class_id | 教师 |
| --- | --- | --- |
| one | one | 史密斯（姓氏） |
| one | Two | 杰克逊 |
| Two | three | 詹姆斯 |
| Two | Two | 杰克逊 |

注意到什么熟悉的问题了吗？我们有数据冗余问题。两次我们看到 Jackson 女士教编程，这也表明如果我只更改其中一行，我们可能会有更新异常的风险。

## 解

因为教师列只依赖于 class_id 列，所以我们应该将这些实体组合在一起。

让我们将教师列放在班级表中:

| 身份证明（identification） | 类别名称 | 教师 |
| --- | --- | --- |
| one | 数学 | 史密斯（姓氏） |
| Two | 编程；编排 | 杰克逊 |
| three | 历史 | 詹姆斯 |

到目前为止，我们已经在三个表中跟踪了很多内容，所以我们只看一个高级图表。大声喊出来 [dbdiagram.io](https://dbdiagram.io/) 做一个有用的工具:

[![](img/b7b9fb425f758608b021026379db8540.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A1DVyyhd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s2xwmgywczwly0f71x54.png)

2NF 很容易解释，但是现在我们已经有了一个设计得相当好的数据库。让我们回顾一下 2NF 之前的所有内容:

*   每个表都有一个主标识符。
*   每一列只有原子值。
*   没有表格有重复组。
*   没有部分依赖关系。

我们现在处于有利位置，因为我们知道如何确保这四件事。在下一篇文章的[中，我们将继续这个规范化的冒险，以符合第三范式。](https://dev.to/adammc331/effective-database-design-part-4-jbj)