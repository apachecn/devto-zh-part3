# 你好，无服务器飞机和电动飞机

> 原文：<https://dev.to/jalerson/hello-serverless-with-jets-and-dynamoid-3fi8>

Jets 是一个 Ruby 框架，它允许我们轻松地创建和部署无服务器应用到 AWS。Jets 采用了 Rails 的一些概念和模式，这使得 Rails 开发人员更容易采用无服务器架构。

Jets 正处于成熟的早期阶段，然而，它的创建者，Tung Nguyen，正在努力改进这个框架，围绕它的社区也在成长。

我们将使用的示例应用程序与之前使用 Ruby 和无服务器框架为真实世界构建 AWS Lambdas 的应用程序相同:一个简单的应用程序，它将使用 Github 发布的数据。点击这里[可以获得这个样本项目。](https://github.com/jalerson/jetsapp)

就像[李罗伊·詹金斯](https://www.youtube.com/watch?v=mLyOj_QD4a4)会说:*好了，时间到了！我们开始吧！*

# 先决条件

显然，安装喷气式飞机是必要的。如果你没有，去你的终端安装它:

```
$ gem install jets 
```

Enter fullscreen mode Exit fullscreen mode

为了将您的应用程序部署到 AWS，您需要在`~/.aws/credentials`中设置您的 AWS 密钥。如果没有，请参考[配置和凭证文件](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)。

# 创建我们的 Jets 应用

由于我们对资产服务不感兴趣，让我们使用 API 模式创建我们的应用程序:

```
$ jets new jetsapp --mode=api
$ cd jetsapp 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Jets 通过 [Dynomite](https://github.com/tongueroo/dynomite) 支持 [ActiveRecord](http://rubyonjets.com/docs/database-activerecord/) 和 DynamoDB。然而，我在这里对一些不同的东西感兴趣:我想使用 [Dynamoid](https://github.com/Dynamoid/dynamoid) ，这是一个用于 AWS DynamoDB 的稳定而成熟的 Ruby ORM。

也就是说，让我们更新我们的 Gemfile，添加 Dynamoid 并删除 mysql2。

```
# gem "mysql2", "~> 0.5.2"
gem 'dynamoid', '~> 3.1.0' 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记通过在终端中执行`bundle install`来更新您的依赖项。

Dynamoid 需要 AWS 凭据才能正确连接到 DynamoDB。如果我们在 Rails 中，我们可以很容易地使用[初始化器](https://guides.rubyonrails.org/configuring.html#initialization-events)来完成。幸运的是，[初始化器是 Rails 的 Jets](http://rubyonjets.com/docs/initializers/) 采用的很酷的东西之一。

因此，我们只需要创建`config/initializers/dynamoid.rb`并添加以下内容: