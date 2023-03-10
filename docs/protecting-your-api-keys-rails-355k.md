# 保护您的 API 密钥- Rails

> 原文：<https://dev.to/torianne02/protecting-your-api-keys-rails-355k>

从需要隐藏 API 密钥到应用程序授权密钥和秘密，`dotenv`是您想要用来保护项目凭证的 gem。作为开发人员，您不希望您的个人项目凭据被那些派生或克隆您的项目的人使用。

API 密钥是易受攻击的，因为大多数 API 都要求它们允许接收每个请求。以下是保护 API 密钥的一些原因:

*   有些 API 只允许你一天/月/年调用这么多 API，然后让你付钱继续使用 API。

*   有些 API 会限制您在特定时间段内可以拨打的电话数量。

*   API 密钥可能会因为超出其服务条款的使用而被暂停。

这就是 dotenv 发挥作用的地方。dotenv 到底是做什么的？作为一个亮点，“dotenv 在引导环境时将变量从一个. env 文件加载到 env 中。”通俗地说，dotenv 获取我们放在. env 文件中的变量，并通过调用`ENV[‘EXAMPLE_KEY’]`来访问它们。我们保护这个。env 文件和其中的密钥，方法是将它放在我们的。gitignore 文件。现在，让我们进入 dotenv 的安装和使用说明。

## 安装

将这一行添加到应用程序的 Gemfile 中:

`gem ‘dotenv-rails’`

然后在你的终端里运行`bundle install`。

创建您的。项目目录根目录下的 env 文件。您可以手动或通过在终端中运行`touch .env`来完成此操作。

## 用法

把你的证件写进去。包封/包围（动词 envelop 的简写）

`EXAMPLE_API_KEY=YOURAPIKEYHERE`

在加载应用程序时，这个文件中的变量将在 ENV 中可用。您可以通过以下方式呼叫他们:

`ENV[“EXAMPLE_API_KEY”]`

最后，您不希望忘记保护这些 API 键。确保包括。env 在你的。gitignore 文件！！！！

好了，您已经准备好使用 dotenv gem 来保护您的 API 密钥了。

编码快乐！

### 资源

[API 密钥保护](https://www.approov.io/api-key-protection.html)
[最简单强大的红宝石——Dotenv](https://medium.com/coffee-and-codes/the-simplest-and-powerful-ruby-gem-dotenv-74d64cbc5d5d)
[Dotenv 文档](https://github.com/bkeepers/dotenv)