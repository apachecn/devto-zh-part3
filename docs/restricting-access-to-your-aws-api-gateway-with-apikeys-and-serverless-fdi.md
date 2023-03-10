# 使用 APIKeys 和无服务器限制对 AWS API 网关的访问

> 原文：<https://dev.to/dvddpl/restricting-access-to-your-aws-api-gateway-with-apikeys-and-serverless-fdi>

目前我正在做一个项目，其中一个 **React SAP** 连接到一个完全**的无服务器后端**:一些 AWS 网关、一些 Lambda 函数和一个 Aurora 无服务器数据库。该应用程序仅供公司内部使用，因此，我们在我们的 API 之上添加了 AWS Cognito 用户池(并允许使用 [Amplify](https://aws-amplify.github.io/) 在前端登录)。我将写一篇关于如何用无服务器框架设置 Cognito 用户池授权器的文章，但今天我只想快速展示如何使用 API 密钥快速限制对 API 的访问。

最近，我们收到了扩展我们的 API 的请求，并提供对我们的 DWH 运行的 ETL 的访问。ETL 是 Extract transformations Load 的缩写——简单地说，ETL 是一个脚本，它从数据源加载数据，对数据应用一些转换，然后加载/保存到其他地方。这些脚本可以由数据科学家手动运行，或者更有可能通过 cron 作业或预定的 lambda 函数定期运行。

在我们的例子中，ETL 也在内部运行，但来自另一个尚未加入无服务器冒险的部门，因此我们不能只从他们的 Lambda 中触发我们的 Lambda，并通过 **IAM 角色策略**管理一切。由于 ETL 不是由登录到我们的单页面应用程序的用户触发的，所以我们也不能使用用户池。

最快和最简单的解决方案是将 ETL 使用的特定端点设为私有，并向 ETL 所有者提供一个 API 密匙。

这被证明非常非常简单。
首先，我们检查了如何用 UI 控制台
[![API Key AWS UI console](img/273c9586d875552d067e46c5921f65a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mYMja1vw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.aws.amazon.com/apigateway/latest/developerguiimg/how-to-api-key-set-requirement-on-method.png) 做到这一点，但随后我们通过[无服务器框架](https://serverless.com/framework/docs/providers/aws/events/apigateway/#setting-api-keys-for-your-rest-api)实现了一切。
和往常一样，UI 过程中所有疯狂的按钮点击和对话框变成了一个非常简单的代码配置:

```
 my-lambda-function:
    handler: index.handler
    events:
     - http:
          path: api/my_endpoint
          method: get
          private: true 
```

Enter fullscreen mode Exit fullscreen mode

(除非你需要对你的 API 的使用进行限制和收费，因此你需要一个 [API 密匙和一个使用计划](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-create-usage-plans-with-console.html)，否则设置实际上只是将【T2 私有:真实】添加到你的 lambda 的 HTTP 事件中。

API 密匙将在部署期间生成(您可以从部署输出中记下它，或者在之后记下`running sls info -v`。

在本地测试时，请记住**无服务器离线会在每次**运行`sls offline start`时生成一个新的令牌。这个令牌是打印在屏幕上的，但是在测试或调试的时候，一直复制粘贴它并不方便。

你可以使用一个定制的令牌，这样你就没有时间从控制台上获取它。
我正在做的是编写一个代码(即 *q12w3e4r5t_offline_token* )，然后我将它作为环境变量传递给我的集成测试，或者保存到 Postman 中的测试集合。

只要运行

`sls offline start --apiKey YOUR_CUSTOM_TOKEN`

，在控制台中，你会看到你的令牌正在被使用`Serverless: Key with token: YOUR_CUSTOM_TOKEN`

一旦有了令牌，就只需要调用 API，在请求的 x-api-key 头中传递它。

`curl -X GET
'http://myapi/myendpoint?fromDate=2019-03-01&toDate=2019-03-27' -H 'x-api-key: YOUR_CUSTOM_TOKEN'`

在 Postman 中，您可以将其添加到 **Headers x-api-key**
下(如您所见，我还在 postman 中使用了 [env 变量，这样我就可以保存基于 localhost/dev/production 的端点和密钥，并且我的集合中的请求会自动更新)](https://blog.getpostman.com/2014/02/20/using-variables-inside-postman-and-collection-runner/) [![postman variables](img/be9f5b5e573560ff828e86247c7e0feb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wxM07Tgg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7oyjwmgof65esv8xakeg.png)