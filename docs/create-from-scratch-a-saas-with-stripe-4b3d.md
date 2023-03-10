# 从头开始创建一个带条纹的 SaaS。

> 原文：<https://dev.to/zenlicence/create-from-scratch-a-saas-with-stripe-4b3d>

如果你想创建一个**软件即服务**，那是因为作为一个企业家，你确信可以提供非物质化销售的潜力。为了增加他们，**自助服务**，**自动化**必须是你的优先事项，国际化是你的挑战。

Stripe 作为支付服务提供商，为发展在线业务带来了出色的解决方案。多亏了它的[组件](https://stripe.com/docs/stripe-js)、[主题](https://stripe.github.io/elements-examples/)和[示例](https://stripe-payments-demo.appspot.com/)，你可以创建令人惊叹的支付页面。但是要建立一个好的销售机制还有很长的路要走。

> 我们将努力帮助您了解如何充分利用 Stripe，以及在国际发展中需要考虑哪些方面。

## 付款方式

首先，关于你的支付页面，花点时间投资在 sources API 上，因为它的简单性和通用性，把你的精力集中在相关的支付方法上。

此外，确保了解针对欧洲客户的新法规[强客户身份认证 SCA](https://stripe.com/guides/strong-customer-authentication) SCA。

> 这项法律将于 2019 年夏天生效，要求你要求**两个独立的认证**要素来验证支付。不要忘记升级你的代码以避免支付问题，或者现在转向未来的[支付意图](https://stripe.com/docs/payments/payment-intents) API(测试版，但已准备好生产)。您可以通过订阅[这里](https://stripe.com/guides/strong-customer-authentication#signup)了解最新消息。

对于希望接受 SEPA 的欧洲公司，您现在可以直接从您的银行申请[债权人标识符](https://www.sepa.ch/en/home/direct-debits/creditor-identifier.html)(延迟 2 到 3 周)。

无论如何，为了正确处理支付失败，异步方法和更普遍大多数支付方法，现在强烈考虑实现 [Webhooks](https://stripe.com/docs/webhooks) 。

## 安全

接受在线支付的公司必须遵守 PCI 安全标准。在进行一些实时交易并完成自我评估问卷(SAQ)后，Stripe 将向您提供所需的合规证明。每年将对这一遵守情况进行评估。

为了确保不导致安全漏洞，Stripe 建议使用他们的 UI 元素和最新的 Stripe.js 库，永远不要将支付数据传输到您的服务器(这将是一种欺诈)，并且只使用与 **HTTPS** 的安全交换。

## 订阅

作为一个 SAAS 提供商，你肯定会喜欢重复计费。订阅为用户提供了更实惠的费用和更好的财务稳定性。

由于有了[计费 API](https://stripe.com/docs/billing/quickstart) ，您可以将大部分订阅逻辑直接迁移到 Stripe 中，避免繁琐的计算并自动开具发票。此外，为了管理与订阅相关的异步事件，您必须设置[计费 webhooks](https://stripe.com/docs/billing/webhooks) 。

为了改善您的自助服务，由于计费 API 只允许您导出逻辑，您仍然需要为您的客户提供专用屏幕来管理他们的订阅、[变更](https://stripe.com/docs/billing/subscriptions/changing)(升级/降级)和[取消](https://stripe.com/docs/billing/subscriptions/canceling-pausing)。

最后，如果您的功能环境允许，尝试将您的应用程序切割成付费的可选模块。这是引发额外经常性销售的有力方式。这个模块的概念在 Stripe 中并不存在，但是你可以同时创建多个订阅或者对 T1 收费。

## 税金

国际发展面临的最大挑战之一是根据不同的法规正确征税。

> 值得注意的是，Stripe 将计费 API 的税务处理完全委托给了商家。

对于**欧洲**，您必须确定您的产品是面向专业人士还是个人，在客户所在国家的条件下收取和申报增值税。商家可以使用一个集中的工具来集中申报欧洲的增值税。前往迷你一站式商店[莫斯](http://www2.impots.gouv.fr/e_service_pro/tva_miniguichet/moss_en.html#quanddeclarer)了解更多详情，并获得增值税 ID。

对于美国来说，一个类似的税收机制正在形成。确保了解未来法律对[经济纽带](https://blog.taxjar.com/economic-nexus-laws/)的影响，该法律将迫使在线服务提供商在客户居住的州收税和报税。尽管这项法律尚未生效，但预计美国各州将很快采纳。您总是可以将这些税收计算委托给诸如 Taxjar 或 Avalara 之类的提供商。

## 结论

Stripe 是一个优秀的工具，它随着法规不断发展。再加上其他服务，它可以在任何情况下满足您的需求。为了确保您的成功，请确保监控法律变更的影响，并且不要忽视提供可持续集成所必需的工作。