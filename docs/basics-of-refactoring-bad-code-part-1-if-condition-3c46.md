# 重构坏代码的基础|第 1 部分- If 条件

> 原文：<https://dev.to/immathanr/basics-of-refactoring-bad-code-part-1-if-condition-3c46>

我们都遇到过这样的情况，当我们作为开发人员成长时，我们经常发现自己写的代码看起来不正确，或者我们怀疑它是否遵循了最佳实践。本文只是总 **n** 系列的**部分 1** 。(我真的不知道会有多少人来🤷‍♂️)

### If 语句

If 语句可能是编写糟糕代码的根本原因。在编写嵌套 if 条件之前，总是要三思是否有办法简化它。写得不好的`if condition`会降低程序的可读性。
这里先做如下假设。任何干净的代码都将具有以下属性。

*   易读的
*   简明的
*   可扩展——应该能够允许添加更多的条件或业务逻辑，而不影响以上两个因素。

错误代码示例 1