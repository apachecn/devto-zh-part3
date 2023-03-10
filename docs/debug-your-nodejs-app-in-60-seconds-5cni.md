# 在 60 秒内调试 Node.js 应用程序

> 原文：<https://dev.to/john_papa/debug-your-nodejs-app-in-60-seconds-5cni>

设置调试可能是一件苦差事。它可以让你感觉像控制台。日志毕竟不是那么糟糕。对吗？虽然我和其他人一样喜欢一个好的 console.log，但我更愿意对我的应用程序进行零配置调试。你不会吗？

<figure>[![](img/be857ce41fff5e54bf7347cfce22f635.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8UAsDqEa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARUTm8jf4xhyRMKhpplUrDg.jpeg)

<figcaption>60 秒调试 Node.js 的 sketch note</figcaption>

</figure>

[Visual Studio 代码](https://code.visualstudio.com/?wt.mc_id=devto-blog-jopapa)(又名 VS 代码)在 Node.js 调试上改变了游戏。有一个特性叫做**节点:自动附加** ( [你可以在这里的文档](https://code.visualstudio.com/docs/nodejs/nodejs-debugging?wt.mc_id=devto-blog-jopapa)中了解更多)。或者继续阅读——这只需要一分钟。

当您启用此功能时，您可以从命令行、npm 脚本或运行其中之一的工具运行节点应用程序。只要确保添加了节点检查器标志，让 Node 知道您正在计划调试。一旦你这样做了，调试器就会在 VS 代码中亮起来！

<figure>[![](img/eab8a3965e5afde7dbe8f0b0d3c9081d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x0yKUmKo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sil2q2wm0lm9ca0iuchl.jpg) 

<figcaption>使用 VS 代码

<figcaption></figcaption>

</figcaption>

</figure>

将调试器自动附加到 Node.js 应用程序

### 循序渐进

在 VS 代码中打开设置(Mac 上为 CMD +，Windows 上为 CTRL +)。你也可以进入命令面板(Mac 上的 CMD + SHIFT + P 或者 Windows 上的 CTRL + SHIFT + P)搜索设置。

搜索“自动附加”，你会看到一个**节点的设置:自动附加**。继续把那个**打开**。

现在，从您最喜欢的命令行运行 Node.js 应用程序，无论是集成终端、外部终端还是使用魔棒(如果您有魔棒，您可能不需要调试😀).

```
node --inspect=0.0.0.0:9229 server.js 
```

现在，当您使用- inspect 标志启动应用程序时，调试器会在 VS 代码中附加到您的应用程序上！从这里，您可以设置断点，单步调试您的代码，并拥有所有伟大的调试好处！

你可以从 VS 代码文档中的[这个链接了解更多关于这个特性的信息。](https://code.visualstudio.com/docs/nodejs/nodejs-debugging?wt.mc_id=devto-blog-jopapa)

[![Auto-Attach](img/04da9582a2a6945e6ac4c7105da9698f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zGbw1nAL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/672/1%2AcvDAt4AVZ1K_yGOyvZRvNw.png)

* * *

*转贴自[媒体](https://medium.com/the-node-js-collection/debug-your-node-js-app-in-60-seconds-9ee942a453f0)T3】上的原创文章*