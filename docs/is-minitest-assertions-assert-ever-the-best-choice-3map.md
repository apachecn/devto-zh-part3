# Minitest::Assertions#assert 是最好的选择吗？

> 原文：<https://dev.to/burdettelamar/is-minitest-assertions-assert-ever-the-best-choice-3map>

我正在写一些关于`Minitest::Assertions`的东西，这让我想知道:

*   有没有一个简单的`assert(test)`不能被更具信息性的断言所取代的情况？

例如:

*   `assert(5 <= 4)`仅报告`Expected false to be truthy.`

鉴于:

*   `assert_operator(5, :<=, 4)`报告`Expected 5 to be <= 4.`

我还没能想出一个我们能做到最好的例子。

想法？