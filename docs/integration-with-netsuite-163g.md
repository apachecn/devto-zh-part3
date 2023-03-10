# 改变我们与 Netsuite 的集成

> 原文：<https://dev.to/smith288/integration-with-netsuite-163g>

所以 Netsuite 决定他们将为登录实现 2FA。这对我来说相当困难，因为我们的集成使用了基本的用户名/密码授权头调用。本质上，在 Netsuite 中，您不能再使用用户名/密码进行 web 服务调用。

为了利用他们的服务，他们允许不同的认证方法。他们有一个 SOAP 服务，然后他们提供一个 RESTful 类型的方法，允许你写你自己的脚本和分配回调函数给动词。它实际上非常灵活和强大，可以做各种各样的动作。如果您不熟悉，我建议您查看 Netsuite 文档中的 RESTlets。

这让我想到了 Netsuite 的认证方法。您可以使用用户名和密码、会话 ID(实际上无法使用...但是能够)和 OAuth 1.0 集成到他们的 web 服务平台中。

由于设置简单，我们的许多合作伙伴都使用用户名和密码认证。显然，当密码过期或客户想要取消访问时，这可能会产生问题，但我们的计划作业会继续尝试从我们的平台内部访问 Netsuite，直到您无法访问为止。Netsuite 很快就会取消用户名/密码功能。

所以说了这么多，我决定从内到外工作。我将在 Netsuite 中使用各种 Suitescript 事件重写我的集成，并调用我们自己的 RESTful API 来接受数据。

这可能会给调试或故障排除任何可能的错误带来一些问题，但我们也有一个端点，我们将向其写入错误，这实质上是复制脚本的执行日志。除了 API 的无效许可密钥之外，我们应该能够解决大多数问题。

我将使用 UserEventScripts 来附加记录的操作，以触发 Map/Reduce 脚本来遍历未导入的记录，并使用我们自己的治理方法(根据客户承诺进行调整)来抑制我们这边的调用。

我还将使用一个公共的 Suitelet webhook，它只会唤醒用户，通过 RESTful api 检索信息来获取更新的信息，然后返回睡眠状态。干净。

通过结合使用 RESTlets、Suitelets 和 javascript，我将解决与无法访问所需记录的用户相关的权限问题、较慢的数据传输时间和笨拙的弹出窗口，这些弹出窗口会调用不熟悉的用户界面，可能会混淆用户体验。不需要使用 OAuth 就能得到一个好的、快速的、可用的解决方案。