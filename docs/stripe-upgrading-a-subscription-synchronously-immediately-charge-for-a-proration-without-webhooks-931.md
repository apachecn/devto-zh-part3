# 同步条带化升级订阅(无需 webhooks，立即按比例收费)

> 原文：<https://dev.to/risafj/stripe-upgrading-a-subscription-synchronously-immediately-charge-for-a-proration-without-webhooks-931>

我在做一些关于升级订阅如何在 [Stripe](https://stripe.com/) 上工作的研究。Stripe 的默认系统是在下一张发票中向客户收取按比例分摊的费用，以及下一个计费周期的费用([文档](https://stripe.com/docs/billing/subscriptions/prorations))。

例如，如果我在账单周期的中点将我的订阅从标准计划(每月 5 美元)升级到高级计划(每月 10 美元)，我在下个月第一天的发票大约是 12.50 美元。下个月收费 10 美元，我半个月前开始使用的高级计划部分 5 美元，升级后标准计划未使用部分 2.50 美元退款。

但是这个计费系统可能会让客户感到困惑，我想知道是否有一种方法可以让客户立即按比例付费(在上面的例子中是 2.50 美元)。没有`prorate_now`标志或任何东西，但它可以手动完成。这里是高层次的步骤和要点，带你通过它。代码很可能需要根据您的用例进行定制。

注意:我按照文档的建议使用了 Stripe 的测试环境。

# 先决条件

*   通过将下面的代码行添加到您的`Gemfile`并运行`bundle`，将`stripe` gem 添加到您的 Rails 应用程序中。

```
gem 'stripe' 
```

Enter fullscreen mode Exit fullscreen mode

*   将您的 Stripe 可发布密钥和秘密密钥分配给环境变量，并创建`config/initializers/stripe.rb`(您可以从 Stripe 仪表板获得您的密钥):

```
Rails.configuration.stripe = {
  publishable_key: ENV['STRIPE_PUBLISHABLE_KEY'],
  secret_key: ENV['STRIPE_SECRET_KEY']
  }

Stripe.api_key = Rails.configuration.stripe[:secret_key] 
```

Enter fullscreen mode Exit fullscreen mode

*   在 Stripe 测试环境中，您需要创建至少一个[产品](https://stripe.com/docs/api/service_products/create)，两个[计划](https://stripe.com/docs/api/plans/create)，以及一个订阅较低层计划的[客户](https://dev.toacreate)。这些可以从条带仪表板创建，或者通过向它们的 API 发送请求来创建；我已经在前一句话中链接了进行 API 请求的相关文档。

*   客户还需要有一张信用卡，这样就可以向他们收费(不是真的信用卡，而是用于测试目的的模拟卡)。如果您通过 API 创建了客户，那么可能会自动为您创建一个模拟卡。您可以通过访问仪表板并检查该客户的详细信息来确保这一点。

# 高等级步骤

1.  创建预览发票，模拟客户现在升级后的下一张发票
2.  根据预览发票计算分摊成本
3.  用该金额创建新的一次性发票
4.  立即向客户收费
5.  检查发票是否已支付，然后在禁用按比例分配的情况下升级客户(否则，他们将被收取两次升级费用！)

注意:创建预览发票并不创建实际发票，因此您不必担心意外向客户开出账单(在[文档](https://stripe.com/docs/api/invoices/upcoming)中解释)。

# 要旨作为概念的证明

下面的测试遵循我上面描述的步骤。客户订阅了我的更便宜的计划，每月 100 日元，但他们想升级到 1000 日元的计划，所以我们在升级之前向他们收取差价。我的 Rails 应用程序中还有一个`User`模型和一个`Plan`模型(它们分别对应于 Stripe 中的 customer 和 plan ),我偶尔会在测试中引用它们。

现在是真正的代码: