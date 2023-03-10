# FaaStRuby:无服务器功能相当于 Ruby 和 Crystal 的服务

> 原文：<https://dev.to/jgaskins/faastruby-serverless-functions-as-a-service-for-ruby-and-crystal-2849>

[![FaaStRuby Logo](img/4cbd18dda11c65269cc79e05d8435f40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7cz65RvS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p4vvxvj04x1jwoanh35q.png)

最近几个月，web 开发中的“无服务器”运动势头越来越猛。AWS Lambda 现在支持 6 种语言，去年年底宣布支持 Ruby:

> ![Alex Wood profile image](img/22fc45b6e1404fd7de6e0414d3af5997.png)亚历克斯·伍德@亚历克斯·伍德![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我很兴奋终于宣布了我在过去几个月里一直在做的工作。Ruby 对 AWS Lambda 的支持正式发布了！2018 年 11 月 29 日下午 17:47[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1068199855237910528)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1068199855237910528)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1068199855237910528)

它们还支持自定义运行时，所以你可以运行任何你喜欢的东西。我最近尝试了一下，并部署了一个水晶功能，但至少可以说很难。

首先，我必须通过无服务器框架运行它。如果你不熟悉它(像我一样)，你必须在 Linux 上运行它。它还需要与 AWS 进行大量集成，包括 CloudFormation、IAM 和 AWS 上的所有其他服务。正因为如此，他们的[介绍视频](https://www.youtube.com/watch?v=KngM5bfpttA)指示你给它管理员权限。当你刚刚起步，你的 AWS 账户上什么都没有的时候，这没什么大不了的，但是对于你现有基础设施的公司账户来说，这是一个可怕的建议。永远不要给任何工具你不控制王国的所有钥匙。

最后，部分是因为我拒绝给它完全的管理权限，我花了大约 4 个小时将一个单晶功能部署到 AWS Lambda 上——当它由于权限不足而不成功时，CloudFormation 回滚需要*foreee ever*并且我找不到任何东西来指出它到底需要什么权限。如果你已经使用了 Lambda 和无服务器，或者你有一个了解这些东西的运营或基础设施团队，我相信这将会顺利得多，但我没有这个特别的任务。我从零开始。

然后我找到了 FaaStRuby，其中[刚刚宣布支持水晶](https://faastruby.io/blog/faastruby-0-4-adds-support-for-ruby-2-6-0-and-crystal-0-27-0/)。在 10 分钟内，我从一无所有到拥有了一个 API 端点可用的 Crystal 函数。

FaaStRuby 的启动和运行非常快:

*   `gem install faastruby`
*   `faastruby create-workspace your-workspace-name`
*   `faastruby new your-function-name --runtime crystal:0.27.0`
    *   对于 Ruby 函数，可以省略`--runtime`标志，因为这是默认标志
*   `cd your-function-name`
*   用您自己的代码替换生成的`src/handler.cr`中的代码
    *   你可能想要写一些真正的规范或者删除生成的规范。他们生成的很可能会失败，除非规范通过，否则 FaaStRuby 不会部署——这是一件好事。
*   `faastruby deploy-to your-workspace-name`

这就是开始的全部工作——没有注册表格，没有需要处理的支付细节，只需安装一个 Ruby gem，运行一些 CLI 命令，编写几行代码，然后在[https://API . tor 1 . faastruby . io/your-workspace-name/your-function-name](https://api.tor1.faastruby.io/your-workspace-name/your-function-name)中点击您的函数。挺滑头的！

它似乎仍然是一个早期的服务，所以它没有 AWS Lambda 拥有的所有好东西，但如果你试图在 Ruby 或 Crystal 中快速部署云功能，它会做你需要的事情，而没有使用 AWS 带来的所有仪式。