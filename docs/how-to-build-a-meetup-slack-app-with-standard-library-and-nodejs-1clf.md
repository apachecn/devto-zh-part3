# 如何用标准库和 Node.js 搭建 Meetup Slack App

> 原文：<https://dev.to/janeth/how-to-build-a-meetup-slack-app-with-standard-library-and-nodejs-1clf>

[在本指南](https://medium.com/@janeth_10755/how-to-build-a-meetup-slack-app-with-standard-library-52f96b27d95a)中，您将学习如何设置一个 Slack 应用程序来显示来自 [Meetup 的 API](https://www.meetup.com/meetup_api/) 的信息，该 API 将来自 Meetup 的存储数据提供给其他应用软件。

Meetup 是一个受欢迎的网站，有相似兴趣的人可以在这里组成团体，在他们所在的城市组织活动。一旦我们成功地向 Meetup 的 API 发出请求，我们将收到来自 Meetup 的响应，我们将从 JSON 有效负载中提取特定的数据，并在 Slack 中显示这些数据。我们将设计 Slack 应用程序的附件，以便它显示事件的名称、描述、日期和时间、位置等等！

[![](img/fd97ae59e53c0f63f52eee3932a17164.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W4YEl1Yh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AbppC-vl7i0Mg0medcD-eAA.png)

**工作原理:**

当你在 Slack 的 message box 里提交`/nextmeetup 94709&javascript`(或者任何邮编和感兴趣的话题)；将触发一个 webhook。在标准库上构建和托管的 webhook 将首先向 Meetup 的 API 发出一个请求，该 API 将返回一个 JSON 有效负载和查询结果。

然后，webhook 将为每个事件创建 Slack 消息，并将这些消息发布到指定的通道。

没必要不知所措！让我们一步一步来！

**你需要什么:**

[1x 备用账户](https://stdlibworkspace.slack.com/app)

[1x Meetup 帐户](https://www.meetup.com/meetup_api/)

[1x 标准库账户](https://stdlib.com/)

**#第一步:设置您的 Slack 应用程序**

确保您已登录到 [Slack](https://slack.com/signin) ，并访问位于【https://api.slack.com/apps】T2 的 Slack 应用仪表板。您将看到如下所示的屏幕。
[![](img/3d76adccad2b52b662d5738a317b9867.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rmzi4EjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AuCcRB20AOnwQzUONRrBOCA.png)

点击**创建新应用**。您将看到一个输入您的应用程序名称和您希望将其添加到的开发 Slack 工作空间的模式。
T3![](img/65b0320a7532c25f0aafe65b50186f58.png)T5】

从这里，点击**创建应用**，你会发现自己在一个**基本信息**页面。

向下滚动到**显示信息**。如果你愿意，你可以在这里给你的 Slack 应用命名、描述和图片。

在浏览器中保持**基本信息**页面打开；我们将马上使用它来检索您的 Slack 应用程序的凭证，以将该应用程序连接到托管在标准库上的后端逻辑——运行您的应用程序的代码。

**#第二步:创建一个免费的标准库账户**
我们将在[标准库](https://stdlib.com/)上托管我们的 Slack 应用程序代码——该代码将从 Meetups API 请求和接收特定信息。因此，前往标准库上的[代码，申请您的免费帐户。](https://code.stdlib.com/) [![](img/2e214cc9f0cdeabf0b4b535a01741a17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pd72CVLv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AZVSgFcah91qujGB3L_vlsQ.png)

**#第三步:复制并修改标准库上的 Slack App 代码模板**

一旦你登录或注册，你将登陆**“特色 API 源”**这些是标准库提供的应用代码模板，任何人都可以轻松复制和修改应用。您将选择 Slack 应用程序代码模板，并对其进行修改，以创建支持您的 Slack 应用程序的 API。

为您的 API 项目输入一个唯一的名称，然后点击**ok**。

**Slack App 源代码模板简介:**
Ok 太好了！让我们暂停一会儿来理解我们正在看的东西。左侧栏是标准库为您构建 Slack 应用程序而设置的 API 项目支架。

Slack 应用程序的代码模板有四个目录。对于本教程，我们将只在`functions`目录下工作，该目录配备了另外三个文件夹`actions/`、`commands/`和`events/`，以及一个文件`__main__.js`。应用程序的松弛动作、斜线命令和事件的说明位于这些文件夹中。

[![](img/3e14e17d7adf4afdb33d184f24e6727f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FQVsmyC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1080/1%2AcP1iJyYLN2Ph0y2h5TG--w.png)

当您部署 API 时，标准库将自动为每个目录生成 HTTPS 端点(URL)。由此产生的 URL 将允许我们配置 webhooks 来监听和响应 Slack 的[动作](https://api.slack.com/actions)、斜杠[命令](https://api.slack.com/slash-commands)和[事件](https://api.slack.com/events-api)。

所有五个文件夹(包括 functions 文件夹)都是用一个`__main__.js`文件(目录的主端点)设置的。当这些端点`__main__.js`从 Slack 收到消息时，它们会分派适当的函数。对于本教程，当我们通过 Slack Bot 调用 API 时，文件`__main__.js`将会分派`commands`端点。现在，让我们回到我们的机器人设置！

**#步骤 4:向标准库 API 添加一个命令**

`commands`:`commands`目录是所有松弛斜线命令的端点。将光标放在命令目录上并单击鼠标右键，创建一个附加命令。选择**新建文件**并将你的斜杠命令文件命名为 **nextmeetup.js** 点击**ok**。

[![](img/26e99bf7694486aa4ee550b7563307b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1WC0YkW6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1080/1%2AgeSV4j5FZ6emgHtCELFJmw.png)

[![](img/ea53d9d55470df66a7fc4761e778c20a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--muL9uK9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AsGE606411SaPRgzYFd03bw.png)

此时，你会注意到里面有一个“hello world”JavaScript 函数(`__main__.js`)，是自动生成的。

[![](img/3c341fb0d542f6f8ff70112124b65387.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Kh_83fs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1440/1%2AGkrX57wXnpND0lx2WriNpw.png)

将 nextmeetup.js 的内容替换为以下内容: