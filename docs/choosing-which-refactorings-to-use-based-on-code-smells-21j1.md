# 根据代码味道选择使用哪种重构

> 原文：<https://dev.to/programmingdecoded/choosing-which-refactorings-to-use-based-on-code-smells-21j1>

本周我完成了精彩的互动课程[深入重构](https://refactoring.guru/refactoring/course)，该课程基于马丁·福勒的名著《重构》。课程结束时，我甚至获得了一张不错的证书

[![](img/b9efba5b94999f937536efe46507f793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kUUwUIoO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/02/refactoring-certificate.png)

但是，如果一个人不知道如何将课程应用到自己的代码中，那么学习课程又有什么用呢？本课程介绍了 66 个重构，显然太多了，无法一次全部应用！最初我打算把这篇博文的标题定为“我最喜欢的重构”，但是后来我意识到它们中的大多数都是基于一些代码气味，这也是本课程所介绍的(确切地说是 21 种)。

所以我的第一步是决定我想要重构的代码库中最糟糕的代码味道。

这些是:

–长方法

原始的痴迷

–长参数列表

–条件复杂性

令人欣慰的是，该课程提供了一个方便的备忘单，告诉你对于某种代码味道使用哪种[重构](https://refactoring.guru/refactoring/catalog)。基于这一点，我选择目前专注于以下重构:

–提取方法

–用对象替换数组

–分解条件

–删除参数的赋值

–用方法对象替换方法

–用类别替换类型代码

–用符号常数替换幻数

–保留整个对象

–引入参数对象

–用保护子句替换嵌套的条件子句

–引入空对象

当然，有用的重构技术不止 11 种，但是很难把它们都记在脑子里，我喜欢从小的步骤开始。老实说，许多重构实际上应该是常识，但是当一个人受到时间限制时，他很容易养成坏习惯，例如，向一个方法添加另一行而不是提取它。

通过学习这门课程，我了解到在重构的过程中有可能引入新的、不同种类的代码味道，所以我将不得不在以后的某个时间再次回顾各种技术。

这门课程的创始人 Alexander Shvets 最近也出版了一本名为《深入设计模式》的书，我已经开始阅读了。我喜欢他解释如何以及为什么使用各种技术的风格。使用设计模式是编写更易维护的代码的下一步。