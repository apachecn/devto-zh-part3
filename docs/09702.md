# 什么是 if 语句？

> 原文：<https://dev.to/codetips/what-is-an-if-statement-22h8>

> 用编程术语来说，if 语句是一个条件语句。当条件满足时，它执行一个动作(或代码块)

[![What is an if statement?](img/318ecb5d122ca97261cc61d657324319.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p7pUZqjA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/03/if-statement_walk-outside.jpg)

如果你曾经问过自己，应用程序或网站如何决定一个视图是否应该显示；有可能会有一个`if`声明。

一个`if`语句是根据一个条件决定某个代码块是否应该被执行的最基本的方法。

你可以把它比作做一个简单的决定: ***“我是不是该出去走走？”**T3】*

如果这个问题的答案是 ***是的*** 你必须穿上鞋(这是条件满足时需要执行的代码块)。因此，从程序的角度来看，它可能是这样的:

```
if (takeWalkOutside) {
    // put on your shoes
} 
```

如果你决定不出去(不满足`takeWalkOutside`条件)，穿鞋子的动作(If 语句内部的代码)被跳过。