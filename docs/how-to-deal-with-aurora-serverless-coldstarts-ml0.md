# 如何应对 Aurora 无服务器冷启动

> 原文：<https://dev.to/dvddpl/how-to-deal-with-aurora-serverless-coldstarts-ml0>

在我目前从事的项目中，我们正在将一个用 PHP 和 Vertica 数据库构建的 Web 应用程序迁移到 React SAP +无服务器后端(AWS API Gateway + Lambda + Aurora 无服务器)。

从 Vertica 到 Aurora 的转换是最后一步，从我们的集成测试来看，一切都很好。我们面临的唯一问题是这些测试“有时”会失败。
我们从一开始就非常确定这与 **Aurora 无服务器 DB“冷启动”**有关。我们已经应用了一些变通方法来避免 Lambda“休眠”,但是现在 VPC +数据库集群需要很长时间才能启动。

[![falling asleep](img/6d93119f1f350a2897edc4fe24099d75.png)](https://i.giphy.com/media/EjXDQt4vBq4MM/giphy.gif)

检查日志，我们发现 Lambda 在 5 秒后超时。简单。在 serverless.yml:
中增加超时即可

`timeout: 30 # vcp + paused Aurora cluster can take a while`

我们设置了一个非常高的值来排除 lambda 本身的任何超时。现在日志告诉我们 MySQL 驱动程序超时了。我们查看了 [mysql2](https://www.npmjs.com/package/mysql2) 的文档，发现它默认为 10 秒。
我们也增加了这一时间，但由于休眠的 Aurora 数据库集群可能需要 25 秒才能被唤醒...我们达到了网关 API 端点的硬限制。

[![screaming](img/2e64bc2d2134162c83a4c5e43a0751fa.png)](https://i.giphy.com/media/BS5naTw6nhlkI/giphy.gif)

为什么硬限制？因为 [AWS 告诉我们](https://docs.aws.amazon.com/en_us/apigateway/latest/developerguide/limits.html#api-gateway-limits)一个 API 网关在 29 秒后超时，并且超时不能增加(这非常有意义——你肯定不希望你的 RestAPIs 挂起这么长时间——如果他们真的需要这么多时间，可能是改变架构和转移到更异步的东西的情况)

因此..我们能做什么？

我们已经进行了预热，但这只是用来产生容器:handler 在检查上下文后立即返回(正如我在这里描述的)。
我们可以简单地修改逻辑，这样在预热时，我们可以 ping 数据库，唤醒它。
但更好的解决方案是禁用 Aurora Serverless 上的“暂停”功能，并将容量单位减少到 1，这样您的数据库集群永远不会进入睡眠状态，并且您始终至少有一个可用的 ACU，如果出现这种情况，AWS 会自动扩展它。
当然，这似乎在某种程度上违背了无服务器数据库的目的——在无服务器数据库中，您可以配置数据库在需要时自动伸缩，并且只为其使用付费:

> 如果因为启动时间太长而必须让一个实例一直运行，那么在 EC2 上使用 Aurora 有什么好处呢？

好处就是**自动扩展功能(跨多个可用性区域)**。

如果您不需要多个 AZ，并且对成本非常敏感，那么您完全可以使用预配的 aurora，而不是无服务器的。

[在这里](https://www.jeremydaly.com/aurora-serverless-the-good-the-bad-and-the-scalable/)你可以找到一篇关于 Aurora 无服务器的**成本与 EC2 上的 Aurora**相比的精彩而详细的文章。

在我们的案例中，多花一些欧元来获得更稳定的服务并没有什么大不了的，也不会疯狂地跟上 cronjobs 和 warmups，因此**我们决定让 1 个 ACU 在生产中始终保持活动状态，只需忍受试运行和开发中的冷启动**(任何 QA 测试人员只需刷新 ReactApp 页面就可以让连接运行——集成测试也可以这样做——重试或 ping DB——等待，然后执行它们)

## 如何在无服务器中设置这个配置？

在 AWS UIconsole 中，这很容易。只需单击“配置”选项卡-更改 ACU 和自动暂停字段，就大功告成了。

[![AWS UI Console RDS configuration](img/d518632ccb9972c2f2a8ec1c16869717.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--WvNnVhVJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q1dcaeykofxfv6h6192r.png)

对于无服务器框架，最困难的部分总是找到正确的属性来描述代码中的堆栈，并浏览大量的 AWS 文档。
您可以在 API 参考页面或 [AWS SDK 文档](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/RDS.html#modifyCurrentDBClusterCapacity-property)中阅读关于 DB 集群容量的[扩展配置，但是要在 yml 文件中找到正确的配置，您必须转到](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyCurrentDBClusterCapacity.html)[云形成文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-rds-dbcluster-scalingconfiguration.html)。一旦你到了那里，你会意识到它确实超级简单。

在刚刚放入的资源下:

```
 RDSCluster:
      Type: AWS::RDS::DBCluster
      Properties:
        MasterUsername: YOUR_DB_USERNAME
        MasterUserPassword: YOUR_DB_PSW
        DatabaseName: YOUR_DB_NAME
        Engine: aurora
        EngineMode: serverless
        ScalingConfiguration:
          AutoPause: false
          MaxCapacity: 64
          MinCapacity: 1
        DBSubnetGroupName: YOUR_SUBNET_NAME
        BackupRetentionPeriod: 1
        DeletionProtection: true 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 Aurora 无服务器数据库集群，它不能被删除，永远不会休眠，并且至少有 1 个 ACU。

但是在我们的例子中，我们希望每个不同的环境有不同的配置。我们不想浪费金钱在始终可用的 QA 和 DEV 实例上，也不需要这些环境的快照和备份。因此——因为条件在 yml 中并不存在——我们创建了一些自定义属性，并根据阶段引用它们:

在自定义下只需声明它们:

```
autopause:
    production: false
    default: true 
```

Enter fullscreen mode Exit fullscreen mode

在 DBCluster 配置中，只需这样引用它们:

```
AutoPause: ${self:custom.autopause.${self:provider.stage}, self:custom.autopause.default} 
```

Enter fullscreen mode Exit fullscreen mode

这就是如何在无服务器中基于阶段获取属性，如果阶段名不在属性中，则使用默认设置。

* * *

当您尝试配置时，一个很好的技巧是使用

```
sls print -s YOUR_STAGENAME 
```

Enter fullscreen mode Exit fullscreen mode

来看看最终的 yml 在所有变量都被解析后会是什么样子。

你对极光无服务器有什么问题吗，或者你对这个话题有什么有趣的建议吗？