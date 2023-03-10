# 预热你的 Lambdas:时间表还是插件？

> 原文：<https://dev.to/dvddpl/warming-up-your-lambdas-schedule-or-plugin--flo>

一旦你将你的后端开发切换到无服务器，你将开始面临一个叫做冷启动的问题:你的 API 是很棒的，可伸缩的，而且通常也很快。只是有时似乎需要很长时间才能做出响应(从用户的角度来看，10 秒实际上是很长时间)。
由于其自身的性质，无服务器应用程序并不总是在运行，当它空闲了几分钟后，容器就会关闭。重新启动容器和重新初始化应用程序的所有组件所需的时间比简单地执行您的代码要长。这是冷启动。

减少冷启动的方法之一(即使这并没有真正解决问题，如果你想知道更多，请阅读崔琰的文章)是保持你的 lambda 温度。

[![keep warm](img/39c628981f40d300102bdbefa896cdf8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Jngn6fO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xo165l1pdvii0f8hqbc1.jpg) 
这意味着拥有一个调度程序，它每隔一段时间调用该函数以防止容器被破坏。

如无服务器所述，你可以快速配置一个[预定事件](https://serverless.com/framework/docs/providers/aws/events/schedule/)或者你可以配置[无服务器预热插件](https://github.com/FidelLimited/serverless-plugin-warmup)

## 有什么区别？

Schedule 创建一个 CloudWatch 事件，用您指定的速率或 cron 作业触发您的 Lambda。
这意味着对于你的 serverless.yml 中的每个 Lambda，你需要一个调度事件的配置，就像这样

```
 - schedule:
      name: your-scheduled-rate-event-name # optional
      rate: rate(10 minutes) 
```

Enter fullscreen mode Exit fullscreen mode

这可能很难维护——特别是如果您选择了多端点方法(其中每个资源都指向一个特定的 Lambda ),这也意味着每个 Lambda 都有自己的 CloudWatch 事件，对此，AWS 上似乎有每个帐户的限制:

> 每个 AWS 帐户最多可以有 100 个 CloudWatch Events- Schedule 源类型的唯一事件源。其中每个都可以是多达五个 Lambda 函数的事件源。也就是说，在您的 AWS 帐户中，您可以有多达 500 个 Lambda 函数可以按计划执行。
> [云观察事件](https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html)

WarmUp 也利用了 CloudWatchEvent，但是该事件只绑定到一个特意创建的特定 Lambda。这个取暖器存储了来自 serverless.yml 的所有 Lambdas 的数组，当被 CloudWatch 触发时，它将依次调用所有的 Lambdas。

在这两种情况下，处理程序中的有效负载将指定调用的来源，以便您可以立即退出并避免无用的计算或错误。
记得检查预热插件的事件源

```
module.exports.handler = async(event, context, callback) => {
    /** Immediate response for WarmUP plugin */
    if (event.source === 'serverless-plugin-warmup') {
        return callback(null, 'Lambda is warm!')
    }
    // do your stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

而像这样的预定事件:

```
module.exports.handler = async(event, context, callback) => {
  /** Immediate response for Ping from CloudWatch */
    if (event.source === 'aws.events' && event["detail-type"] === 'Scheduled Event') {
       return callback(null, 'Lambda is warm!')
    }
    // do your stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的项目中，我们选择了插件，因为它允许更容易和更干净的全局配置。

我们的 serverless.yml 最终看起来是这样的:

```
custom:  
    warmup:
        default:
          - production  
            # lambdas in dev and staging environment can even freeze... who cares. 
        schedule: 'cron(0/20  8-18:30  ?  *  MON-FRI  *)'  
            # since our API is used only from an internal company WebApp keep lambda warm only every 20 minutes in office hours.
        prewarm: true # Run WarmUp immediately after a deployment 
        concurrency: 2 # Warm up 2 concurrent instances 
```

Enter fullscreen mode Exit fullscreen mode

运行 sls deploy 时，确保插件读取配置并记录将要预热和预热的 Lambdas:

[![Found Lambdas to warm up](img/fb4e508082067541a27d426a6a5ec996.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HqR0vK1Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4oxf7qyf634apx8qccb.png)

[![Pre-warming after deployment](img/cda8cac2ec4046a8f3844b03bb8e1461.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C6sedfuY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0yfi7fp6awv47tcuvkc1.png)

之后，你可以检查你的 AWS 用户界面控制台，沿着你的 Lambdas，你会发现另一个 Lambda 描述无服务器预热插件。如果你打开它的代码，你会看到你所有的 lambdas 都将被预热，你也可以确保 CloudWatch 事件在那里，并正确配置:`Schedule expression: rate(5 minutes)`。

如果在终端你注意到 [![No Lambda to warm up](img/9d07592c2d8ad70cd06b615519f34832.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--One8AKrs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rbdoxncdtvu6ftnkz3jz.png) 那么就有问题了。就像我的第一次尝试一样:事实上，我是按照 Github repo 上的[指令和本文](https://github.com/FidelLimited/serverless-plugin-warmup#global-configuration)[中的](https://serverless.com/blog/keep-your-lambdas-warm/)进行的，我发现 info 在每个函数的配置中都有冲突，全局配置根本不起作用。
然后我[发现了这个问题](https://github.com/FidelLimited/serverless-plugin-warmup/issues/100)，并意识到插件目前在发布新版本时存在问题，Github 上 Master 的代码和自述文件比 NPM 上的版本更新，因此配置必须不同。

在新版本发布之前，只要坚持使用 NPM 上的[文档，然后如果你用 NPM 或 yarn 升级，记得在你的 serverless.yml 中重构它的配置](https://www.npmjs.com/package/serverless-plugin-warmup)

希望这有所帮助！