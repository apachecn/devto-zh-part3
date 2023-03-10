# 开始使用 AWS 需要了解的内容

> 原文：<https://dev.to/hmlon/what-you-need-to-know-to-get-started-with-aws-4lff>

AWS 是最流行的云计算服务之一。AWS 是亚马逊网络服务的缩写，天哪，他们有很多服务。

我们每天使用的许多应用程序都托管在 AWS 上。其中包括网飞、Airbnb、Slack 等等。

也许你在一家使用 AWS 的公司工作，你想了解更多。或者您可能想将应用程序部署到 AWS。或者你只是在某个地方听到了 AWS，却无法从脑海中抹去。不管怎样，这是你需要知道的。

# 服务器

[![](img/6547bda46214c5ddbd7cf2a4256c679b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BZEnvunZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1484557052118-f32bd25b45b5%3Fixlib%3Drb-1.2.1%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

您的应用程序，无论是您自己的还是您工作的公司的，都可能运行在服务器上。当你开发应用程序时，你是在你的计算机上运行它。但是当您部署应用程序时，您希望在其他人的计算机上运行它。

AWS 可以提供那台电脑。它被称为[EC2](https://aws.amazon.com/ec2/)T2 或者弹性计算云。

此外，如果你对数据科学感兴趣，你可以在那里运行你的 Jupyter 笔记本。

# 数据库

[![](img/44aece7d7264cc3b38d467a410e29a99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XQZWP_9v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1507842217343-583bb7270b66%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2653%26q%3D80)

您的应用程序可能使用数据库。PostgreSQL，MySQL，甚至 Oracle，都无所谓。你需要知道的是，AWS 有一项针对数据库的服务，它叫做 [RDS](https://aws.amazon.com/rds/) **(关系数据库服务)**。

您可以在另一个 EC2 实例上运行您的数据库，就像在您的个人计算机上运行一样，但是 RDS 有很多优点。这里有几个例子:

*   成本效益
*   自动软件修补
*   易于扩展
*   自动备份
*   监视

你可以在这里熟悉完整的特性列表[。](https://aws.amazon.com/rds/features/)

# 部署

[![](img/cf960f676a5759f5bed219befb9e4542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5rDZHBr---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1544849250-82efaa1cd029%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D882%26q%3D80)

但是如何部署您的应用程序呢？在 AWS 上有很多方法可以做到这一点。但是如果你刚刚开始，我建议你使用 [EB](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html) **(弹性豆茎)**。

它允许您轻松:

*   [创建环境](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.environments.html),提供您需要的所有配置(EC2、RDS ),以及一些您可能一开始并不需要但仍然有用的配置(负载平衡器、监控、通知)
*   将部署到环境中
*   [检查日志和对服务器的 ssh](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli-troubleshooting.html)

EB 支持部署从 [Docker](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker.html) 到 [Java](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_Java.html) 到 [Ruby](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_Ruby.html) 的不同平台。你可以点击查看支持平台的完整列表[。](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.platforms.html)

如果你过去用过 Heroku，EB 和它很相似。与单独配置所需的每个 AWS 服务相比，EB 很容易设置。但还是没有赫罗库那么容易。

如果你想在提交 git 之后开始部署，你可以查看 AWS [CodeDeploy](https://aws.amazon.com/codedeploy/) **。**

# HTTP 路由

[![Road photo](img/b504e9dea70c8b71bf819a7adf1a1a23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e_A1c5Uq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1486674776040-2aeda9228e76%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2603%26q%3D80)

一旦部署了应用程序，您就需要将您的域流量路由到它。您可以使用 [Route53](https://aws.amazon.com/route53/) 来完成这项工作。

虽然当你用 EB 部署时，你可以得到一个像`http://your-application.elasticbeanstalk.com/`这样相当不错的域名，但它仍然有那种不是你的感觉。您可以直接在 Route53 上为您的应用购买域名。如果你是在 GoDaddy 或 NameCheap 等其他服务上购买的，你也可以将它委托给 Route53。您可以使用`dig`控制台命令来检查您的域是否迁移到 AWS。

```
dig your-domain.com 
```

Enter fullscreen mode Exit fullscreen mode

一旦你在 AWS 上有了你的域名，你需要添加一个别名记录到

*   EB 给你的域名(`[your-application.elasticbeanstalk.com](http://your-application.elasticbeanstalk.com/)`)
*   或者你的 EC2 的弹性 IP
*   或者您的负载平衡器 DNS 名称

如果你对“记录”这个术语不熟悉，我推荐你阅读[这篇关于 DNS](https://dev.to/swyx/networking-essentials-dns-1dl7) 的文章。

# 处理更多流量

[![Roads photo](img/76176f2485902883657b38c66ee4726e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CUi6HsX6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1530044426743-4b7125613d93%3Fixlib%3Drb-1.2.1%26auto%3Dformat%26fit%3Dcrop%26w%3D1934%26q%3D80)

在某些时候，您的应用程序可能会获得一些流量，而您的一个 EC2 实例将无法处理所有传入的请求。[ELBs](https://aws.amazon.com/elasticloadbalancing/)**【弹性负载平衡器】已经在 AWS 上拯救你了。**

 **负载平衡器将传入流量路由到不同的服务器。他们使用“循环”算法来实现这一点。循环算法非常简单，当一个请求进来时，负载均衡器将它发送到一个服务器，第二个请求进入第二个服务器。当所有的服务器都收到请求时，负载均衡器从头开始，再次向第一个服务器发送请求。

# 使其安全(HTTPS)

[![Locks photo](img/ed9d62f25987c5277312fe43c496b2cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ABRAmDlT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1506967726964-da9127fdec36%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2251%26q%3D80)

如果你想把 HTTPS 加入你的网站，你需要获得一个证书。可以在 [ACM](https://aws.amazon.com/certificate-manager/) **(AWS 证书管理器)**获取证书。一旦你得到了它们，你需要使用它们，它们不会自动应用自己。您可以[将证书添加到您的 EC2 实例](https://aws.amazon.com/premiumsupport/knowledge-center/connect-http-https-ec2/)。或者，如果您已经使用 EB 进行了部署，那么您应该已经获得了一个负载平衡器。在这种情况下，您可以将证书应用到负载平衡器，并且现在可以通过 HTTPS 服务请求。

然而，在这个简单的例子中，人们仍然可以通过 HTTP 访问您的应用程序。但是如果它们来自 HTTP，您可以通过重定向它们来强制它们使用 HTTPS。以前，您需要[设置 NGINX 或 Apache](https://www.keycdn.com/blog/http-to-https#5-add-301-redirects-to-new-https-urls) 重定向。但是最近，AWS 发布了应用程序负载平衡器，它们包含了使用一些自定义规则进行重定向的选项。你可以在这里阅读如何使用应用程序负载平衡器[设置 HTTPS 重定向。](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-cfg-alb.html#environments-cfg-alb-console)

# 存储文件

[![Files photo](img/74b0fb063a47bfc8896046a7981e8406.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n56v_iFD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1544396821-4dd40b938ad3%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2253%26q%3D80)

您经常需要在应用程序中保存文件。但是将它们存储在数据库中可能不是最有效的解决方案。AWS 有一个名为 [S3](https://aws.amazon.com/s3/) 的服务，代表**简单存储服务**。顾名思义，它只是存储数据。你可以在那里写任何你能想到的东西。它就像一个谷歌驱动器或 iCloud。

要在 S3 上存储文件，你可以使用适用于你的语言的 AWS SDK。你也可以找其他的图书馆，因为我确定还有更多。

# 发送邮件

[![Envelopes photo](img/40b6f09a1eb3826bcdd48c62d218b2e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VIM42PVr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1526554850534-7c78330d5f90%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2250%26q%3D80)

大多数应用程序做的另一件事是发送电子邮件。电子邮件已经失去了用途:时事通讯、系统通知等等。

要使用 AWS 发送电子邮件，您需要使用 [SES](https://aws.amazon.com/ses/) **(简单电子邮件服务)**。您可以使用您的个人电子邮件或为您的应用程序注册一个新的电子邮件，并从这些电子邮件发送电子邮件。这是一个很好的起点。

然而，从长远来看，从个人邮箱发送邮件并不是一个好的选择。一个更好的方法是获得一个域名，并从它发送电子邮件。为此，你需要[在 SES](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-domain-procedure.html) 中验证你的域名。之后，你可以使用支持多种编程语言的 AWS SDK 发送[电子邮件。或者你可以自己找一个图书馆或者创建一个。](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/send-an-email-using-sdk-programmatically.html)

# 结论

在本文中，我讨论了最流行的 AWS 服务以及为什么和如何使用它们。现在你应该知道你应该

*   在 EC2 上运行您的服务器
*   在 RDS 上运行数据库
*   使用 EB 部署到 AWS
*   在 Route53 中管理您的域
*   在 ACM 中获取 SSL 证书
*   通过 ELB 处理更多流量
*   存储 S3 的文件
*   用 se 发送电子邮件

当然，这个列表只是 AWS 服务的冰山一角，但它应该足以让你开始。

如果你喜欢这篇文章，一定要在下面留下你的评论，然后关注我。**