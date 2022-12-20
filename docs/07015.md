# 谷歌云功能:使用 Gmail SMTP 和 Nodemailer 发送电子邮件

> 原文：<https://dev.to/i_maka/google-cloud-functions-sending-emails-using-gmail-smtp-and-nodemailer-1lij>

我有一个非常简单的网站托管在 GCP 存储部分，现在我需要通过添加一个联系方式使它稍微不那么简单。然后我意识到最简单的方法是使用我已经或多或少准备好的东西:我可以使用 Google 云平台编写一个函数，使用我在同一个域中使用的 GSuite 帐户发送电子邮件。让我们看看一切进展如何！

## 调教

我们需要使用 **OAuth2** 认证方法，为此我们首先需要进入[GCP 控制台](https://console.cloud.google.com/iam-admin/serviceaccounts)并选择(或创建！)一个服务账号。完成后，点击**更多**按钮(三个点)，然后点击**创建密钥**，这将带您进入创建服务密钥的过程，并将下载一个包含我们需要的所有数据的 JSON 文件。

现在，我们必须转到 [GSuite 控制面板](https://admin.google.com)并为服务帐户启用 de API。转到**安全- >高级安全- >管理 API 客户端访问**，输入您的 API 范围的`client_id`和`https://mail.google.com/`。你可以在 JSON 文件中找到你的`client_id`。

## 谷歌云功能

要创建一个谷歌云功能，我们必须进入 gcp 控制台中的[功能概述页面](https://console.cloud.google.com/functions/list)，点击**创建功能**并为其命名。然后我们需要选择 **HTTP 触发器**和**节点**运行时。您可能还希望选择尽可能低的内存分配。现在，我们将使用内联编辑器，所以我们稍后需要将我们的`index.js`和`package.json`的内容粘贴到那里。我们需要设置**函数来执行**，在我的例子中是`sendMail`；然后我们可以点击**更多**来设置一些环境变量。我们的代码将使用以下内容:

`GMAIL_ADDRESS`:这是我们将用于认证的用户，记住它必须是真正的**T4 用户，而不是别名。
`CLIENT_ID`:在 JSON 文件中找到。
`PRIVATE_KEY`:在 JSON 文件中找到。**

我们还会设置`MAIL_FROM`、`MAIL_TO`、`MAIL_BCC`；这些可以在请求中发送，但我们希望有一个后备，因为它们不是强制性的。

## 代码

我们将拥有的唯一依赖项是 Nodemailer，所以:
`npm i --S nodemailer`

现在，我们来看看代码:

```
exports.sendMail = (req, res) => {} 
```

为了让这个函数工作，我们的 **sendMail** 方法将提供两个参数:请求和响应。

```
 if (!req.body.subject || !req.body.text) {
    res.status(422).send({
      error: {
        code: 422,
        message: "Missing arguments"
      }
    });
    return;
  } 
```

我们做的第一件事是检查我们是否有我们需要继续的一切，在我的情况下，我只关心我是否有一封实际的电子邮件要发送，其他参数如`from`或`to`是可选的，因为我将为它们存储环境变量，但你可以检查你需要的任何东西！

```
const nodeMailer = require("nodemailer");

const transporter = nodeMailer.createTransport({
  host: "smtp.gmail.com",
  port: 465,
  secure: true,
  auth: {
    type: "OAuth2",
    user: process.env.GMAIL_ADDRESS,
    serviceClient: process.env.CLIENT_ID,
    privateKey: process.env.PRIVATE_KEY.replace(/\\n/g, "\n")
  }
}); 
```

现在我们用我们的配置创建一个传输。它将从我们之前定义的环境变量中获取值。请注意，我们正在进行字符串替换，因为平台会对我们的密钥中的`\n`进行转义，如果我们不这样做，私钥将无效。

```
const mailOptions = {
  from: req.body.from || process.env.MAIL_FROM,
  to: req.body.to || process.env.MAIL_TO,
  bcc: req.body.bcc || process.env.MAIL_BCC,
  subject: req.body.subject,
  text: req.body.text
}; 
```

现在我们定义我们的邮件选项，同样，这些可能与您需要的不同，您可以查看[文档](https://nodemailer.com/message/)了解更多可用的细节。

```
transporter
    .sendMail(mailOptions)
    .then(() => {
      res.status(200).send({
        data: {
          code: 200,
          message: "Mail sent"
        }
      });
    })
    .catch(e => {
      res.status(500).send({
        error: {
          code: 500,
          message: e.toString()
        }
      });
    }); 
```

最后，我们尝试发送电子邮件并返回适当的响应。你可以在 [github](https://github.com/imaka/GCloudFunction-SendMail) 上查看完整代码

## 同步您的回购

您可能注意到有几个选项可以将代码上传到您的函数，其中之一是附加一个云源代码库...这很好，因为这样你就可以通过将代码推送到回购来维护你的功能。我的问题是我喜欢使用 GitHub，这不是一个选项...但是我们可以**镜像**我们的回购！

我们需要在[谷歌资源仓库](https://source.cloud.google.com/onboarding/welcome)上添加一个新的云资源仓库。点击**添加库**并选择**连接外部库**。然后，选择包含您的函数的项目，并选择您的连接器(GitHub 或 Bitbucket)。接下来要做的是授权 Cloud Source Repository 存储您的凭据，按照步骤连接您的帐户，并选择您希望与您的 Cloud Source repo 关联的 repo。存储库名称的开头会添加`github_`或`bitbucket`，并且会自动与原始存储库同步。

一旦我们准备好了我们的云资源存储库，我们可以返回到我们的函数，单击 **edit** 并选择云资源存储库作为源:填写存储库名称和您想要链接的分支或标签，您就可以开始了！