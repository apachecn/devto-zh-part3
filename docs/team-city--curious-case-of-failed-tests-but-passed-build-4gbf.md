# 团队城市——测试失败但构建通过的奇怪案例

> 原文：<https://dev.to/ankitvijay/team-city--curious-case-of-failed-tests-but-passed-build-4gbf>

最近，我写了一篇关于我如何设法关闭整个组织的[构建管道的帖子。](https://dev.to/ankitvijay/when-i-brought-down-build-pipeline-for-entire-organization-57kh)虽然那段时间是我的错，但这次应该是曼城队承担责任。

### 背景

我最近为我们的新创建了一个 CI/CD 渠道。NET 核心项目。作为构建管道的一部分，我有通常的构建步骤来构建解决方案，运行单元测试，运行集成测试，然后部署到 Octopus。

### 团队城市 dotnet CLI 插件

正如我之前在 Team City 上的帖子中提到的，我已经尽可能地避免使用 Team City 插件，而是使用脚本或命令行。为了运行我的新项目的测试，我的首选是利用现成的“dotnet”命令，而不是使用 Team City dotnet 插件。但是， **[dotnet test](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test?tabs=netcore21)** 命令默认不带多个项目列表*。显然有很多方法可以解决这个问题，但是由于简单易用，我选择了 Team City 插件。配置简单明了，我在几分钟内就启动并运行了。*

[![](img/f2b75c64dc1d2244de2fcb6681068488.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29C7DqsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/ankitvijay.net/wp-content/uploads/2019/03/image.png%3Ffit%3D810%252C261%26ssl%3D1) 

<figcaption>团队城市 dotnet CLI 插件</figcaption>

### 一期

我们的 CI/CD 渠道似乎像预期的那样运作，没有任何戏剧性事件。一旦公共关系分支机构获得批准，主分支机构的建设是触发和成功的建设，解决方案将部署到八达通。在一个晴朗的日子里，我们的 QA 报告了一个 bug，这个 bug 本应该被我们的集成测试发现。当我查看 Team City build 时，我注意到即使集成测试失败，构建也是成功的。这在很多方面都很奇怪，我仔细检查了构建配置，并验证了它们都设置正确。所有构建步骤的“执行步骤”都被设置为“如果前面的所有步骤都成功完成”。但是在*失败的*构建步骤之后的构建步骤似乎仍在执行。

### 根本原因

进一步挖掘后，我发现问题的根本原因是 Team City dotnet 插件上的这个问题。还有其他像我一样猝不及防的开发者。根据插件贡献者之一的说法:

> 这是原始行为。对于大多数运行程序，进程返回非零退出代码，这意味着运行不成功。对于像 NUnit、VSTest、dotnet test、msbuild /t:VSTest、dotnet vstest 这样的运行者来说，正退出代码只是大量失败的测试。详见此[螺纹](https://youtrack.jetbrains.com/issue/TW-49018)

然而，对我来说，这个解释不足以修复插件中的错误行为/错误。这种行为并不直观，会导致像我和其他开发人员一样的问题。

如果你想让 JetBrains 优先考虑这个问题，你可以在这里投票[。](https://youtrack.jetbrains.com/issue/TW-55626)

### 工作区

针对此问题，建议的解决方法之一是将每个构建步骤的“执行步骤”设置为“仅当构建状态为成功时”。但是我不喜欢这个解决方案，因为它意味着我们会无缘无故地脱离默认的,而且我们需要记住*,每当我们添加一个新的构建步骤时，还要改变默认的*。

我通过在我的 Team City 构建配置中添加以下“失败条件”,成功地解决了这个问题。

[![image](img/7c32e40310c6cd207e30ff3a9609dc32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YtkJB0cK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/user-images.githubusercontent.com/5988564/53308876-44dab080-38f0-11e9-9575-1657d0d60757.png%3Fw%3D810%26ssl%3D1)

每次测试构建步骤失败时，构建日志消息都会包含文本“测试失败”,这就是此失败条件的作用。

希望这个建议能帮助你避免犯同样的错误，并节省几个小时。🙂

帖子[团队城市-测试失败但建造通过](https://ankitvijay.net/2019/03/25/team-city-failed-tests-but-build-pass/)首先出现在[嗨，我是 Ankit](https://ankitvijay.net) 。