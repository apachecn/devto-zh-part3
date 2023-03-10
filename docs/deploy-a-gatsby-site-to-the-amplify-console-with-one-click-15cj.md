# 只需点击一下鼠标，即可将 Gatsby 站点部署到 Amplify 控制台

> 原文：<https://dev.to/dabit3/deploy-a-gatsby-site-to-the-amplify-console-with-one-click-15cj>

[![Amplify Console 1 Click Deploy](img/ab24a36ea97ce82eea18f948f3add370.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h-Bpl3Dp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r4h7jffg9s0y5bnkc2y1.jpg)

我们最近发布了[部署放大控制台按钮](https://docs.aws.amazon.com/amplify/latest/userguide/one-click.html)。

您可以将此按钮添加到 GitHub README.md 文件、博客帖子或任何其他呈现 HTML 的标记页面，以允许开发人员通过单击将其应用程序部署到 Amplify 控制台。

尝试在下面推出[盖茨比入门博客](https://github.com/gatsbyjs/gatsby-starter-blog)👇。

[![amplifybutton](img/51508332abcfc80db7c7ad175ea69bbf.png)](https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/gatsbyjs/gatsby-starter-blog)

它基本上是这样工作的:

您创建了一个具有以下 URL 结构的链接:

```
https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/username/repository 
```

然后，您可以像这样将它包装在 markdown 中的按钮中:

```
[![amplifybutton](https://oneclick.amplifyapp.com/button.svg)](https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/username/repository) 
```

这样部署一个 app 会怎么样？

1.  将回购转入你的 GitHub 账户
2.  创建您的放大器控制台应用程序
3.  启动您的构建

一旦应用程序启动，你会得到一个活的网址。

要开始新的构建，克隆应用程序，在本地进行更改，并将更改合并到 master 中。

如果正在启动的应用程序有一个附加的 Amplify 后端，你也可以选择部署后端。

要了解更多关于 Amplify 控制台的信息，请点击查看文档[。](https://aws.amazon.com/amplify/console/)

> 我的名字是纳德·达比特。我是亚马逊网络服务的一名开发者倡导者，负责像 [AWS AppSync](https://aws.amazon.com/appsync/) 和 [AWS Amplify](https://aws-amplify.github.io/) 这样的项目。我专门从事跨平台&云应用开发。