# 你把 Lambda 函数的凭证放在哪里？

> 原文：<https://dev.to/dvddpl/where-do-you-keep-credentials-for-your-lambda-functions-5dno>

如果你的 Lambda 函数必须访问一个数据库(或者任何其他需要凭证的服务),你在哪里以及如何存储那个配置？

最近，我们一直在迭代我们的 MVP，我们的应用程序的需求和规模有所增长，我们一直在讨论如何安全地处理不同环境/阶段和相关用户/密码的数据库配置。

有很多很多可能性，让我们来看看其中的一些:

### 只要把主机、用户、密码硬编码在你的文件里就行了。

[![nope](img/da32e1059ec0538a65750dc2da47b188.png)](https://i.giphy.com/media/3o7btT1T9qpQZWhNlK/giphy.gif) 
请不要。我真的应该告诉你为什么吗？

### 使用一个. env 文件——它被提交给 repo

[![neither](img/32b68d5514cf4222fbb6d8bed60bd808.png)](https://i.giphy.com/media/i1JSXl0MfeRd6/giphy.gif) 
尽管这种解决方案可能允许更大的灵活性，但仍然非常糟糕。每个可以访问您的回购协议的人都可以立即看到您的凭据。

### 使用一个. secrets 文件(基本上是。上面的 env 文件是通过[无服务器秘密插件](https://github.com/serverless/serverless-secrets-plugin)加密的

[![mmmh, maybe](img/1fdbc6222ea9cff12eb3e603a66c84b7.png)](https://i.giphy.com/media/fVeK4abvX79BjF5mF7/giphy.gif) 
这是我们的第一个快速方法，但事实证明效果并不好，因为:

*   一旦 lambda 函数被部署，凭证在 AWS UI 控制台中清晰可见(env 变量在部署时被*嵌入到代码中)*
*   有人错误提交解密文件的风险很高
*   我们不得不在许多共享类似凭证的回购中复制这些文件
*   最重要的是，问题出现了- **我们把解密这些秘密的密码存储在哪里**？

```
plugins:
  - serverless-secrets-plugin
custom:
  secrets: ${file(secrets.${self:provider.stage}.yml)} 
```

Enter fullscreen mode Exit fullscreen mode

### 在你的 serverless.yml 中使用 SSM 加密的 [env 变量](https://serverless.com/framework/docs/providers/aws/guide/variables#reference-variables-using-aws-secrets-manager)

[![better, but mmmh](img/0d6ea7bd158d4cbcf2ae02f29e8bd7ee.png)](https://i.giphy.com/media/fnmo7jLamKpqno5wuK/giphy.gif) 
这是 secrets-plugin 的进一步发展， [AWS Systems Manager 参数存储](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)允许您去掉文件，只拥有一个由许多 lambda/repos 共享的配置，可以通过 AWS UI 控制台或 AWS CLI 快速更新，但它也有同样的缺点:

*   配置值以纯文本形式存储为 Lambda 环境变量——您可以在 AWS Lambda 控制台中清楚地看到它们——如果该功能被攻击者破坏(攻击者可以访问 process.env ),那么他们也可以很容易地找到解密后的值
*   因为您将代码与 env 变量一起部署，所以如果您需要更改配置，您需要重新部署每个 lambda 来传播所有更改。

```
custom:
  supersecret: ${ssm:/aws/reference/secretsmanager/secret_ID_in_Secrets_Manager~true} 
```

Enter fullscreen mode Exit fullscreen mode

### 运行时访问 SSM 或 SecretsManager(并使用缓存)

[![much better](img/fff0fcfd2c7e774bf7697d8dea9593d0.png)](https://i.giphy.com/media/RrVzUOXldFe8M/giphy.gif)

在系统管理器参数存储或[机密管理器](https://aws.amazon.com/secrets-manager/)(也允许自动轮换)上安全加密存储您的凭证，并在运行时访问它们。
然后配置您的无服务器 yaml，通过 IAMRole 策略授予对 lambda 的访问权限:

```
iamRoleStatements:
 - Effect: Allow
        Action:
         - ssm:GetParameter
        Resource:"arn:aws:ssm:YOUR_REGION:YOUR_ACCOUNT_ID:parameter/YOUR_PARAMETER" 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用不断增长的粒度级别来设置此权限

```
"arn:aws:ssm:*:*:parameter/*"
"arn:aws:ssm:YOUR_REGION:YOUR_ACCOUNT_ID:parameter/*"
"arn:aws:ssm:YOUR_REGION:YOUR_ACCOUNT_ID:parameter/YOUR_PARAMETER-*"
"arn:aws:ssm:YOUR_REGION:YOUR_ACCOUNT_ID:parameter/YOUR_PARAMETER-SOME_MORE_SPECIFIC" 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码直接指定了你的 ARN /地区/账户——如果你想更灵活，你可以设置自动获取这些值的权限:

```
iamRoleStatements:
 - Effect: Allow
        Action:
         - ssm:GetParameter    
        Resource:
         - Fn::Join:
          - ':'
          - - arn:aws:ssm
            - Ref: AWS::Region
            - Ref: AWS::AccountId
            - parameter/YOUR_PARAMETER-* 
```

Enter fullscreen mode Exit fullscreen mode

由于 SecretsManager 与 ParameterStore 集成，您可以通过 SSM 访问您的秘密，只需在您的密钥前加上`aws/reference/secretsmanager/`

如果您开始使用这些权限(尤其是在 UI 控制台中编辑策略——而不是重新部署 lambda——可能需要一些时间。通常以秒为单位，但也可能是 2-5 分钟)

一旦你授予你的 lambda 访问你的秘密的权限，你就可以指定一个环境变量来告诉你的 lambda 在运行时基于环境/阶段加载哪些凭证:

```
 custom:  
      credentialsKey:
        production: YOUR-PRODUCTION-CREDENTIALS-KEY
        development: YOUR-DEV-CREDENTIALS-KEY
        other: YOUR-OTHER-CREDENTIALS-KEY

functions:
  environment: 
    SECRETS_KEY:${self:custom.credentialsKey} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个将某种条件应用于无服务器部署的巧妙小技巧。基本上，你是在告诉 serverless 你有三个密匙:一个用于生产，一个用于开发，一个用于所有其他阶段。在 lambda 函数的 environment 节点中，根据当前正在部署的阶段设置密钥。如果当前阶段匹配列表中的一个变量名，它将被选中，否则，它将退回到另一个变量。

在您的 lambda 中，您只需从 SSM 或 SecretsManager 加载凭证并连接到您的数据库。

```
const ssm = new AWS.SSM();
const params = {
  Name: process.env.SECRETS_KEY,
  WithDecryption: true 
};
ssm.getParameter(params, function(err, data) {
  if (err) console.log(err, err.stack); // an error occurred
  else     console.log(data.Parameter.Value);    // here you have your values!
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 记住要实现某种缓存，这样当 lambda 容器被重用时，就可以避免从 AWS 加载键(并导致额外的成本)

我想指出的是， **SSM 要求在实例化**时定义 aws 区域。如你所见，我没有传递那个值。这是因为`process.env.AWS_REGION`是自动从 AWS SDK 读取的，而 env 变量是由无服务器脱机设置的。

你不需要做任何事情，直到你有一些集成测试试图加载秘密——我们添加了一些测试，以确保在每次部署后，该 env-stage 的秘密在 SecretsManager 上可用。在这种情况下，您必须将该变量传递给集成测试(记住手动将其传递给集成测试)。

这是我们的 npm 脚本(我们使用 [AVA](https://github.com/avajs/ava) 进行测试，使用[伊斯坦布尔/纽约](https://github.com/istanbuljs/nyc)进行代码覆盖):

```
"test:integration": "AWS_REGION=eu-west-1 SECRETS_KEY=MY_KEY_DEVSTAGE nyc ava tests-integration/**/*.*" 
```

Enter fullscreen mode Exit fullscreen mode

你有没有其他方法来处理这种常见的 id 的基本/基本特征？

* * *

更多相关资源:
[https://docs . AWS . Amazon . com/en _ us/systems-manager/latest/user guide/integration-PS-secrets manager . html](https://docs.aws.amazon.com/en_us/systems-manager/latest/userguide/integration-ps-secretsmanager.html)
[https://server less . com/framework/docs/providers/AWS/guide/variables/# reference-variables-using-AWS-secrets-manager](https://serverless.com/framework/docs/providers/aws/guide/variables/#reference-variables-using-aws-secrets-manager)