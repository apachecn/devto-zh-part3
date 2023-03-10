# 在口袋中即时获得最新剧集

> 原文：<https://dev.to/m1guelpf/getting-the-latest-episodes-instantly-in-pocket-casts-4956>

*累得不想看书？改为听这篇文章！*

[https://blogcast.host/embed/531](https://blogcast.host/embed/531)

我用口袋模型作为我唯一的捕手。这很棒，但我注意到有时剧集需要一段时间才能出现。例如，有人在推特上发布了他们播客最新一集的链接，但它不会出现在应用程序中，即使我点击了刷新。

这是因为 Pocket Casts 会缓存您订阅的所有提要，并定期刷新它们。当你点击应用程序中的刷新按钮时，它只会获取最新的缓存版本，而不会重新获取。

幸运的是，我设法想出了一个解决方案，可以破坏特定播客的缓存。这一变化不仅会影响你，所有 Pocket Casts 用户都会在他们的 podcatcher 中看到新的一集。

要使用我的技巧，您首先需要找到您想要刷新的播客的 RSS 提要的 URL。这通常可以在播客网站上找到。一旦你有了它，使用像 Bitly 这样的网址缩写来创建一个更短的链接。这将通过 Pocket Casts 的重复检查，使其认为这是您正在添加的新提要。

最后，转到 [Pocket Casts 的提交页面](https://pocketcasts.com/submit)并粘贴缩短的 URL。您应该会立即看到新剧集出现在您的应用程序中！