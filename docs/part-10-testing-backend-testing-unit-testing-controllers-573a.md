# 第十部分。测试:后端测试—单元测试—控制器

> 原文：<https://dev.to/carlillo/part-10-testing-backend-testing-unit-testing-controllers-573a>

这篇文章是我描述上下班打卡系统的一系列文章的一部分，如果你想了解更多，你可以阅读下面的文章:

*   第一部分。上下班打卡系统:图表。
*   第二部分。上下班打卡系统:基本后端— AuthModule。
*   第三部分。上下班打卡系统:基本后端—用户模块。
*   第四部分。上下班打卡系统:基本后端应用模块。
*   第五部分。打卡系统:种子数据库和迁移数据
*   第六部分。上下班打卡系统:基本前端。
*   第七部分。打卡/下班系统:使用 docker/docker-compose 部署后端(nestJS)。
*   第八部分。上下班打卡系统:使用环境部署前端(角度 2+)。
*   第九部分。测试:后端测试—单元测试—服务
*   第十部分。测试:后端测试—单元测试—控制器
*   第十一部分。测试:后端测试— E2E 测试
*   第十二部分。测试:前端测试—单元测试
*   第十三部分。测试:前端测试—集成测试

## 简介

这是第一个关于测试的帖子，也可能是第一个关于**质量评估** (QA)的帖子。这个项目从一开始就没有使用**试驾开发** (TDD)进行开发，但我目前正在测试阶段。感谢测试阶段，我发现了很多小错误，如果这个项目已经投入生产，这些小错误可能会成为一个大问题。事实是，该项目将在接下来的几周内进入生产模式。这些测试对于修复这次发现的几个 bug 会非常有用。

测试的第一步是决定我应该测试什么？任何人都可以对你说，你必须测试整个应用程序，你必须获得接近 100%的覆盖率，但事实是你不需要测试整个应用程序，但你必须测试软件的最关键部分。你的软件的这些部分的价值可能接近 90%或 70%，这取决于你的应用程序。

在我们的例子中，我将描述我们应该测试:

服务:

*   app.service。

*   用户服务。

*   授权服务

控制器:

*   app.controller

*   用户.控制器

因此，在我们的项目中，不需要测试 d to、常量、实体和模块，因为这些测试很难，价值也很小。

后端使用 NestJS 框架开发，该框架使用 Jest 作为测试工具。此外，NestJS 包括一个强大的测试包，它模拟了一个类似于角度测试包的环境。

## 控制器测试

在这篇文章中，我将描述控制器单元测试。该测试是服务单元测试之后的**测试金字塔**中最简单的测试。我对测试领域的初学者的建议是，您应该从服务的单元测试开始，因为这些都是小功能，它们有独特的任务，很容易被隔离。然后，测试世界的下一步是您进行控制器单元测试，因为控制器经常调用服务方法。

[![](img/0157d464cb9591ba3db9ee874c76114c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ktUdklAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2A_7U7tlqjeoW1KMAL.png)

## App 控制器

我们要测试的第一个控制器是 app.controller.ts，它使用了一个服务:AppService。因此，我们的测试套件必须检查 app.service 将使用正确的参数调用方法。

第一步包括我们将要开发的每个测试的初始配置。因此，app.controller.ts 在其构造函数(AppService)中需要一个服务，该服务将是一个间谍。来自@nestjs/testing 的 Testpackage 提供了 createTestingModule 方法，该方法创建了一个要测试的测试模块。在这个 testingModule 中，providers 数组由 AppController 和一个 spy 组成，该 spy 使用一个工厂来拦截 AppService。以下代码向您展示了这一初始配置:

[![](img/93adfce0334ea2db8b3b8c6a5d54d165.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ubbyrtw6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2472/1%2AmnAWlmH__ytrbUlFm1s9jw.png)

下一步包括知道我们想要测试什么。主要思想是独立于任何其他函数/方法来测试每个函数/方法。所以，下面这些方法就是 app.controller.ts 的代码。

[![](img/1b79320e438d956b42aed86a328af02c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i1hC0wVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2908/1%2AE6zv0xfGQWTu8U7lhM97nw.png)

authIn、authOut 和 usersTicketing 方法应该检查是否使用正确的参数调用了 appService。在我们的例子中，测试是单元，因此，这些方法不应该使用真正的函数/方法来调用，这就是为什么我们对这些方法使用 spy 的原因。测试函数的代码如下:

[![](img/faf3d81155e52d9f71112ced9b35db05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VFhnzCCO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2908/1%2AWr9RBL0dL8boj6HkdtGPOA.png)

在前面的测试中，您可以注意到 expect 与方法 authIn、authOut 和 usersTicketing 相关，这些方法检查这些方法是否被调用以及参数是否正确。在这些方法中，方法 authIn 或 authOut 中抛出的错误是不相关的，因为在这些方法中，责任被委托给 AppService。

## 用户控制器

测试用户控制器的过程与在 app.controller.ts 中使用的过程相同，因此，第一步是创建测试模块，该模块包含将在下面的测试中使用的 spy 和控制器。

[![](img/19f598350f13e010ff7cb4ade1bc034b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cf7QjUwK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2840/1%2AzRudABziFoCouCEWrdYs5Q.png)

这些方法非常容易测试，因为使用的技术与 app.controller.ts 中的技术相同。因此，要测试的代码如下:

[![](img/35b825b8efaff3009b1862fc327c4833.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lHMCIOEa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3112/1%2AEUpC2qOimODLuaOJm5-Jng.png)

它的测试套件只检查调用 getUsersWithoutKey 和 addUser 方法时是否使用了正确的参数，如下面的代码所示:

[![](img/c0d7ee106a3cb443772b04e5ee9162de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EswgQPwl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2776/1%2AKSHKB6sn-Bi3HtkKfi0OFQ.png)

## 结论

在这篇文章中，我解释了如何使用 jest 和 NestJS 框架测试后端控制器。如果你知道间谍的工作原理，这些测试就很简单。事实上，控制器经常调用服务的方法。

在下一篇文章中，我将向你展示如何对后端进行 e2e 测试。

*   GitHub 项目是[https://github.com/Caballerog/clock-in-out](https://github.com/Caballerog/clock-in-out)。

*   这个帖子的 GitHub 分支是[https://GitHub . com/Caballerog/clock-in-out/tree/part 9-back end-unit-test](https://github.com/Caballerog/clock-in-out/tree/part9-backend-unit-test)。