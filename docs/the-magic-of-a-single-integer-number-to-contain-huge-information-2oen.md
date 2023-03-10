# 单个整数包含大量信息的魔力

> 原文：<https://dev.to/haripriya4241/the-magic-of-a-single-integer-number-to-contain-huge-information-2oen>

这个博客是关于我们如何通过消耗更少的空间来有效地存储大量的信息。为此，这里以“如何在一个整数值中存储一个用户的所有角色”为例。开发应用软件时，程序员应该为用户编写一个处理角色和权限的模块。假设我们有以下角色。
[![](img/ac4104769ec8964084c47df91ac49750.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZKm3-jy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujmm4p3110gkhq25egf8.PNG)

程序员通常会给每个角色分配一个整数 ID，如下所示:

[![](img/6a8957cc1b19acff0568b747ac28dfb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x1E2f3PL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9rdf7tuw3z1znbadraug.PNG)

假设 ID 为 1 的用户只具有数据输入和查看的角色。
在这种情况下，程序员可以用两种不同的方式保存用户权限:
创建一个用逗号分隔角色的字符串，例如 6，7，这可以存储在列中。在检索时，我们可以用逗号分割字符串并找到角色 id。
通过使用每个角色 id 的每个列，可以根据用户 ID 存储每个角色 ID，如下所示:

[![](img/8a33b2a9c4e88009d870c65a41e901c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fnyQv4Bw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6u9uksluqsynxaglaj6q.PNG)

在这种情况下，我们需要一个单独的表来存储关于用户角色的信息。您想知道将用户角色的任意组合存储为单个整数值的另一种更好的方法吗？
阅读我们最新的博客，作者 Sukesh Chand，关于在单个整数值中存储大量信息【https://urlzs.com/7QWkj
T3