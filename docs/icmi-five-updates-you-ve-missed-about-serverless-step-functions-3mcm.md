# ICYMI:你错过的关于无服务器步进函数的五个更新

> 原文：<https://dev.to/theburningmonk/icmi-five-updates-you-ve-missed-about-serverless-step-functions-3mcm>

在过去的 6 个月里，我们已经使[无服务器步进函数](https://github.com/horike37/serverless-step-functions)插件变得更好更有用。以下是最有影响力的变化，以防你错过！

### 支持固有功能

长期以来，使用插件的一个主要痛点是您需要使用完整形式的 ARNs。从 [v1.18.0](https://github.com/horike37/serverless-step-functions/releases/tag/v1.18.0) 开始，您可以使用云形成固有函数`Fn:GetAtt`和`Ref`来表示`Task`状态。

这既适用于具有 Lambda 函数的`Task`状态，也适用于非 Lambda 服务，如 SNS、SQS、DynamoDB、ECS 和 Batch。

```
stepFunctions:
  stateMachines:
    hellostepfunc1:      
      name: myStateMachine
      definition:
        StartAt: HelloWorld1
        States:
          HelloWorld1:
            Type: Task
            Resource:
              Fn::GetAtt: [HelloLambdaFunction, Arn]
            End: true 
```

### 与非 Lambda 服务集成

说到非 Lambda 服务。Step Functions 在 re:invent 2018 上宣布[支持另外八个服务](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-step-functions-adds-eight-more-service-integrations/)(除了 Lambda)。从 [v1.10.0](https://github.com/horike37/serverless-step-functions/releases/tag/1.10.0) 开始，`serverless-step-functions`插件已经支持这一功能。

SNS 和 SQS 的例子在本例 [repo](https://github.com/theburningmonk/complete-guide-to-step-functions-examples) 中可用。它们也包含在我的视频课程[**AWS 步骤功能**](https://theburningmonk.thinkific.com/courses/complete-guide-to-aws-step-functions) 完全指南中。

要向 SNS 发布消息，您需要类似下面这样的东西:

```
stepFunctions:
  stateMachines:
    hellostepfunc1:      
      name: myStateMachine
      definition:
        StartAt: Publish SNS message
        States:
          Publish SNS message:
            Type: Task
            Resource: arn:aws:states:::sns:publish
            Parameters:
              Message: "{ \"answer\": 42 }"
              TopicArn:
                Ref: AlarmTopic
            End: true 
```

当您集成这些非 Lambda 服务时，插件会自动为执行角色生成相应的 IAM 权限。

目前支持 SNS、SQS、DynamoDB、ECS 和 Batch。

### 云手表报警

想要监控工作流的运行状况并在出现问题时得到提醒是很常见的做法。您现在可以使用内置的`alarms`配置为每个状态机生成一组默认警报。

```
stepFunctions:
  stateMachines:
    myStateMachine:
      alarms:
        topics:
          ok: arn:aws:sns:...
          alarm: arn:aws:sns:...
          insufficientData: arn:aws:sns:...
        metrics:
          - executionsTimeOut
          - executionsFailed
          - executionsAborted
          - executionThrottled
        treatMissingData: missing 
```

生成的 CloudWatch 警报将具有以下配置:

```
namespace: 'AWS/States'
metric: <ExecutionsTimeOut | ExecutionsFailed | ExecutionsAborted | ExecutionThrottled>
threshold: 1
period: 60
evaluationPeriods: 1
ComparisonOperator: GreaterThanOrEqualToThreshold
Statistic: Sum
treatMissingData: <missing (default) | ignore | breaching | notBreaching>
Dimensions:
  - Name: StateMachineArn
    Value: <ArnOfTheStateMachine> 
```

### 工作流执行事件

Step Functions 最近宣布[支持工作流执行事件](https://aws.amazon.com/about-aws/whats-new/2019/05/aws-step-functions-adds-support-for-workflow-execution-events/)。有了这个新特性，您可以通过 CloudWatch 事件设置工作流状态开始/完成/出错时的自动通知。可以将事件交付给 AWS Lambda、SNS、SQS、Kinesis 或 AWS Step 功能，以便自动响应事件。

使用`serverless-step-functions`插件，您可以设置当工作流状态变为`ABORTED`、`FAILED`、`RUNNING`、`SUCCEEDED`或`TIMED_OUT`时的通知。下面是如何配置`notifications` :

```
stepFunctions:
  stateMachines:
    hellostepfunc1:
      name: test
      definition:
        ...
      notifications:
        ABORTED:
          - sns: SNS_TOPIC_ARN
          - sqs: SQS_TOPIC_ARN
          - sqs: # for FIFO queues, which requires you to configure the message group ID
              arn: SQS_TOPIC_ARN
              messageGroupId: 12345
          - lambda: LAMBDA_FUNCTION_ARN
          - kinesis: KINESIS_STREAM_ARN
          - kinesis:
               arn: KINESIS_STREAM_ARN
               partitionKeyPath: $.id # used to choose the parition key from payload
          - firehose: FIREHOSE_STREAM_ARN
          - stepFunctions: STATE_MACHINE_ARN
        FAILED:
          ... # same as above
        ... # other status 
```

您还可以使用 CloudFormation 内部函数`Fn::GetAtt`和`Ref`来引用`serverless.yml`中的其他资源。

### LAMBDA_PROXY 请求模板

当您使用 HTTP 事件触发器时，默认的 API 网关请求模板只转发请求正文作为输入。

这意味着您丢失了大量与 HTTP 请求相关的上下文，比如 HTTP 头以及路径和查询字符串参数。要访问这些信息，您必须创建一个定制的请求模板。

此外，它与我们在用 API Gateway 和 Lambda 构建 API 时所期望的事件有效负载有着明显的不同。

所以我们更新了 HTTP 事件触发器，并让您选择 LAMBDA_PROXY 请求模板。

```
stepFunctions:
  stateMachines:
    hello:
      events:
        - http:
            path: posts/create
            method: POST
            request:
              template: lambda_proxy 
```

当您使用 LAMBDA_PROXY 集成方法时，这为您提供了一个与 API Gateway 的事件有效负载非常接近的近似值。但是，多值查询字符串参数目前不起作用。这是因为 API Gateway 没有将它们传递给集成请求。一旦这个问题得到解决，我们将增加对它的支持。

### 其他变化

这是过去 6 个月中最值得注意的 5 个变化。总结一下，这里有几个你可能会感兴趣的小变化:

*   [支持全局标签](https://github.com/horike37/serverless-step-functions/issues/182)
*   [为预定事件定制 IAM 角色](https://github.com/horike37/serverless-step-functions/commit/f5362f4)
*   [支持依赖](https://github.com/horike37/serverless-step-functions/issues/158)

如果您有兴趣了解更多关于阶跃函数的知识，以及它在更广泛的解决方案中的位置。请查看我的课程-[AWS 步骤功能完整指南](https://theburningmonk.thinkific.com/courses/complete-guide-to-aws-step-functions)。

帖子[theburningmonk.com](https://theburningmonk.com/2019/05/icmi-five-updates-youve-missed-about-serverless-step-functions/)最先出现在[ICMI:你错过的关于无服务器步骤功能](https://theburningmonk.com)的五个更新。

[![](img/c27f8830d62b672b40ed6e9054d114ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rLDrmNRj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/theburningmonk/%257E4/Edcz8ICPhyU)