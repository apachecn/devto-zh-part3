# Gitpod 简介 GitHub 上的无摩擦编码

> 原文：<https://dev.to/svenefftinge/introducing-gitpod-frictionless-coding-on-github-397g>

每天，开发人员都要浪费数百万小时在项目和分支之间切换上下文，建立开发环境，或者只是等待构建完成。

这种摩擦不仅耗费时间，而且对贡献者来说是一个严重的障碍。通读一长串的安装说明和搞乱你自己的笔记本电脑实在是太费力了。

今天，我们很高兴地宣布推出 [Gitpod.io](https://gitpod.io) ，这是一项在线服务，为 GitHub 项目提供一次性的现成代码开发环境。

## GitHub 一键式开发环境

Gitpod 是这样工作的:你在 GitHub 上，想开始做一个项目。而不是通过手动设置等。您只需点击一个按钮，就可以在浏览器中启动一个现成的代码开发环境。您现在可以编码、评审或者只是尝试这个项目。完成后，您只需关闭选项卡。

[![GitHub button](img/102883bdb83224fab30b92f63fb98f91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8VuDZNtW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rqbhmyoic23uzmaw300t.png)

还没看到 GitHub 上的按钮？你需要[安装一个小的浏览器扩展](https://www.gitpod.io/docs/20_Browser_Extension)才能得到。或者，你可以在任何 GitHub 的 URL 前加上 https://gitpod.io/#"的。

你现在可以试试 Gitpod。它对公共存储库是免费的。

## 没有设置

并非所有项目都是平等的。我们维护了一个[开发者友好的 docker 镜像](https://github.com/gitpod-io/workspace-images/blob/master/full/Dockerfile),其中包含了所有常用的工具和版本管理器。但是你可以在一个 **.gitpod.yml** 文件中指向你自己的 docker 图像。Gitpod 会选择它，如果需要的话，甚至会构建映像。阅读 [Gero 的帖子](https://www.gitpod.io/blog/docker-in-gitpod)了解更多详情。

有了 Gitpod，贡献者就不需要浏览通常已经过时的设置说明列表。相反，他们不需要额外的努力就能得到手头项目所需要的东西。作为副作用，任何“在我的机器上工作”的场景都被消除了，因为每个团队成员都在云中的同类机器上使用相同的工作设置。由于 **.gitpod.yml** 是用代码版本化的，回到旧版本和分支也变得非常容易。我们称这个[开发环境为代码](https://dev.to/blog/dev-env-as-code)。

为了优化体验， **.gitpod.yml** 接受了[的进一步配置](https://dev.to/docs/40_Configuration)，在这里你可以在多个终端中自动启动构建、观察器和开发服务器。您可以配置它们在 IDE 布局中的显示方式，并连接所有的 web 端口和预览。

作为项目负责人，你应该尽你所能简化参与者的体验，这样每个人，包括你自己，都可以专注于创新和编写代码。Gitpod 让您可以自动化设置，并确保开发人员可以访问真正准备好编码的开发环境。

一个很好的例子就是 [ssloy 的 tinyraytracer 项目](https://github.com/ssloy/tinyraytracer/wiki)。这是一个用 C++编写的光线追踪教程，它被配置为运行构建并自动打开渲染的图形。作者甚至在教程的各个步骤中添加了 Gitpod 按钮，以便更容易理解。

[![Tinyraytracer](img/e1a0aafba5111d664352d721a38b4bc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7cx6VH_3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5iz76lxsulimj6qoh71e.png)

## 少等

每个开发人员都必须定期经历一点额外的摩擦:

运行构建并下载依赖项。

从今天开始，[新的 Gitpod 应用程序在 GitHub marketplace](https://github.com/marketplace/gitpod-io) 上可用。它是同类产品中的第一款，也是新 IDE 类别中的唯一一款。

一旦你为你的 GitHub 库安装了应用程序，它将在每次提交时预构建开发环境。因此，当任何人在您的项目上打开 Gitpod 环境时，dev 环境会如上所述打开。但是现在构建已经完成，所有的依赖项都已经存在了。

我们称这个特性为预建工作区，你可以在 [Chris 的帖子](https://www.gitpod.io/blog/prebuilds)中了解更多信息。

[![Prebuilt Workspaces](img/3bfd9dc32192c6419035eef0e2e8b25a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84i7n0nm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4q8342azejren48asati.png)

## 更流

根据您来自的 GitHub 页面，您的开发环境会以不同的方式打开。例如，你可以打开分支或文件，只需进入相应的 GitHub 页面，按下那里的按钮(或在 URL 前加前缀)。

当来自一个问题时，Gitpod 理解您想要修复它，所以您将获得一个新的本地分支，并且提交消息准备有对该问题的引用。

从一个拉请求启动一个工作区将在代码审查模式下打开。更改列表在左侧打开，因此您可以轻松地浏览它们，并在右侧的 diff 编辑器中查看详细信息。当然，您可以在 IDE 中进行注释，甚至提交您的代码评审。

即使您更喜欢在本地机器上进行开发，能够在浏览器中进行[深度代码审查](https://www.gitpod.io/blog/when-code-reviews-lgtm)也是改进工作流程的一个很好的方式。

[![Inlined Code Review](img/caf6e57452155285cc1e7e60a9c38536.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IIycrd8J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6vmtl1fb5lfof6w374pa.png)

## 运行在云端的完整 IDE

虽然 Gitpod 保留了在线编码操场和沙箱的便利性和可访问性，但它不是一个玩具，而是一个运行在云中的完整开发环境。

我们与谷歌、爱立信、Arm 和红帽合作开发开源项目 [Eclipse 忒伊亚](https://theia-ide.org)。该项目将 [VS Code](https://code.visualstudio.com/) 的最先进技术和 UX 放入基于一个为云优化的可扩展架构的浏览器中。

Gitpod 已经预装了几个 VS 代码扩展(比如来自微软自己扩展的 Go 支持)。我们将很快添加更多流行的 VS 代码扩展，稍后允许用户定义他们想要的任何扩展。

除了最好的代码编辑器之一，Gitpod 环境还提供了由运行在云中的 Linux 容器支持的全功能终端。因此，您可以获得与本地使用的命令行工具相同的命令行工具，以及当今最热门的 IDE 技术。所有这些都在浏览器选项卡中完成。

## 协作

Gitpod 有两个支持协作的主要特性。

*   如果你想和同事一起查看代码，一起寻找 bug 或者进行结对编程，那么与其他用户共享运行的开发环境会很方便。合作者将看到与您看到的相同的代码，甚至终端也被同步和共享。

*   快照在很多方面都很有用。简而言之，它们允许在任何时间点获取开发环境的不可变副本。你可以在任何你想要的地方分享提供的链接。用户打开这样的快照链接将获得环境的精确克隆，包括所有状态，甚至 UI 布局。这个特性非常适合共享问题中的可复制内容，也非常适合博客文章或堆栈溢出的解决方案。

[![Collaboration](img/9f0192b154b8c7865b7eed81d491d566.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BRCzmW46--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/olbfzi8zkdz8qxyq6a8m.png)

## 定价

离开测试版的一个重要部分是找到一个好的定价模型来满足不同用户群的需求。

我们认为，通过提供以下个人订阅选项，我们已经找到了一个很好的平衡:

*   一个每月免费 100 小时的开源开发计划
*   为私人工作的个人提供每月 100 小时的薪酬计划
*   一个**付费无限计划**供专业使用

(我知道你们中的一些人很幸运能够做专业开源。在这种情况下，决定什么最适合你；-))

我们还提供**团队订阅**，允许经理们购买一批座位并分配给团队。

当然，一个特殊的**学生折扣**正在准备中，如果你的主要 GitHub URL 属于一个教育机构，就有资格享受。

## 谢谢你

我想借此机会感谢所有帮助 Gitpod 成为现实的人:所有在测试阶段提供了[巨大反馈](https://github.com/gitpod-io/gitpod/issues)的[热情](https://twitter.com/JeremyJaydan/status/1096742668137246721) [早期](https://twitter.com/RalfDMueller/status/1054079167841660928) [采纳者](https://twitter.com/doppelganger9/status/1088926330857680896)；通过识别 bug 和分享用例来提供帮助的贡献者；当然还有 [Eclipse 忒伊亚社区](https://github.com/theia-ide/theia/graphs/contributors)满怀激情地为未来开发开源 IDE 平台。

此外，我要感谢来自 [Reify](https://www.reifyworks.com/) 的 Michael Bernstein 澄清了我们与 Kelly Fitzpatrick 和 [Redmonk](https://redmonk.com/) 团队的其他成员的信息，感谢他们的指导和有益的反馈。

最后，当然，我想向我在 [TypeFox](http://typefox.io/about) 的朋友们发出一声大喊。你真是太棒了，能和你一起开发这么棒的产品是我最想要的。

<sub><sup>(本帖首发于 2019 年 4 月 5 日 https://www.gitpod.io/blog/gitpod-launch/[)。](https://www.gitpod.io/blog/gitpod-launch/)</sup></sub>