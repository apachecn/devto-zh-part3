# Hyperledger 结构项目的 CI/CD

> 原文：<https://dev.to/wealize/ci-cd-on-a-hyperledger-fabric-project-83b>

今天，我们将为您带来关于使用 Hyperledger Fabric 进行开发的这一系列技术文章的第二篇文章。

上次我们[谈到了我们如何测试我们的链码](https://dev.to/javaguirre/testing-chaincode-on-hyperledger-fabric-2j8k)，
今天我们更进一步，我们将向你展示我们如何自动部署代码。

[![](img/7f9c9ce634a63deccbaf7532d4e29527.png)](https://i.giphy.com/media/26DNdV3b6dqn1jzR6/giphy.gif)

CI/CD 是自动化测试我们的应用程序和将应用程序部署到我们的服务器的组合实践。

我们的工作流集成了我们管道中的不同平台，Github、Gitlab CI 和 IBM Blockchain。我们将代码存储在 Github 中，当我们将代码推送到 Github 时，它会自动转到 Gitlab CI，在那里执行自动化测试。如果一切顺利，我们会自动将代码部署到 IBM。

# 持续整合

持续集成是一种实践，它要求开发人员一天几次将代码集成到共享存储库中。每当代码被推送到代码存储库时，自动化测试就会被执行。

有许多很酷的 CI 服务，我们已经使用了其中的几个，现在我们对 Gitlab CI 很满意，它非常容易配置并且充满了可能性！

我们的 CI/CD 流程有两个阶段:测试和部署阶段。你可以有几个并行的阶段，在测试阶段我们运行三个过程，后端，前端和链码测试。当测试通过并且满足规则时，应用程序和链代码被*部署*。:-)

[![](img/aa730a02d39bd97561eb08f91ddca173.png)](https://i.giphy.com/media/KsCtl2h2RZKso/giphy.gif)

这里我们只向您展示 chaincode 的配置，因为应用程序的其余部分到处都有很好的文档记录(我们使用行业标准，如 Django、React 等……)。

在测试阶段，我们使用这种配置运行链码测试，步骤记录在代码片段中: