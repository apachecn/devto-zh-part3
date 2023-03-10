# 基于云的销售点应用的兴起

> 原文：<https://dev.to/nphaskins/the-rise-of-cloud-based-point-of-sale-applications-5c9>

早在 2010 年，当伊桑·马科特写下[响应式网页设计](https://alistapart.com/article/responsive-web-design)的帖子时，我立刻意识到这一格局将永远改变。当时我正在一个伪 html-flash 平台上建立网站。我立即改变了方向，因为我知道在可预见的未来，响应式网页设计将会改变我们开发网站的方式。

八年后，响应式网页设计已经成为标准实践。

我的观点并不是我可以预测未来，而是随着 [Stripe 终端](https://stripe.com/terminal)的即将发布，我们开始进入一个类似的时期。ICYMI，Stripe 发布了一款名为 Terminal 的产品(仅限邀请测试版)，这是一款读卡器，提供用于 web 应用的 JS 驱动的 SDK，以及用于移动应用的 iOS SDK。我已经[将条纹终端](https://dev.to/nphaskins/how-i-integrated-stripe-terminal-with-ruby-on-rails-33pg)集成到我的健身房管理软件平台[运动管家](https://sport-keeper.com)中，计划于今年年初推出。

Stripe Terminal 提供了一个真正的 JS 驱动的 SDK，专为构建网站应用程序而设计。这是一个巨大的时刻，因为当前的解决方案是基于 PC 的，并且许多通过 iframes 安全地连接。或者，他们使用 USB 读卡器和 JS 来解析信用卡数据以便输入到表单中。

当您使用 Stripe 及其托管字段时，这将不起作用。此外，它很容易出错。通过提供基于 Javascript 的 SDK，我们可以 100%安全、无缝地集成到用户界面中，而用户界面只受我们想象力的限制。

未来几年将是激动人心的快节奏，因为我们将看到一系列新的创业公司推出 IPO 产品，旨在取代从健康和健身到餐饮等众多行业中老化的硬件和软件。