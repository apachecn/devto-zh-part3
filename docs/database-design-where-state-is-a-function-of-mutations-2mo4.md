# 状态是突变函数的数据库设计。

> 原文：<https://dev.to/itsarnavb/database-design-where-state-is-a-function-of-mutations-2mo4>

我沿着 redux 的思路思考，在 redux 中，状态是初始状态的函数，而突变是由 redux 执行的。

好处是突变可以捕获大量的信息，随着需求的变化，还原体可以被修改产生新的状态。

此外，我们有一个内置的历史。

这是数据库设计中的常见模式吗？

具体来说，我正在考虑在 Firestore 上实现这一点。我应该知道什么？