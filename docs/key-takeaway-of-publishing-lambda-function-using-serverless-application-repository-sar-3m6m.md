# 使用无服务器应用程序库(SAR)发布 Lambda 函数的关键要点

> 原文：<https://dev.to/mengjiann/key-takeaway-of-publishing-lambda-function-using-serverless-application-repository-sar-3m6m>

无服务器应用程序存储库(SAR)是开发人员在 AWS 云中查找和部署现有应用程序的一种简单方式。更多信息，请参考[官方指南](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/what-is-serverlessrepo.html)。

现在更流行的用例之一是与社区共享 Lambda 函数。在 AWS SAR 上发布之后，任何人只需点击几下鼠标就可以将 Lambda 函数直接部署到他们的帐户中。

在 SAR 上发布有几种方法:

*   AWS 管理控制台
*   AWS SAM 命令行界面(AWS SAM CLI)
*   AWS SDKs 上传代码。

简单的方法是通过**管理控制台**来完成，以下是一些关键要点:

*   只有在`us-east-1`或`us-east-2`中创建的应用程序才允许共享/对公众可见。在其他地区创建的应用程序可以使用**账号**私下共享。`Semantic version`也是公开发表申请所必需的。

*   确保您已经创建了一个 bucket 来托管您的 Lambda 代码工件(archive.zip ),并使用以下内容修改了 bucket 策略:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service":  "serverlessrepo.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<your-bucket-name>/*"
        }
    ]
} 
```

*   SAR 上的描述和许可信息是必需的。

*   最重要的是创建一个有效的 AWS 无服务器应用程序模型(AWS SAM ),它定义了 AWS 资源。完整的文档在[这里](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md)。但是，这些是我的 Github 上的一些示例:
    - [示例 1](https://github.com/mengjiann/aws-lambda-sqs/blob/master/template-sar.yaml) -这包含了 Lambda 所需的多个角色策略的添加。`Parameters`在本示例中也用于接收 SAR 应用的输入。
    - [示例 2](https://github.com/mengjiann/aws-lambda-s3/blob/master/template-sar.yaml) -这包含创建一个 S3 桶，并将监听的`Events`添加到在 S3 桶中创建的对象。
    - [示例 3](https://github.com/mengjiann/ssm-parameters-getter-lambda/blob/master/template/sar-template.yaml) -这包含创建用于附加到 Lambda 函数的`Role`和`RolePolicy`资源。`DependsOn`也用于确保资源以正确的顺序创建。

*   一旦在 SAR 上发布了应用程序的新版本，用户需要使用最新版本重新部署应用程序。否则，它们将保留在旧版本上。

*   一旦一个应用程序使用 SAR 被**部署**，您就可以从云结构的 Stacks 控制台检查状态。您可以从那里删除应用程序，并检查部署是否有任何问题。

参考:

*   什么是 AWS SAR:[https://docs . AWS . Amazon . com/server lessrepo/latest/dev guide/what-is-server lessrepo . html](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/what-is-serverlessrepo.html)
*   如何在 SAR 上发布:[https://docs . AWS . Amazon . com/server lessrepo/latest/dev guide/server lessrepo-how-to-publish . html](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/serverlessrepo-how-to-publish.html)