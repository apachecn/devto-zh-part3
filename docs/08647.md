# 快速修复自动映射器/xUnit 测试中的“映射器已初始化”错误

> 原文：<https://dev.to/cesarcodes/quick-fix-for-mapper-already-initialized-error-in-automapper-xunit-tests-6k8>

[![xUnit and Automapper error](img/4cc7a2f8be6a4cf17523ea8ca383a8be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kIrpwbYH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ycpwfh7528nslinae40d.jpg)

我的团队经历了几次这样的情况，在执行多个使用 AutoMapper 的 xUnit 测试时，我们的一些开发人员机器会遇到以下错误:

`Mapper already initialized. You must call Initialize once per application domain/process`

如果你谷歌一下，有一些建议的解决方案，但它们似乎对我们不起作用。因此，经过一些研究，我查阅了文档，并意识到根据我们的需要，我们可以关闭 [xUnit 并行化](http://xunit.net/docs/running-tests-in-parallel)。我认为对大多数人来说，这应该没问题，除非你正在运行大量需要很长时间的测试。
为了做到这一点，您可以简单地在您的主测试文件或者测试配置文件上设置下面的行(第 5 行):