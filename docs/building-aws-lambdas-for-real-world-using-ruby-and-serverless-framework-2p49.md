# 使用 Ruby 和无服务器框架为真实世界构建 AWS Lambdas

> 原文：<https://dev.to/jalerson/building-aws-lambdas-for-real-world-using-ruby-and-serverless-framework-2p49>

你可能想知道:"*为什么 AWS Lambdas 适用于真实世界？*”。嗯，自从[亚马逊在 11 月 29 日宣布 Ruby 支持 AWS Lambdas](https://aws.amazon.com/blogs/compute/announcing-ruby-support-for-aws-lambda/) 以来，我一直在阅读相关报道，因为我是一个 Ruby 爱好者。

目前，你可以很容易地找到一些解释如何在 Ruby 中构建自己的 Lambda 函数的博客文章和教程，其中大多数都使用了著名的 Hello World 作为例子，这是一个很好的起点，但是，说实话，你不需要构建像 Hello World 这样简单的东西。您将需要面对现实世界中关于自动化测试、使用其他服务、构建/部署、处理依赖性等问题。

在这篇文章中，我想和那些像我一样开始深入了解这个问题的人分享一些想法，并讨论如何使用 Ruby 和[无服务器框架](https://serverless.com)解决这些现实世界中的问题。请注意，我并不是说这些是最佳实践。正如我所提到的，我的目标是分享一些想法，并就此展开讨论。

我将使用一个应用程序作为例子来说明这些想法，这个应用程序是一个 [GitHub 应用程序](https://developer.github.com/apps/)，它将使用来自 GitHub 的数据并计算一些指标。第一步，也是本文的重点，是接收从 Github Webhook 发送的数据，并将其存储在 DynamoDB 表中。

# 应用结构

我们要解决的第一个问题是如何组织你的应用程序。我找到的很多指南都是把所有东西都放在根项目文件夹里就搞定了。我尤其不喜欢这种方法，我倾向于关注如何在多个子文件夹中组织我的项目，所以，从长远来看，这种组织可以持续下去，并避免一些令人头痛的问题。此外，由于你可能有几个项目，保持一个相似的结构将有助于你找到你要找的东西。如果你不在乎，可以跳过这一节。

据我所知，有三种方法来组织你应用程序。

1.  具有所有功能的单个项目和单个无服务器框架设置文件
2.  分成应用程序模块的多个项目，每个项目都有自己的无服务器框架设置文件
3.  多个项目，每个函数一个，每个项目都有自己的无服务器框架设置文件

我特别选择选项 2。我相信第一个选项将导致一个大的单个项目，这可能会让刚开始做贡献的新开发人员有点困惑，但是，这可能是跨不同功能构建集成测试的最简单的选项。另一方面，第三种选择可能会使这些集成测试更难实现，然而，新来的开发人员会有一个更小的项目需要理解。第二种选择有点两全其美。

我决定将第一个模块命名为“webhooks”。目前，我正在将我的应用程序与 Github 集成，但在未来，我可能会决定将它与其他东西集成。也就是说，Webhooks 项目具有以下结构。

```
├── app
    └── functions
        └── github.rb
    └── lib
        └── log.rb
    └── models
        └── github_event.rb
├── spec
    └── functions
        └── github_spec.rb
    └── support
        └── fixtures
            └── github
                └── events
                    └── push.json
    └── spec_helper.rb
├── serverless.yml
├── Gemfile and Gemfile.lock
├── Rakefile
├── Other files like .gitignore, .editorconfig, etc. 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我没有撒谎，我把我的项目组织在几个子文件夹中。如果您是 Rails 开发人员，您可能会注意到我正在使用 Rails 项目的类似结构。我喜欢 Rails 组织项目的方式，我也很熟悉这种方式，这让我更容易找到一些东西。

正如我之前提到的，大多数 Hello World 示例将所有文件保存在一个根项目文件夹中，这使得无服务器框架设置文件(`serverless.yml`)很容易引用 lambda 函数。

```
functions:
  myFunctionName:
    handler: <filename_without_extension>.<lambda_function_name> 
```

Enter fullscreen mode Exit fullscreen mode

然而，在我的例子中，包含我的 lambda 函数(`app/functions/github.rb`)的文件在多个子文件夹中，此外，它还是一个`Webhooks::Github`的类方法。