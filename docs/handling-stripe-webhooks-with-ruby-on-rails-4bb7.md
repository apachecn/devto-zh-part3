# 用 Ruby on Rails 处理 stripe webhooks

> 原文：<https://dev.to/maxencehenneron/handling-stripe-webhooks-with-ruby-on-rails-4bb7>

当在 Ruby on Rails 应用上接受支付时，如果你想知道 stripe 上发生的每一个动作，你必须实现 stripe [webhooks](https://stripe.com/docs/webhooks) 。

我发现了一个处理它们的好方法，我将在本文中分享。

我*强烈*不鼓励你通过添加一个简单的 POST 路径到/webhooks 来自己实现它们，因为:

*   在接受它们之前，您需要检查 stripe 的签名(有人可能试图冒充 Stripe！)
*   有一些宝石可以简化这项任务

在本教程中，我们将使用一个名为 [stripe_event](https://github.com/integrallis/stripe_event) 的 gem

## 我们开始吧

### 安装依赖项

第一步是将宝石添加到您的宝石文件中。如果你是 ruby 新手，这个文件位于你的应用程序的根目录下。

```
gem 'stripe_event' 
```

然后，在/config 中的 routes.rb 文件中，添加下面一行:

```
mount StripeEvent::Engine, at: '/stripe-webhooks' #you can change this url 
```

这将创建一个 POST 路由来处理所有的 webhooks。我们将在条带控制面板中完成所有设置后，马上实施该操作

### 设置网页挂钩

首先，如果您想在您的开发环境中尝试 webhooks，您将需要使用类似于 [ngrok](https://ngrok.io) 的工具。

然后，在你的 Stripe 仪表盘中，导航到 Developers -> Webhooks 或者点击这个[链接](https://dashboard.stripe.com/account/webhooks)。

最后，切换到“测试数据”并添加一个新的端点。在“要调用的 URL”中，添加您的 ngrok URL，后跟您之前选择的路由名称。

一旦你创建了你的网页挂钩，请记下该网页挂钩的签名密码。
[![Signing secret](img/82dd61ceb685424da542ee94d0ddc136.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--RHrGeFe2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/45jfebj2hw7fubtzn8ms.png)

### 添加凭证

我们现在将条带凭证添加到(相对)新的 [rails 加密凭证](https://www.engineyard.com/blog/rails-encrypted-credentials-on-rails-5.2)

要编辑凭据，请键入以下命令:

```
EDITOR=nano rails credentials:edit 
```

并写下下面几行:

```
stripe:
  development:
    publishable_key: 'pk_test_'
    secret_key: 'sk_test_'
    signing_secret: 'whsec_'
  production:
    publishable_key: 'pk_live_'
    secret_key: 'sk_live_'
    signing_secret: 'whsec_' 
```

publishable_key 和 secret_key 是你的条纹密钥，你可以在你的账户设置里找到，signing_secret 是我们之前生成的秘密。

### 配置条带 _ 事件

在 config/initializer 中创建一个名为“stripe_events.rb”的文件，并粘贴以下代码

```
Stripe.api_key = Rails.application.credentials.stripe[Rails.env.to_sym][:publishable_key]
StripeEvent.signing_secret = Rails.application.credentials.stripe[Rails.env.to_sym][:signing_secret]

StripeEvent.configure do |events|
  events.subscribe 'invoice.', Stripe::InvoiceEventHandler.new
end 
```

前两行是根据您当前的环境(开发或生产)设置正确的令牌

在最后三行中，我们告诉 stripe_event 订阅所有以“invoice”开头的事件并将它们重定向到名为“InvoiceEventHandler”的类

下一步是实现这个类。

在您的 app 文件夹中，创建一个名为“services”的新文件夹，并在其中添加一个名为“stripe”的文件夹。

然后，在我们刚刚创建的条带文件夹中添加一个名为 invoice_event_handler.rb 的文件。

```
module Stripe
  class InvoiceEventHandler
    def call(event)
      begin
        method = "handle_" + event.type.tr('.', '_')
        self.send method, event
      rescue JSON::ParserError => e
        # handle the json parsing error here
        raise # re-raise the exception to return a 500 error to stripe
      rescue NoMethodError => e
        #code to run when handling an unknown event
      end
    end

    def handle_invoice_payment_failed(event)
    end

    def handle_invoice_payment_succeeded(event)
    end
  end
end 
```

这里，我实现了两个事件:invoice.payment.failed 和 invoice.payment.succeeded。(鸣谢: [@aradilopez](https://dev.to/arandilopez/efficent-webhook-handlig-with-ruby-on-rails-4pj) )

就是这样！

在我的各种项目中，我将所有有用的通知重定向到一个空闲通道，所以我总是知道发生了什么。