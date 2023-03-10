# Apache OpenWhisk 上 Slack bot 的快速 Python 后端

> 原文：<https://dev.to/ibmdeveloper/quick-python-backend-for-your-slack-bot-on-apache-openwhisk-3k8f>

[![](img/f93f1a0c77ddf2bcd81134b76e9db6d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pc_3p8Eh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5IaZdfxGvkqfUhNz4YXwxg.png) 

<figcaption>OpenWhisk 从头开始启用事件驱动架构！</figcaption>

> 一位同事最近在我们的内部 slack workspace 上询问如何为 slack bot 构建 Python 后端。这是 ***无服务器*** 的一个很好的用例，其中你的代码正在做一个非常专注的任务，使用可能**不频繁**和**不可预测**。我们将使用 IBM Cloud Functions，它是 Apache OpenWhisk 的托管版本，这是一个开源的分布式事件驱动的无服务器平台。

#### 先决条件

1.您的机器人将被部署的宽松的工作空间。我的名字叫 **upkar-watson-team** 。
2。IBM Cloud account 来构建 API 网关背后的操作和公开。[你可以在这里报名](https://cloud.ibm.com/login)。
3。[可选— WSK CLI](https://openwhisk.apache.org/documentation.html#wsk-cli) 将函数上传到 OpenWhisk。我们还将展示如何在 IBM Cloud UI 上做到这一点。

### 步骤

这些步骤非常简单，包括在 Slack 上创建一个应用程序和一个机器人，并在 OpenWhisk 上部署一个可以用作回调的函数。

> 这个想法是 Slack 将向我们的 Python 动作发送一个 POST 请求，其中包含我们的 bot 订阅的所有事件的事件信息。

#### 在 Apache OpenWhisk 上写一个动作(IBM 云函数)

1.登录 [IBM 云](https://cloud.ibm.com/login)。登录后，您将被带到您的 ***仪表盘*** 。

2.点击左边菜单上的功能来启动 OpenWhisk 主页

[![](img/4d4905ac3dcb6c344db7e32b520b9b66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ykecmPly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/785/1%2A6d4UOrBPwNkeAGTCm2iAlQ.png)

3.点击 ***创建新动作，开始创建*** 。

[![](img/b9a0e7886e0bc06aba18b6004733c492.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ZPWPGq6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1007/1%2A1d5ulxV9EtYh5Fda5NOKYQ.png)

4.给动作起一个名字，选择 ***Python 3*** 作为运行时。

[![](img/14c95d0fe8524450f0f418f875321ee9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n4IAch7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/968/1%2AWaQr84YLAEMzHAYooYBDmw.png)

5.将以下 python 代码添加到动作

```
#
#
# main() will be run when you invoke this action
#
# [@param](http://twitter.com/param) Cloud Functions actions accept a single                     parameter, which must be a JSON object.
#
# [@return](http://twitter.com/return) The output of this action, which must be     a JSON object.
#
#
import sys

def main(event):
  # check if the event contains a challenge
  if "challenge" in event:
      print('Event with verification challenge detected.')
      challenge = event['challenge']
      return {'challenge': challenge}

  return { 'message': 'No challenge found. Nothing to do' } 
```

[![](img/d78676f1e362cab98b2817377aa25b35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S307HhaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/963/1%2AkF397V8W2KuYUBIR9EE0cw.png)

您可以从 UI 本身调用这个函数。因为事件字典中没有事件，所以动作返回

```
‘message’: ‘No challenge found. Nothing to do’ } 
```

[![](img/6baf4cbb40d1f2712f394cf74aa25050.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A5bzGgBS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUzxi8VCZAE-OzvSgy1Cr_Q.png) 

<figcaption>动作调用时没有事件通过</figcaption>

6.点击 ***添加以下参数，更改输入*** 如下:
[![](img/2a33097822c4ae026779091e75534707.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3oUDHxC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AN3sC7xpppOemNtcyENufRg.png) 

<figcaption>设置事件为动作</figcaption>

的输入

当您现在调用该函数时，您会看到挑战作为输出返回。这就是 Slack 所期待的。你可以在这里阅读[更多细节。](https://api.slack.com/events/url_verification)

[![](img/2a443acc5572988e814ef19f5ba7eb50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--45xk97tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2AAtuoWC-wnrqFubTiUX6A.png)

7.点击 ***端点*** ，将动作启用为 web 动作

[![](img/5489145461a386caea6c4f417df13221.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qN7niRJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aj_uDOkWq0yY6CMJ-2jJ0vg.png)

8.返回到 ***动作*** 主页，在您的动作前面添加一个 API。

[![](img/9918f5b4c2b42f91151472985e152888.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k55723eM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2And6KcWNbNSlG10e6fHqYXQ.png)

9.为您的操作创建一个 API 端点

[![](img/f56bed3967964bfe667d5e763cb8c766.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--reJiHRSN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcXold5SVtP8HvRzGtoNfYQ.png)

10.给一个 ***命名*** ，一个 ***基础 api 路径*** 和 ***创建一个新操作*** 。

[![](img/fa1252e720aeee1e2b40d9b86dea95c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ltr8L7Ts--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL9AHCPvoZVWViLDT8f5BxA.png)

11.创建一个监听 POST 请求并调用我们的 python 操作的操作

[![](img/8fabf8e07ccb185f50e33a2c39fb73c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mEbUCjrT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AndPuMPZVzf5-akoj31nhWw.png)

12.保存 API

[![](img/6caaccab810a76d51d43ead8c9591595.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l9JXd2gB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1NB8o66siDHN9JQot4_54g.png)

13.从 API Explorer 获取文章 URL

[![](img/1939155d5c024e4fc3abc83dfdd4d5ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6CiUanqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEakxf5_dXddqwAZun8xVDw.png)

`Keep this URL handy. We will provide it to slack in the next section as a verification URL.`

#### 在空闲时间创建一个机器人

1.  转到 api.slack.com 并创建一个新的应用程序

[![](img/e091fa91d112d73634b33acf5c46f8d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nXbY6OQu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/723/1%2AWkcmGQa3zMUOoYTSYQ2i_Q.png)

[![](img/7a6353f2a6f0be2bfdc99229ef8cef9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sBlVjeK1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/784/1%2AI3u3gfHmFc1zxBuJB6nNfQ.png) 

<figcaption>创建新应用</figcaption>

1.  添加机器人用户

[![](img/0aa481f9e009dc421435c2ae928ac94f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UeEVUvYm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/988/1%2AEJOfE8Totg-H5jR7UX7B0g.png) 

<figcaption>添加新的 bot 用户</figcaption>

[![](img/00db1fea9c68e31cbeef47da18c198a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4y_bgBiW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1015/1%2Ay0h_FZdlkVD5FArK_8wvzA.png) 

<figcaption>添加新的 bot 用户</figcaption>

1.  订阅工作空间和机器人事件

[![](img/78b350856c719cd4587cecf7071d3dcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cZNg97yt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/779/1%2AuoISxzVjjKs2CHXeHY1fHw.png)

1.  启用事件并添加 API 网关端点发布 URL。

[![](img/0853f34a17b7da1abc2504137c893533.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ThEqXfK5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1009/1%2ApRhj4rsJnoC0hdofv19ecQ.png)

<figcaption>bot 订阅不同的消息事件</figcaption>

将您在上一节中复制的 IBM API URL 添加为该屏幕中的 ***请求 URL*** 。跳出该字段，并确保 Slack 可以验证该 URL。

[![](img/3837f67cdf63b598f9f1f391ecdb4e0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yK2BteX4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/961/1%2A1XgNdKXy1c80CtNYuZauOQ.png) 

<figcaption>添加请求 URL 并在空闲时验证</figcaption>

1.  我们已经完成了对 Slack 上的应用程序和 bot 的配置。下一步是将我们的应用程序安装到我们的工作区。

[![](img/25ea57f34468f7f782cb0a4b60a0fa08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LxeoE3E4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/920/1%2AV5DUZSUlqDTfQzMQPEInjA.png)

[![](img/26471b0acd133190b934204748d57908.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rFU9qNQq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/581/1%2AsEy3NvrcgkkvO8BYZmehPA.png)

1.  转到 slack 工作区，您应该会看到新的 bot 可用。

[![](img/726a208b0044855c6f6f01679d44bc8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tfr5EdwX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/652/1%2A_-DUBREVFfO__Rca-IytJw.png)

这么🎆🎈！！我们在 OpenWhisk 中非常快速地构建了一个 Python 后端，这将允许我们与我们的机器人进行交互。

#### 在空闲时测试我们的机器人

我修改了 OpenWhisk 动作代码，打印出来自 Slack 的消息。

[![](img/4cc253eb4cec5f35190d21b14d32a693.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MX_2cOY_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/989/1%2AxQo2xZR4kBtdaSldPAsFwQ.png)

现在，我们可以使用***wsk activation poll***命令来轮询 OpenWhisk 日志，正如您所看到的，Slack 客户端和日志中都发布了相同的消息。这就需要你安装 ***IBM Cloud CLI*** 和 ***WSK CLI*** 。或者，您可以使用 IBM 内置日志来查看操作输出的值。

[![](img/de9b5f725a0502e270c87f44c7f74916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xP2GAehX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/956/1%2ASzoDYziioRLTbtu8DjjvNw.png)

既然我们已经成功地为我们的 bot 部署了 Python 后端，我们就可以处理来自 Slack 的事件了。一个常见的用例是使用开源 python 库来分析用户所说的内容。我正在写一篇使用 NLTK 的帖子，NLTK 是用 Python 编写的一个流行的 NLP 库，用来检测用户输入内容的情感。

敬请期待！

[【max Katz】、](https://medium.com/u/bf01a11701fe)[【Carlos Santana】、](https://medium.com/u/1f2fe78a0cf3)[【tomami immura】、](https://medium.com/u/a0b0677819e4)