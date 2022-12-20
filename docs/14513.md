# 如何阐明数组差异？

> 原文：<https://dev.to/burdettelamar/how-to-elucidate-array-differences-2oi>

应该在第一时间把[这个](https://dev.to/burdettelamar/lucid-array-differences-1b5e)贴给`#discuss`。

所以现在我在这里贴上之前的帖子，求教讨论。

* * *

正如承诺的那样，我正在寻找将`Array`解释添加到 gem [minitest_lucid](https://rubygems.org/gems/minitest_lucid) (文档在 [GitHub](https://github.com/BurdetteLamar/minitest_lucid#minitest-lucid) )。

到目前为止，对`Hash`、`Set`和`Struct`有用的解释已经非常简单明了，对`OpenStruct`也是如此。

对于`Array`，就没那么多了。

规定:对于小数组来说，这些都无关紧要，它们的差异很容易直观地评估。更复杂的比较只适用于更大的数组。

是的，这是矫枉过正——直到它不是。

*对于失败的断言:*

*   *多少信息就够了？*
*   越多越好。
*   *太多才刚刚好。*

我能想到一些有用的数组处理方法，但是每种方法的有用性很大程度上取决于实际数据的性质:

*   逐元素比较。
*   差异-LCS 比较。
*   缺失元素:`expected - actual`。
*   意外元素:`actual - expected`。
*   常见元素:`actual & expected`。
*   所有元素:`actual | expected`。

我将详细介绍前两个。

### 逐元素比较

在这个比较中，`actual[0]`与`expected[0]`进行比较，`actual[1]`与`expected[1]`进行比较，...`actual[n]`比作`expected[n]`。

在以下情况下，这种比较是理想的:

*   数组大小相同。
*   大部分元素都是一样的。

在以下情况下，比较可能仍然有用:

*   数组大小相似。
*   相同的元素越来越少。

### 不同 LCS 比较

" [Diff::LCS](https://github.com/halostatue/diff-lcs#description) 使用麦克洛伊-亨特最长公共子序列(LCS)算法计算两个可枚举序列之间的差。"

匹配算法擅长寻找匹配的序列，即使它们不在数组中的相同位置。

在以下情况下，这种比较是理想的:

*   这些阵列有多个匹配序列。
*   实际的数组有一些插入。
*   实际阵列有一些删除。

在以下情况下，比较可能仍然有用:

*   阵列具有较少的匹配序列。
*   实际数组有更多插入。
*   实际阵列有更多删除。

### 说明输出格式

我开始把解释放入失败断言的信息中。

因为可能会有更多的阐释数据，而不是更少，我正在尝试 HTML 中的附加输出，其中颜色和链接可以帮助组织和阐明。

### 最后，`minitest_lucid`该怎么做？

如果`minitest_lucid`实现了这些策略中的几个，默认情况下应该返回所有策略的信息吗？如果没有，默认是什么？是否应该有一个非违约选项？

* * *

我将感谢你对这些复杂性的思考，特别是如果你现在或将来是 gem [minitest_lucid](https://rubygems.org/gems/minitest_lucid) 的用户。

非常感谢！