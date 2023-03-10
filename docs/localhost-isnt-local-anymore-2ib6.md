# Localhost 不再是本地的了

> 原文：<https://dev.to/lostintangent/localhost-isnt-local-anymore-2ib6>

在我的[上一篇文章](https://dev.to/lostintangent/visual-studio-live-share-in-pursuit-of-enjoyable-developer-collaboration-2nko)中，我讨论了我们创建 [Visual Studio Live Share](https://aka.ms/vsls) 的原因，以及它可以改善开发人员协作体验的各种方式，无论您正在构建的应用程序是什么类型(例如，桌面、移动、游戏、web)。然而，在这篇文章中，我想深入探讨 Live Share 提供的一个功能，它是专门针对 web 开发人员的:[共享本地主机服务器](https://docs.microsoft.com/en-us/visualstudio/liveshare/use/vscode#share-a-server)。

<figure>

[![Image #1](img/d74bdf99f0c18ef17d297ed1af6d7339.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hzyrjFDg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/55004544-a6e21080-4f97-11e9-8f37-806a976cf3f2.gif)

<figcaption>Sharing port 8080, and having that be immediately available to guests (left) in a Live Share session</figcaption>

</figure>

## 协作应该很容易

当您与另一个开发人员合作时，他们能够直观地看到您正在开发的应用程序是非常重要的。这样，他们就可以报告你遇到的错误，对你做的一些 UI 修改提供反馈，和/或验证你们一起做的修复。但是，如果你正在构建的应用是 web 前端或 API (REST、GraphQL 等。)，并且它在您的开发机器上本地运行，那么您实际上如何与他们共享它，以便他们可以独立于您并使用他们喜欢的工具与它交互(即，不是屏幕共享)？你可以...

1.  将您的更改推送到版本控制，并要求他们将它们拉下来并在本地运行应用程序？然而，这需要每个人都有自己的环境设置来运行应用程序，并且这个工作流不支持实时迭代代码库(废话！).

2.  将应用程序部署到云中，然后向每个人发送公开的 URL？然而，您可能还没有做好部署的准备，并且这个工作流遭受与#1 相同的迭代问题，因为您必须在每次代码更改时重新部署应用程序(不酷！).

3.  配置你的路由器[转发应用端口](https://en.wikipedia.org/wiki/Port_forwarding)，然后把你的 IP 地址发给大家？这将支持对代码进行实时更新，但是，它要求您在互联网上公开您的机器，这可能是不可取的，甚至是不可能的(取决于您的网络代理/防火墙设置)。

4.  设置一个可以上网的[“堡垒服务器”](https://en.m.wikipedia.org/wiki/Bastion_host)，你可以用它来反向转发本地端口(如图所示)。这提供了一个更安全的机制，但是它要求你支付/管理一个 VPS，并且设置起来有些复杂。

5.  使用类似于 [localhost.run](http://localhost.run/) 、 [serveo](https://serveo.net/) 或 [ngrok](https://ngrok.com) 的服务，这些服务都是免费的，使用起来也很简单。然而，除了您的编辑器之外，它们需要使用单独的工具，这增加了额外的复杂性。

有了这些选择，问题就来了:如何共享一个正在运行的 web 应用程序，不仅要易于设置、完全安全，还要让你和你的团队能够实时编辑代码，并立即看到变化？这就是 Live Share 的用武之地！

## 分享所有的东西

当您连接到实时共享会话时，您和“主机”之间会建立一个 [SSH 连接](https://en.wikipedia.org/wiki/Secure_Shell)，这可确保所有通信(例如文件编辑、调试步骤)都是端到端加密的。然后，这种安全连接可以用于实现各种协作环境，包括“客人”查看共享 web 应用程序的能力，就像他们在自己的机器上运行一样。但事实并非如此。它是...有点神奇😎

> ![Kamran ⛄ Ayub profile image](img/1bf3f5ae3b3186eb318efb5a42b22d44.png)卡姆兰⛄ayub[@卡姆兰 ayub](https://dev.to/kamranayub)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)用 [@code](https://twitter.com/code) Live Share 做 4 人黑客会话，有两个共享服务器和两个代码库🔥🔥🔥2019 年 1 月 18 日下午 14:41[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1086272490710675457)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1086272490710675457)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1086272490710675457)

托管实时共享会话时，您可以随时共享服务器，只需在实时共享视图中单击`Share server...`命令，然后指定相应的端口(例如`8080`)。它将立即对您会话中的所有来宾可用，并且他们可以在他们喜欢的浏览器或 API 开发工具中打开它(例如[邮差](https://www.getpostman.com/))。

[![](img/8faca890e58d00572dc92ed58521e721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R4tHsVEJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/116461/55005347-10aeea00-4f99-11e9-85fb-f9ca5d4a55fb.png)

从那里，您可以进行编辑，并立即查看更改。更好的是，如果你的网络应用程序设置为使用“实时重新加载”，那么当你或客人保存文件时，每个人的本地浏览器都会自动更新，不管你是在世界的另一端(还是在飞机上)！🛫

液体错误:内部

## 柠檬榨汁机

虽然共享一个服务器可以实现一种强大的协作 web 开发形式，但它仍然需要您执行一个手动手势，随着时间的推移，这可能会变得有点重复。由于我们的目标是让协作感觉像是现有开发人员工作流程的自然延伸，我们认为我们可以做得更好。我们做到了。

<figure>

[![Image #2](img/74f6cd41f0918ca0c9e60a713d934e5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4vi3PH3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/55005872-050ff300-4f9a-11e9-947c-91abd9eb8546.gif)

<figcaption>Using the Live Server VS Code extension to serve static HTML, and having the server port automatically shared</figcaption>

</figure>

无论你是使用 [Live 服务器扩展](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)来托管静态 HTML 文件，还是通过类似 [create-react-app](https://github.com/facebook/create-react-app) 、 [Django](https://www.djangoproject.com/) 、 [Phoenix](https://phoenixframework.org/) 等命令行界面来运行你的前端。，或者从 ASP.NET 项目中在 Visual Studio 中按下`F5`，Live Share 会自动检测服务器并进行共享。这样，您不需要做任何特别的事情就可以使用 Live Share。您只需按照自己喜欢的方式工作，让工具来完成剩下的工作👍

<figure>

[![Image #3](img/569e70134c72e735d924b41c1b788869.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QxJ3AtuA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/116461/55006973-d6931780-4f9b-11e9-84f9-c5bde9088b70.gif)

<figcaption>Using the create-react-app CLI to spin up a dev server, and having the server port automatically shared</figcaption>

</figure>

为了确保安全，我们只自动共享 web 框架常用的端口(例如 3000、8080)，这可以防止我们意外共享您意想不到的内容。此外，如果您想禁用自动共享行为，并坚持手动共享，您也可以禁用`liveShare.autoShareServers`设置。这样，我们可以提供一个简单的、开箱即用的体验，同时还能给开发人员带来自信所需的控制。

[![Screen Shot 2019-03-26 at 8 40 51 AM](img/da91918b0b8d88c75f5e666fea222749.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DzdXvUtZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/116461/55011279-ea8e4780-4fa2-11e9-8b74-a815d9827ca2.png)

## 释放本地主机

既然共享本地主机服务器只需点击一下`Live Share`按钮...)，我们渴望听到任何关于如何进一步改善体验的反馈。Web 和 API 开发是 Live Share 的核心场景之一，因此我们期待改进协作开发体验，满足每个开发人员的需求，无论他们使用何种语言或平台。

如果您遇到任何问题，或者有疑问/意见，请不要犹豫，在 [GitHub](https://github.com/microsoftdocs/live-share) 上 ping 我们的团队。此外，如果您愿意参加一个[快速调查](https://aka.ms/vsls-survey)，这将对我们的团队非常有帮助。谢谢！