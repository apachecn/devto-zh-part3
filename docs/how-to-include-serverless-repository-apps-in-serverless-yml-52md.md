# 如何在 serverless.yml 中包含无服务器存储库应用程序

> 原文：<https://dev.to/theburningmonk/how-to-include-serverless-repository-apps-in-serverless-yml-52md>

在过去的一年中，无服务器应用程序库(SAR)服务有了很大的改进。我越来越喜欢它，并且自己也贡献了一些应用程序:

*   [**lambda-看门人**](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:374852340823:applications~lambda-janitor) : cron 作业删除区域中所有 Lambda 函数的旧的、未使用的版本，以释放存储空间。
*   [**auto-subscribe-log-group-to-arn**](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:374852340823:applications~auto-subscribe-log-group-to-arn):向 arn 的 Lambda 函数、Kinesis 流或 Firehose 交付流订阅新的和现有的 CloudWatch 日志组。
*   [**auto-set-log-group-retention**](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:374852340823:applications~auto-set-log-group-retention):将新的和现有的 CloudWatch 日志组的保留策略更新到指定的天数，以降低 CloudWatch 日志成本。

然而，CloudFormation 本身并不支持`AWS::Serverless::Application`资源类型。直到最近，我不得不使用 [SAM](https://github.com/awslabs/serverless-application-model) 部署框架，以便在我的堆栈中包含一个 SAR 应用程序。也就是说，直到 [1.41.0](https://serverless.com/blog/framework-release-v141/) 发布[无服务器](https://serverless.com/framework/)框架。

在这个版本中，无服务器框架增加了对 CloudFormation 的转换指令的[支持。这意味着我们可以使用与 SAM 相同的魔法源(云形成宏)!](https://github.com/serverless/serverless/pull/5997)

要将无服务器存储库应用程序添加到您的`serverless.yml`中，您需要:

1.  将`Transform: AWS::Serverless-2016–10–31`添加到您的`serverless.yml`的`resources`部分。这使得全局宏能够在整个 CloudFormation 堆栈上运行，并根据需要转换特殊资源。在这种情况下，它会将`AWS::Serverless::Application`资源转换成嵌套的 CloudFormation 堆栈。
2.  添加无服务器存储库应用程序作为额外的云信息资源。这些应该有资源类型`AWS::Serverless::Application`。

例如，要将`auto-subscribe-log-group-to-arn`应用程序作为 serverless.yml 的一部分，我需要以下内容: