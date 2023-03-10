# 将 Google reCAPTCHA v3 添加到您的 Laravel 应用程序

> 原文：<https://dev.to/adi/adding-google-recaptcha-v3-to-your-laravel-app-222h>

你好。我想和你分享一下，我是如何在我的 Laravel 应用中实现 Google 的 reCAPTCHA v3 的。

如今，保护 HTML 表单免受僵尸程序攻击是任何 web 应用程序安全性的重要组成部分。一个常见且简单的解决方案是实现一个验证码系统。Google 的 reCAPTCHA v3 让开发者和用户都可以轻松实现(使用 v3，用户不必选择图片来验证)。

让我们开始吧。

> 看看[LaravelCollections.com](https://laravelcollections.com/?utm_campaign=captcha-article)——越来越多的大型旅游资源

### 注册新站点

首先，我们需要在 [Google reCAPTCHA Admin](https://www.google.com/recaptcha/admin) 注册我们的网站，并获得使用密钥。当你添加你的站点时，也在域名列表中添加`localhost`，这将允许你使用相同的密钥进行开发。

[![Google reCAPTCHA admin console](img/72327b730c5393d812ddb5ddd0a2b2e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EMteGhz3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/18mCTiS.png)

提交这些详细信息后，您应该会看到为您的域生成了两个密钥。一个是在前端使用的`SITE KEY`，另一个是在服务器中使用的`SECRET KEY`。

### 前端设置

前端设置非常简单。选择您想要实现 reCAPTCHA 的页面，在我的例子中，我在页面上有一个联系表单。

下面的代码显示了一个联系人表单，它有一个名为`recaptcha`的隐藏输入(可以是任何名字)。然后在页面的末尾添加 reCAPTCHA 的 JS 库，一旦它准备好了，我们就得到一个令牌，并将其设置为`recaptcha`输入。

[![Frontend setup](img/03aca38a7b7ed592263f46f9f337078e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NzevEJRe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cPPlmWX.png)

### 后端设置

[![Backend Setup](img/8f7d5e24c7bf2ecc492d2bc79c6ba260.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gG3U5tCq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/bw8FGvx.png)

上面的代码检查当前请求是否来自机器人。我们通过从前端向 Google reCAPTCHA API 发送带有`SECRET KEY`和`TOKEN`的 post 请求来做到这一点。

结果 JSON 有一个属性`success`,当 Google 检测到来自机器人的请求时，该属性被设置为 false。下面是返回内容的示例。

[![JSON from Google Api](img/de7cb811676c39ba23165a0b6240d34a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SThi49Iv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VME4rmf.png)

### 优化设置

下面是你可以对上述设置进行的优化列表。

1.  将配置密钥移动到`.env`文件
2.  将验证逻辑移动到自定义验证器中。

这两者都包含在下面链接的教程中。

### 结论

这就是了。将 Google 的 reCAPTCHA 添加到您的应用程序中非常简单。我建议你把这个添加到所有可以被机器人访问的页面上。如果你有任何疑问，请在下面留下，我会尽力帮助你。

目前就这些。更多关于我在 [SimplestWeb.in](https://simplestweb.in/?utm_campaign=captcha-article)
查看我的副业-[LaravelCollections.com](https://laravelcollections.com/?utm_campaign=captcha-article)

相关教程

*   [https://medium . com/@ welcm/adding-Google-recaptcha-to-forms-in-laravel-4 be 8 a 4b 0 e 0 e 9](https://medium.com/@welcm/adding-google-recaptcha-to-forms-in-laravel-4be8a4b0e0e9)
*   [https://stevencotterill . com/articles/adding-Google-recaptcha-v3-to-a-PHP-form](https://stevencotterill.com/articles/adding-google-recaptcha-v3-to-a-php-form)