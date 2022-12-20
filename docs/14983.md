# 一流的测试

> 原文：<https://dev.to/revskill10/first-class-testing-48fk>

几年前，我看到一些像 Ruby on Rails 这样的框架试图将`tests`文件夹与 dev codebase 放在同一个文件夹中。

这种做法有什么问题？

*   Gemfile 在测试、开发和生产依赖方面变得一团糟。
*   不鼓励依赖性去耦合，这意味着我们鼓励把所有东西放在同一个地方，以方便“目录遍历”
*   代码库现在更容易被“破坏”，因为出于某种原因，测试代码和开发代码都在同一个地方，所以它们必须互相取悦。

这种方法的主要缺点是:测试人员害怕编写测试！他们在接触主代码库！

我在其他语言的许多框架中也看到了同样的问题。

“停止将测试文件夹与开发文件夹放在同一代码库中”

那么“一流测试”是什么意思呢？

将您的测试文件夹视为与开发文件夹相同的角色，有其自己的依赖项、团队所有权、开发(测试是代码)、部署(测试是代码，因此可以为部署进行编译)

我对这种方法很满意，我发现测试更容易验证主代码库中的一些奇怪行为。

你最喜欢的测试策略是什么？

编辑:我的项目结构:

[![](img/b915d41bc8247776cc1c213552b4bcdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LsIIq8nV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jrd2cz3gdsejn7vgmcwk.png)