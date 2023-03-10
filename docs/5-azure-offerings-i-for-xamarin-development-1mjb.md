# 5 Azure 产品在❤️用于 Xamarin 开发

> 原文：<https://dev.to/azure/5-azure-offerings-i-for-xamarin-development-1mjb>

我承认...我是❤️·沙玛琳，我是❤️·阿祖尔。所以我想有什么更好的方式来传播一些❤️for Xamarin，而不是谈论我在做 Xamarin 开发时❤️的五个 Azure 产品！

注册一个[免费 Azure 账户](https://azure.microsoft.com/en-us/free/?WT.mc_id=fiveazureforxamarin-devto-masoucou)，让所有这些服务都来一次旋转！

## Azure AD B2C

任何人都不会对 Azure 广告 B2C(t1)出现在我❤️.的 Azure 产品列表中感到惊讶毕竟，我在上面写过类似于 [13 部分文章系列](https://codemilltech.com/tag/azure-ad-b2c/)的东西，指导过一次 Xamarin 大学的客座演讲，更不用说还做过几次会议演讲。

但是我为什么这么喜欢呢？

首先也是最重要的，当与[MSAL.NET](https://docs.microsoft.com/en-us/azure/active-directory/develop/reference-v2-libraries?WT.mc_id=fiveazureforxamarin-devto-masoucou)结合使用时，它允许用户以一种非常简单的方式创建账户并登录你的应用。不仅如此，用户可以使用现有的社交账户注册，比如 Twitter 或脸书。这样他们就不必创建和记忆另一个账户。

然后，一旦您创建了帐户，整个世界的认证和授权为您打开。

此外，它与其他 Azure 服务无缝集成。你想锁定你的 Azure 功能吗？使用 Azure AD B2C 和 Functions portal 可以直接做到这一点。

## Azure 函数

说到 Azure 功能，这是我清单上的第二项。函数不是由 Xamarin 应用程序中托管的 SDK 使用的。相反，它们通常会被 HTTP 调用调用。

但令人惊讶的是，它们允许你为你的应用程序创建一个后端，而不必启动整个 ASP.NET web API 或 Node.js 后端，这些后端大部分时间都处于闲置状态，等待请求的到来。使用 Azure 函数，您可以创建一个...嗯，功能...奇异函数是端点。您不必担心托管基于 web 的 API 所带来的其他开销。(这就是无服务器一词的由来。)

但是让功能与众不同的是它们绑定到其他 Azure 服务。

*   需要运行一个特定的查询，从 [Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-cosmosdb-v2?WT.mc_id=fiveazureforxamarin-devto-masoucou) 中填充数据集，并将其作为传入参数发送吗？✅
*   需要写入一个 [Azure BLOB 存储](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-storage-blob?WT.mc_id=fiveazureforxamarin-devto-masoucou)？✅
*   甚至[写 SignalR 客户端](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-signalr-service?WT.mc_id=fiveazureforxamarin-devto-masoucou)？✅ ✅和✅！

查看[这里的所有绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?WT.mc_id=fiveazureforxamarin-devto-masoucou)。

有了这些附加到其他 Azure 服务上的能力，函数是驱动你的移动后端的一个惊人的方式！

## 天蓝色钥匙金库

下一个有点棘手。

首先让我设计一个场景...

假设你的老板给了你一个机会，让你开发一个你已经谈论了两年的应用程序。

你有两个月的时间来组装。所以你尽可能快地将数据库连接字符串、Twitter 密钥和其他任何需要保密的东西硬编码到一个配置文件中(但至少你没有把它推给 GitHub！)

> 嘿，你得完成它！

你做得非常好！如此之好，事实上，你得到 3 个以上的开发者！但是一个问题很快就出现了——你如何分发你的应用程序的秘密？当你的团队发展得更快时会发生什么？更糟糕的是，当 Steve——总是 Steve——在咖啡店里，有人给他的笔记本电脑屏幕拍了一张暴露了数据库连接字符串的照片，会发生什么呢？

这就是 [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault?WT.mc_id=fiveazureforxamarin-devto-masoucou) 派上用场的地方！这是一个保险库——存放你的钥匙——在 Azure 里。通过拥有您所有秘密的单点位置，它允许您在发生违规时快速循环或版本化密钥。

你所要做的就是告诉钥匙库-嘿，给我这把钥匙...它会把秘密值发送给你！

但这是棘手的部分。你真的不应该有一个秘密的设备，即使是在一个短暂的状态。您应该做的是请求一个访问令牌，将该令牌传递给 web 服务，一旦该服务验证您拥有访问权限，它就会获取密钥并执行它需要做的事情。

由于托管在 Azure 中，web 服务[将可以访问](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault?WT.mc_id=fiveazureforxamarin-devto-masoucou)密钥库(在完成一些配置之后)。

因此，密钥库-这是一个伟大的地方来存储您的应用程序的秘密。如果您选择在设备上访问它们，还有一个. NET SDK。但是您确实应该考虑进行身份验证，并将令牌传递给 web 服务(它使用 Key Vault SDK)，并让该服务根据秘密密钥获取任何信息。

尽管如此，只要有可能就使用密钥库来存储您的秘密！

## Azure 自定义视觉 API

你可能已经看过来自[认知服务计算机视觉 API](https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/home?WT.mc_id=fiveazureforxamarin-devto-masoucou) 的很酷的演示——它告诉你它正在看一个“桌子上的橘子”。

是的-那很酷。但是还有更好的东西。有一个 Azure 服务可以让你训练自己的视觉模型...这是机器学习的说法，告诉计算机你想让它识别什么类型的东西。

而这个服务就是 [Azure 自定义视觉 API](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/home?WT.mc_id=fiveazureforxamarin-devto-masoucou) 。

假设您想通过给植物拍照来识别它们的个体品种。

如果你使用认知服务计算机视觉 API，它只会告诉你它看到了一株植物。

然而，自定义视觉 API 会告诉你它发现了一个蕨菜！

现在，所有这些深入的信息都离不开它的[份工作](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier?WT.mc_id=fiveazureforxamarin-devto-masoucou)。为了让它区分植物的种类，你首先要加载该植物的许多不同的照片。然后*给每张照片贴上标签*，或者告诉机器这是什么。之后，你训练它。所以它开始学习如何识别哪些植物是哪些植物。

这可能需要更多的初始工作，但从长远来看，你得到的是一个更具体和定制的视觉模型，认知服务并为你提供开箱即用。

哦！你**不需要**调用 web api 来使用它们！您可以在设备上下载 TensorFlow、CoreML 甚至 Onnx(用于 Windows)模型，并以这种方式运行它们！这意味着快，快，快！对移动开发总是有好处的。

## 应用中心

这里我不打算说现有的 [App Center](https://docs.microsoft.com/en-us/appcenter?WT.mc_id=fiveazureforxamarin-devto-masoucou) 功能 [Build](https://msou.co/bq1) ， [Test](https://docs.microsoft.com/en-us/appcenter/test-cloud?WT.mc_id=fiveazureforxamarin-devto-masoucou) ， [Deploy](https://msou.co/bq3) ， [Crash](https://docs.microsoft.com/en-us/appcenter/diagnostics?WT.mc_id=fiveazureforxamarin-devto-masoucou) -a- [ltyics](https://docs.microsoft.com/en-us/appcenter/analytics?WT.mc_id=fiveazureforxamarin-devto-masoucou) ，以及 [Push](https://docs.microsoft.com/en-us/appcenter/push?WT.mc_id=fiveazureforxamarin-devto-masoucou) 。

相反，我要谈谈应用中心的未来。这是一个会让很多 Xamarin 开发者非常非常开心的未来！

你看，应用中心团队已经发布了他们 2019 年开发的前 6 个月的[路线图。](https://msou.co/bq7)

路线图上的一项是:创建下一代 Azure 移动服务。

> 应用中心将创造下一代 Azure 移动服务！

最棒的是，他们将在公开场合进行所有的规划和设计。你可以跟着[到这里](https://msou.co/bq8)。

更重要的是，开始留下你的评论，这样你就可以对即将到来的[身份](https://msou.co/bq9)、一个 [Cosmos DB 存储后端](https://msou.co/bra)等等的变化影响设计！

这是公开设计的，团队对社区协作 100%开放。请让他们知道你的想法！

## 总结一下！

这就是我❤️的五个伟大的 Azure 服务，让 Xamarin 开发者的生活变得更简单。

Azure AD B2C 可以满足所有用户的账户需求。Azure 的后台功能，你不需要做所有的工作。Azure Key Vault -确保这些秘密的安全！Azure 自定义视觉 API -检测高度特定的视觉模型。

当然——全新的应用中心——继续前进，帮助决定 Azure 移动开发的未来！

所以，用那个免费的 Azure 账户来试试这些服务吧！