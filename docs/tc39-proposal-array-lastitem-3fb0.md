# TC39 建议:Array.lastItem

> 原文：<https://dev.to/tombarr/tc39-proposal-array-lastitem-3fb0>

最近的 TC39 提案提供了对 JavaScript 数组中最后一个元素更容易的访问。虽然从表面上看这似乎并不重要，但这样的提议对 T2 来说有很多好处。

终端元素的独特之处在于它们比中间元素更容易被访问。正因为如此，许多其他脚本语言如 [Ruby](https://stackoverflow.com/questions/18212240/ruby-convention-for-accessing-first-last-element-in-array) 和 [Python](http://knowledgehills.com/python/negative-indexing-slicing-stepping-comparing-lists.htm) 提供了方便的方法和属性来访问和修改它们。

在 ES6 中做到这一点的唯一方法是使用索引。但是，即使像交换两个数组中的最后一个元素这样简单的任务也会很快变得难以阅读。