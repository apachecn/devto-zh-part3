# 以正确的方式将 Azure Key Vault 与您的移动应用配合使用

> 原文：<https://dev.to/azure/using-azure-key-vault-with-your-app-the-right-way-4b67>

[![Mobile Apps + Azure Key Vault ... Don't Do It!](img/b6989ed1646ed15b4e45a70e0b50f2b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XY4vSOkX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/code-mill-technologies-inc/image/upload/bo_1px_solid_rgb:000000%2Cc_scale%2Ch_600/v1552587811/PNG_image-EA4DAD32F0FB-1_cjnje3.png)

> 嘿！跟随这里的所有行动！首先，如果你没有 Azure 订阅，请点击这里获得免费订阅。接下来，您可以部署我将要谈到的所有资源，只需点击按钮下方的按钮即可。
> T3[T5](https://codemilltech.com/mobile-apps-azure-keyvault-dont-do-it/repository=https://github.com/codemillmatt/mobile-keyvault/blob/master/README.md#/form/setup)

不要在你的应用程序中硬编码连接字符串或者任何秘密！为了神圣的一切-加密这些秘密！

像我得到的大多数其他建议(牙线...运用...每周淋浴一次以上...)看似容易遵循的安全指南最佳实践并不经常得到遵循。

为什么？因为这是开发周期中额外的一步。为什么要经历加密、存储的麻烦，必须想出一种方法将我的应用程序的秘密分发给我团队中的其他开发人员...当我可以让他们坐在某个地方看纯文本的时候？有人反编译我的应用程序来获取它们的几率有多大？

就像当我可以无所事事，吃品客薯片，看《海绵宝宝》重播的时候，我为什么要运动？我今天死于心脏病的几率有多大？

[![Mobile Apps + Azure Key Vault ... Don't Do It!](img/e9336ac5cf651461c1331e34afed28e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K4SWxx-b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/code-mill-technologies-inc/image/upload/bo_1px_solid_rgb:000000%2Cc_scale%2Ch_500/v1552429919/PNG_image-EF444747D594-1_si76os.png)

良好的...最终运气会耗尽。有一天，即使应用程序没有被破解，我的应用程序的秘密也会发生一些不好的事情(就像我将秘密文件提交给 GitHub 一样)。就像有一天我会后悔错过所有的训练去观看黄金女孩的马拉松一样。(Lol...好像那会是一个遗憾。)

但是一定有办法的...必须有一种方法来保护应用程序的秘密，同时遵循最佳实践，*和*不会让我们的生活变得太困难...对吗？对吗？

## 进入 Azure Key 金库

Azure Key Vault 不仅提供了一个集中存储应用程序秘密的地方，还可以用于密钥和[证书管理](https://docs.microsoft.com/azure/key-vault/certificate-scenarios?WT.mc_id=keyvaultmobiledont-devto-masoucou)。

> 密钥库将所有秘密放在一个地方...几乎和纯文本文件一样好，但是有加密、授权和许多其他好处。😉

Key Vault secrets 本质上是一个键/值存储库——但是在它们上面有很多修饰。至少，您要在 Key Vault 中创建一个秘密，给它一个名称，然后在其中放置(secret)值。

[如文档所述](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview?WT.mc_id=keyvaultmobiledont-devto-masoucou)，数据/机密在静态和传输时都是加密的，微软永远不会看到其中包含的数据。你可以监控访问&使用的每一个秘密。对机密的访问由 Active Directory 控制。

您可以对机密进行版本控制——因此，如果连接字符串发生变化，您可以将现有值设置为旧值，并使用连接字符串创建一个新值，所有这些都在同一个键中。

您也可以禁用键，设置激活和停用日期。

密钥库中的每个秘密都有自己的 URI，可以被引用。

所以很酷，对吧？应用程序秘密在一个地方，默认加密，甚至还有微软提供的 SDK 来获取它们。

## 因此，这就是我们在这篇文章中要做的...

我们将从 Xamarin 移动应用程序开始。该应用程序将访问 Azure 表存储。获取表存储的 API 键将被硬编码到应用程序中。

我们希望将 API 密钥移动到密钥库中。但是我们不想在移动应用程序中有任何凭证或秘密。

因此，我们将创建一个函数应用程序来使用 Key Vault。(您将在下面看到我们如何解决凭证问题。)

然后，移动应用程序将调用该函数来获取数据。

事实上，任何人都可以对该函数发出 GET 请求，并接收回表的内容。

Azure 存储的 API 密钥在密钥库中受到保护，只能从 Azure 函数访问。由于函数只返回数据，我们不关心人们向表中插入数据。

如果您想锁定该函数，以便只有某些人可以访问它，或者根据登录者限制该函数返回的数据。查看我的关于 Azure AD B2C 认证和授权的系列文章。

我们走吧！

## 还是用钥匙金库吧！

所以我有一个应用程序，它从 Azure 表存储中提取一些数据。

这是 Xamarin。Forms 应用程序，它将在表中找到的数据显示在一个`ListView`中——看起来像这样:

[![Mobile Apps + Azure Key Vault ... Don't Do It!](img/36fdda05ab1315d845a119a71496ab7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2O4zHdIc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/code-mill-technologies-inc/image/upload/bo_1px_solid_rgb:000000%2Cc_scale%2Ch_700/v1552347777/Simulator_Screen_Shot_-_iPhone_XS_-_2019-03-11_at_16.39.25_okxs0b.png)

从表存储中获取数据的代码如下所示: