# 通过 AWS SES 和 Lambda 发送电子邮件

> 原文：<https://dev.to/centrics/send-e-mails-through-aws-ses-and-lambda-17f2>

通过代码发送电子邮件有几种不同的方式。有几个平台和服务可以帮助你实现它。在这种情况下，我决定使用 AWS 的 Lambda 和 SES(简单电子邮件服务)来实现它，我将解释为什么:

*   我们所有的堆栈都在 AWS 上，这使得跟踪和监控一切(账单、指标等)变得更加容易...)
*   它必须与用 PHP 编写的后端分离，并成为一个整体
*   它会偶尔运行一次，所以当服务没有被使用时不付费是很好的
*   我想尝试 SES，我是 Lambda :p 的忠实粉丝

让我看一下我们的用例，然后分享一些代码和我是如何实现它的！在向你展示代码的时候，我假设你熟悉 Node，Lambda 和无服务器框架。

* * *

# 用例

我们是一个客户成功平台，依靠几个指标来帮助我们的客户定义他们的客户战略。我们依赖的指标之一是 NPS(净推广者分数),这基本上是一个衡量客户满意度的分数。

我们必须开发的是一种发送电子邮件的方式，客户可以从 0-10 分中选择一个分数。这封电子邮件是在我们的客户采取行动后由后端触发的。客户选择一个分数，并保存下来供将来分析。

# 创造我们的λ

第一步是创建由后端触发的 Lambda 函数。我已经用[无服务器框架](https://serverless.com/framework/)这样做了，因为它很简单，而且我已经有了一些使用它的经验(实际上我想下次试试 [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) )。

一旦我们创建了项目(`npm init`)，我们必须在配置中定义`serverless.yml`文件。以下是我们的初始配置(注意我使用`sa-east-1`作为地区):

```
service:
  name: my-nps-email

provider:
  name: aws
  runtime: nodejs8.10
  region: sa-east-1
  stage: ${opt:stage, "dev"}
  deploymentBucket: my-nps-email-deployment-bucket
  memorySize: 128
  timeout: 5

functions:
  send-email:
    handler: index.handler 
```

Enter fullscreen mode Exit fullscreen mode

首先请记住，您必须自己创建部署桶，您可以通过 CLI 或 AWS 控制台来完成。

现在我们只需创建一个名为`index.js`的文件，并导出`handler`函数:

```
exports.handler = (params) => {
  console.log(params);
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们部署之前，请确保您已经安装了无服务器框架(`npm i serverless`)。然后你只要`npx serverless deploy`它就会展开。

以下是一些注释:

*   我喜欢把它作为一个开发依赖项来安装，并且有精确的版本(我在需要的时候会手动更新它)，所以我做了`npm i --save-dev --save-exact serverless`
*   当使用`serverless`时，我总是使用标志`-v`,这意味着详细并显示部署期间的所有堆栈事件
*   当使用`serverless`时，我总是设置下面的环境变量`SLS_DEBUG=*`来启用调试日志

我也添加了一些 NPM 脚本到我的`package.json`中，使它更容易使用:

```
{  "name":  "my-nps-email",  "version":  "1.0.0",  "scripts":  {  "deploy":  "SLS_DEBUG=* serverless deploy -v"  "test:valid":  "SLS_DEBUG=* serverless invoke --function send-email --path data/valid.json"  },  "devDependencies":  {  "serverless":  "1.34.1",  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`valid.json`是将在 Lambda 函数中调用的有效主体。

现在我们很好，我们的功能已经部署好了。之后，我们必须确保我们的后端可以调用这个函数，为此我们必须管理 IAM 权限。默认情况下，无服务器为您创建了一个 IAM 角色，格式如下:`arn:aws:iam::YOUR_ACCOUNT_ID:role/my-nps-email-dev-sa-east-1-lambdaRole`，您可以自定义它，如果你想，但我喜欢，因为它使用函数的名称，阶段和区域来创建角色名称。我们现在要做的是将这个角色添加到我们的后端权限中(在本例中是调用权限):

```
{  "Effect":  "Allow",  "Action":  "lambda:InvokeFunction",  "Resource":  "arn:aws:iam::YOUR_ACCOUNT_ID:role/my-nps-email-dev-sa-east-1-lambdaRole"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的后端可以调用我们的函数了。我不会详细讨论我们的后端如何调用 Lambda 函数，因为它基本上是从 AWS 文档中复制代码。

下一步是让我们的 Lambda 函数从 SES 发送电子邮件。

# 用 SES 发送电子邮件

使用 SES 只有一个设置:允许您的电子邮件接收来自 SES 的电子邮件(用于测试目的)。当你准备好离开时，你必须要求 AWS 离开他们所谓的沙箱。之后，你可以“按照你想要的方式”发送电子邮件(当然要尊重他们的政策)。

进入您的控制台> SES >电子邮件地址>验证新的电子邮件地址。遵循这些步骤，您将准备好接收来自 SES 的电子邮件。

与任何 AWS 服务一样，您需要从您的服务/功能/用户处获得使用许可...所以我们的第一步是允许我们的 Lambda 函数调用 SES。为此，我们向我们的`serverless.yml` :
添加一个 IAM 角色语句

```
...

provider:
...
  iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "ses:SendEmail"
      Resource:
        - "*"
      Condition:
        StringEquals:
          ses:FromAddress:
            - "fromemail@someemail.com"
... 
```

Enter fullscreen mode Exit fullscreen mode

我是说我的 Lambda 被允许使用发件人地址从 SES 发送电子邮件。这只是一个安全检查，以避免任何控制台覆盖错误。

现在我们将使用 Javascript 的 AWS SDK 从 SES 发送电子邮件。我们的函数应该接收所有需要的参数，以便能够发送电子邮件。下面是我目前的配置:

```
const AWS = require('aws-sdk');
const SES = new AWS.SES({ region: 'us-east-1' });

exports.handler = async (params)  => {
  console.log(params);

  const {
    to,
    from,
    reply_to: replyTo,
    subject,
  } = params;
  const fromBase64 = Buffer.from(from).toString('base64');

  const htmlBody = `
    <!DOCTYPE html>
    <html>
      <head></head>
      <body><h1>Hello world!</h1></body>
    </html>
  `;

  const sesParams = {
    Destination: {
      ToAddresses: [to],
    },
    Message: {
      Body: {
        Html: {
          Charset: 'UTF-8',
          Data: htmlBody,
        },
      },
      Subject: {
        Charset: 'UTF-8',
        Data: subject,
      },
    },
    ReplyToAddresses: [replyTo],
    Source: `=?utf-8?B?${fromBase64}?= <fromemail@someemail.com>`,
  };

  const response = await SES.sendEmail(sesParams).promise();

  console.log(response); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一下这段代码:

*   出于日志记录的目的，我在开头和结尾都使用了`console.log`
*   我使用`async/await`是因为阅读代码比使用承诺更好
*   我有一些参数来自我们的后端，用来发送我们的电子邮件
*   你可能会问那是什么，是做什么的。首先:你不需要它，你可以只对一封电子邮件使用属性`Source`,但是在你的收件箱中，当有人收到那封电子邮件时，它看起来不太好，因为它没有“发件人”的名字，只有它的电子邮件。我这样做是因为我需要处理`UTF-8`字符而 [AWS SES 不支持 SMTPUTF8 扩展](https://docs.aws.amazon.com/pt_br/ses/latest/APIReference/API_SendEmail.html)，而这个扩展应该根据[这个规范](https://tools.ietf.org/html/rfc2047#section-2)来实现。我不会深入研究它，但是您可以很容易地按照这一节来了解每个参数的含义。

现在，我们的函数应该能够从 SES 发送电子邮件。只需再次部署它并更新您的`valid.json`以便与`npm run test:valid`一起使用。

现在一切都正常，我们可以根据需要进行扩展(不是在这种情况下)和付费使用(如果不超过免费层，它甚至可能是免费的)。

# 结论

几乎所有无服务器的东西，我们花在配置上的时间都比实际编码的时间多，但是主要的区别是我们编码的是业务相关的东西，而不是建立框架和库。

该解决方案对于它所解决的问题来说可能“太多了”，但它处理得很好，无需维护，并且在需要时易于更新/部署。

如果您对如何改进该解决方案有任何建议或任何问题，请在下面发表评论！