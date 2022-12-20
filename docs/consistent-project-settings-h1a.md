# 一致的项目设置

> 原文：<https://dev.to/mayerj/consistent-project-settings-h1a>

在一个有许多项目的大型 C#解决方案中，如何为整个解决方案维护和实施一致的项目设置？

我在一个有 50 多个项目的 100 多万行代码库中工作。我们还有 60 名开发人员。在任何给定的时间，都有多个功能被开发，错误被修复，等等。虽然我们有严格的代码审查和反馈文化，但项目文件的更改偶尔会偷偷溜进来。项目设置在“指南”中定义，它描述了如何从头开始创建一个新项目，包括我们需要的所有定制构建目标和设置。然后签入将由团队领导和/或架构师审查。对我们模板的修正(或更改)是以一种特别的方式检入的，cookbook 也相应地更新。

依靠代码审查来捕捉设置中的错误并不是最佳选择。知道当前的设置应该是什么给评审者带来了很大的负担。此外，虽然在添加/删除文件时确实需要修改项目文件，但是很容易错过其他类型的更改。由于使用“旧”csproj 文件的项目需要添加/删除更新，所以根本不可能阻止修改。显而易见，我们需要一个更好、更一致的机制来执行设置，并且需要一个看门狗来提醒团队(或者更好的是，提醒引入它的开发人员),当一个设置与期望值有所不同时。巧合的是，团队刚刚在我们的 CI 构建中实现了签入。每个构建都将运行我们的单元和集成测试，只有当所有测试都通过时，才将变更集提交给 VCS。该团队认为，在设置发生变化时通知某人是不够的。如果一个项目文件以这样一种方式被改变，它不再匹配我们的模板，CI 流程应该拒绝签入。

输入没有创造性名称的单元测试“ProjectFileSettingsAreCorrect”。在 C#中。csproj 文件只是 XML。我们修改了构建过程，以提供(通过环境变量)源文件在磁盘上的位置。测试选择这个位置，然后找到所有的。csproj 文件递归。然后，我们一次加载一个文件，验证它们只包含预期的构建目标(Visual Studio 习惯于向解决方案中的所有项目添加新的构建目标，尤其是在添加新项目时。)在构建目标被验证之后，测试接着检查它们的设置，验证它们具有预期的设置。

单元测试已经运行了将近 10 个月了。由于它是签入入口的一部分，所以不可能意外地改变或引入不兼容的设置。这解放了评审者，让他们更多地关注变更集的内容，这意味着他们将更多的时间花在代码评审的有用部分。以项目文件变更为中心的评审意见实际上已经消失了。

该团队将这一原则应用于我们在评论中看到的其他几种常见错误。例如，我们现在验证 csproj 中列出的文件是否存在于我们的 VCS 中。我们还验证相反的情况，我们的源代码树下的代码文件存在于项目文件中(我们有一个固定的规则，如果文件没有包含在编译中，它应该被删除)。这是从 Visual Studio 解决方案资源管理器中删除文件与从源代码管理资源管理器中删除文件时的常见错误。我们验证项目在适当的地方使用 ProjectReferences 而不是 DllReferences，等等。

该团队还采用了一种“自动化评审评论”的策略，也就是说，任何可以以编程方式执行的评审评论都应该被执行。除了这些单元测试之外，我们还使用定制的 Roslyn 诊断分析器，在错误的上下文中立即向开发人员提供反馈。(分析器有一个额外的好处，可以选择提供一个建议的内嵌修复)。