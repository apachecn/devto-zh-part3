# git 的防御子模块

> 原文：<https://dev.to/jessekphillips/defending-submoduals-for-git-4f5m>

子文档往往被认为是一个糟糕的解决方案，替代方案可能是包管理器(nuget)甚至 git 子树。

我先说理想。对于一个给定的程序，每个部分的所有源代码都保存在一个单一的存储库中，并且组织得很整齐。在这个理想的世界中，没有其他的程序，代码共享只存在于这个程序的不同组件中。(我不想让你想象什么样的代码库会是浏览器和虚幻引擎...这个世界只关心你今天写的标准 app)

为什么这是理想的？因为任何错误修复或功能都可以从一个位置完全控制。但是现实发生了，我们有很多理由分离我们的代码。

*   不同的语言需要不同的构建管道
*   构建时间
*   与其他应用程序共享
*   实施关注点分离

我想为你的内部和开发的组件提倡子文档而不是包管理器。如果您控制着源代码，并且它没有被其他人使用，您就不需要协调发布、设置内部软件包回购、扩展您的存储来永久保留官方发布。尤其是如果这些组件不能与它们所插入的部件分开进行彻底测试的话。

通过子模块，一个项目可以共享部分代码，代码可以根据每个项目的需要而改变和利用。是的，管理变更并向所有人传播补丁需要行动。每个替代方案都有相同的挑战，只是使用了不同的机制。

我真希望有更好的工具支持子模块，尽管 git 已经得到了适当的关注。