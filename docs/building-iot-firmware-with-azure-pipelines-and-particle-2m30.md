# 使用 Azure Pipelines 和 Particle 构建物联网固件

> 原文：<https://dev.to/bsatrom/building-iot-firmware-with-azure-pipelines-and-particle-2m30>

*TL；DR 使用 Azure Pipelines 和 Particle CLI，可以轻松创建管道来自动构建物联网设备固件、发布二进制文件，甚至运行自动化测试！*

曾经有过一个一次又一次出现的爱好项目吗？你得到灵感开始，疯狂地工作一段时间，然后把它放在一边，因为生活和工作发生了。

或者你被这个东西吓到了，然后慢慢地(或很快地)后退？

[![](img/1543ae0868e7bdabe7c6bf4affdb2abf.png)](https://i.giphy.com/media/rVHcovSkVKUuY/giphy.gif)

猫为什么害怕香蕉？也许他只是去酮。

我知道那种恐惧的感觉，尤其是当它涉及到爱好项目的时候。

然而，有一些永远不会真正消失。你反复思考它们。你偶尔会对它们稍加修改。你不断在你的愿望清单上添加功能。灵感(或恐惧)永远不会真正消失，你的某一部分只知道你有一天会完成那件事。

我的一个这样的项目是 [Brew Buddy](https://github.com/bsatrom/brew-buddy) 。一个[粒子动力](https://particle.io)家酿监测装置，我在三年前开始使用。我用来自 Sparkfun 的[粒子光子](https://docs.particle.io/photon/)、一个 [Adafruit](https://adafruit.com) TFT 和一个 [K 型热电偶突破开始了这个项目。在从奥什公园](https://www.sparkfun.com/products/13266)[得到真正的多氯联苯之前，我在我的另一家工厂创造了我自己的多氯联苯。](https://oshpark.com/)

我甚至制作了几个关于这个过程的 YouTube 视频。

[![](img/4b7d4ac6188edae8510e1342cb9c1219.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QaqEhLGx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rcwnyj0uthbmmw91hz56.png)

我不打算链接到那些 YouTube 视频。不客气如果你足够努力地寻找你能找到他们，但是不要说我没有警告你。

碰巧的是，在一阵忙乱之后，我把这个项目束之高阁，转而做其他事情。测试 Brew Buddy 的每一次迭代都需要“真实世界”的测试，信不信由你，人类可以喝的啤酒量实际上是有限制的。

去年年底，Brew Buddy 又回到了我的脑海里。在粒子公司发布了他们的新的第三代设备(T1)，氩、硼和氙后不久，一个微弱的声音非常温柔地说:“T2，你知道，氩(T3)和 T4 对酿酒伙伴来说很棒。您还可以添加那些您一直在考虑的新功能。”

无力抗拒新硬件的诱惑，我在一月份重新开始了这个项目。我在我的 Twitch 频道上直播了很多我最初的工作，并在粒子博客上发表了一篇关于光子到氩端口的 T2 帖子。

我被吸了回去。

## 进入 Azure DevOps

我喜欢硬件项目的一个原因是，它们给我一个借口去探索我一直渴望尝试的相关主题或概念。例如，最初的 Brew Buddy 是进入 PCB 设计的门户，这在过去几年里已经成为我的一个困扰。

> ![unknown tweet media content](img/9d7e3f5c3b3e781b3fac400b1c45e1ed.png)![Brandon Satrom 🥕👖 profile image](img/f93119ff1b908c7f955c2e7574a35fcf.png)布兰登·萨特罗姆🥕👖@ brandonsatrom![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)除了我们的徽章设计，最近还做了一点 PCB 艺术。@mrtwinkletwink 有一些很棒的东西，我受到启发创作了这个。我给你一个垃圾熊猫徽章...[# badge life](https://twitter.com/hashtag/badgelife)2018 年 7 月 02 日晚 21 点[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1013890175355752448)20[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1013890175355752448)113

在这次更新中，我真的想探索固件/物联网开发的 DevOps 方面。我听说了很多关于 Azure DevOps 和[新管道](https://azure.microsoft.com/en-us/services/devops/pipelines/)功能的好消息，所以我决定尝试一下。

使用 Azure Pipelines 和 Particle CLI，我能够快速自动化构建 BrewBuddy 固件的过程，并在需要时发布二进制文件。这里有一个快速的步骤概述，我采取了这个工作。

## 1。设置 Azure DevOps

要使用 Azure Pipelines，您需要一个 Azure DevOps 帐户。微软有[大量关于这个过程的文档](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/index?view=azure-devops)，我不会在这里重复。前往 dev.azure.com[的](https://dev.azure.com)，做好一切准备。在创建项目之前，您需要创建一个 Azure DevOps 组织，以及一个 GitHub 帐户，这样您就可以将您的管道连接到相关的存储库。

[![My sparse DevOps dashboard](img/694604ce2a47f6e1dc35fa00b2ef02b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CVmvhG7A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kfcbges8prrpqoksfjza.png)

## 2。创建您的第一个管道

创建了 Azure DevOps 组织后，您就可以创建您的第一个管道了。如果你只是在探索管道，[微软有一些快速启动工具，你可以克隆出来尝试所有的东西](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started-yaml?view=azure-devops)。

当您创建一个管道时，您将有两种选择:在 YAML 中创建管道定义，或者通过可视化编辑器。每个都有[的利弊。我选择了 YAML 的方法，这样我就可以在我的](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/pipelines-get-started?toc=/azure/devops/pipelines/toc.json&bc=/azure/devops/boards/pipelines/breadcrumb/toc.json&view=azure-devops) [Brew Buddy repo](https://github.com/bsatrom/brew-buddy) 中版本化构建过程。

如果你准备开始一个真正的项目，第一步是为你的管道选择一个源回购，可以是 GitHub 或 Azure repos 中的回购。

[![](img/d7d7736480b09755f01f8f4eba2af01e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_jr_zEzU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rieqh4jqgn78no88by0a.png)

下一步是选择一个存储库，然后选择一个启动配置。这里有大量的选项，所以你应该找到适合你正在构建的东西。

[![](img/f527aed1e73b40b80adb537b73db959a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WxPRgUV4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ypn6bt9ga8ctmi9qn2s.png)

由于我正在构建一个固件项目，您可能会认为我选择了带有 GCC 选项的 C/C++。不是这样的！虽然我可以这样做，但我想用基于 npm 的 [Particle CLI](https://docs.particle.io/tutorials/developer-tools/cli/) 进行云构建，这意味着我不需要安装本地 GCC 工具链，即使在我的构建环境中也是如此。相反，我选择了 Node.js 选项，这给了我一个很好的预配置 YAML 文件，可以随时进行定制。

[![](img/a254229789f334da1a05d678e6a32fd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ger5afi0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i217zj18gxqp6fpsg6w6.png)

## 3。设置触发器并定义虚拟机池

默认管道将为您预定义一些东西，包括单个触发器和池。触发器定义何时运行构建管道，池指定希望管道在哪些类型的虚拟环境中运行。以下是我为我的构建所做的调整。

```
trigger:
- master
- feature/*

pool:
  vmImage: 'macOS-10.13' 
```

默认情况下，构建将在所有分支上触发。我稍微修改了一下，以建立在我创建的`master`和任何`feature/`分支之上。

对于 VM 池，我使用`macOS`进行构建，因为这与我的开发环境非常相似。Windows，Mac 和 Linux 都支持粒子 CLI，所以我真的不需要改变这一点，但我无法抗拒。我们生活在一个微软在 Azure 的 macOS 上提供自动化构建服务的世界。活着是多么美好的时光。

## 4。使用步骤来简化依赖项的安装、构建等

在 Azure 管道中，步骤构成了工作的主要部分。每个管道都可以定义一个或多个步骤来安装依赖项、运行脚本、构建工件、运行测试等等。出于这个项目的目的，从源代码到固件二进制文件，我需要采取五个步骤。

### 安装 Node.js

首先，我需要安装 Node.js。模板包含了这个任务，但我对它进行了调整，使其包含 Node.js 9.x.

```
- task: NodeTool@0
  inputs:
    versionSpec: '9.x'
  displayName: 'Install  Node.js' 
```

### 安装粒子 CLI

安装 Node.js(和 npm)后，下一步是全局安装 Particle CLI。

```
- script: |
    npm install -g particle-cli
  displayName: 'Install  the  Particle  CLI' 
```

### 登录到粒子 CLI

粒子 CLI 可以执行基于云的固件构建，但必须登录到自己的[粒子账户](https://login.particle.io)才能使用该功能。我有一个帐户和凭证可以被管道用于这项工作，但是我想以一种安全的方式指定这些凭证(而不是将我的用户名和密码提交给 source)。幸运的是，Azure Pipelines 通过每个管道的库提供了对变量的支持。我创建了一个名为`particle-secrets`的组，并用适当的凭证添加了`PARTICLE_USERNAME`和`PARTICLE_PASSWORD`变量。

[![](img/f0f10730b3c6a1c0eb2dc3455e1d2aaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzNrMCqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4yawv9isidxpmqeh2f2r.png)

要使用可变组，我可以在我的 YAML 顶部引用组。

```
variables:
- group: particle-secrets 
```

这允许我在`particle login`命令中引用这些变量。

```
- script: |
    particle login --username $(PARTICLE_USERNAME) --password $(PARTICLE_PASSWORD)
  displayName: 'Login  to  Particle  CLI' 
```

### 使用粒子 CLI 构建固件二进制文件

登录后，我可以使用`particle compile`命令构建一个二进制文件，目标是粒子氩和 0.9.0 版本的设备操作系统固件。

```
- script: |
    particle compile argon --target 0.9.0 brew-buddy-firmware --saveTo brewBuddy.argon.bin
  displayName: 'Build  Firmware' 
```

### 将固件二进制文件复制到暂存目录

有了二进制文件，我将把`.bin`文件直接复制到 staging 中。这允许我为最后一个任务选择二进制文件。

```
- task: CopyFiles@2
  inputs:
    sourceFolder: '$(Build.SourcesDirectory)'
    contents: 'brewBuddy.argon.bin'
    targetFolder: '$(Build.ArtifactStagingDirectory)' 
```

## 5。合并到 master 时创建一个 GitHub 版本

上述步骤在每次签入时运行，并针对我的所有分支，以确保我总是可以构建固件，并且不会将重大更改提交到我的项目中。

我在`feature/`分支中进行所有的开发，虽然我想持续运行构建，但我只想在一个特性分支被合并到主分支时发布新的固件。使用`condition`语句，我可以指定，如果前面的所有步骤都成功并且源分支是 master，那么我只想运行发布任务。

```
# GitHub Release
- task: GithubRelease@0 
  condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/master'))
  displayName: 'Create  GitHub  Release'      
  inputs:
    gitHubConnection: bsatromBB
    repositoryName: bsatrom/brew-buddy
    tagSource: manual
    tag: $(Build.BuildNumber) 
    assets: $(Build.ArtifactStagingDirectory)/*.bin 
```

作为这个最终任务的一部分，`assets`输入将获取我的固件二进制文件，并将其作为发布的一部分。

## 6。运行管道！

管道就绪后，下一步是运行它，这可以通过签入或在 DevOps 门户中手动触发来完成。在几个(十几个)调整之后，我很高兴在我的构建中看到一排排绿色。

[![](img/850a297d1038a5a258cd3598a7444e07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eHALHy7R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/424q9ef1cc6r57gtva6v.png)

而且，每一个合并到 master 的 PR 都会导致一个新的发布，带有一个自动生成的变更列表和一个闪亮的新二进制文件。

[![](img/b0c983a9a1b3dcad5159686174add5c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dW7q-lYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/93b3p6k9xficaaig4kwt.png)

*我的全管道 YAML 在 [Brew Buddy GitHub 回购](https://github.com/bsatrom/brew-buddy/blob/master/azure-pipelines.yml)T3】中可用*

## 接下来是什么？

到目前为止，我对 Azure Pipelines 的体验还算顺利。在创建可变组和正确完成 GitHub 发布任务时，我遇到了一些挫折，但在使用一个新的、不熟悉的服务时，这并没有超出人们的预期。我已经运行上述管道几个星期了，它对我的工作流程是一个很好的补充。

当然，作为一个不断修补的人，我还想做更多的事情。既然我已经自动构建了固件，为什么不使用 Particle CLI 对专用设备进行空中下载(OTA)固件更新呢？之后，为什么不使用[粒子变量、函数和发布/订阅原语](https://docs.particle.io/reference/device-os/firmware/argon/#cloud-functions)运行自动化集成测试呢？

如果一切顺利，我将在几周后查看关于该项目的另一篇文章。

或者我会被别的事情分心。

* * *

要关注我在 BrewBuddy 和其他粒子驱动物联网项目上的进展，请务必在 Twitch 上关注我，并在周一和周四与我一起享受直播乐趣！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[b satrom](https://github.com/bsatrom)/[brew-buddy](https://github.com/bsatrom/brew-buddy)

### Brew Buddy 项目的硬件、固件和软件项目

<article class="markdown-body entry-content container-lg" itemprop="text">

# 酿酒伙伴

[![Build Status](img/8007e9adcc6153f4dd64de4d1f715187.png)](https://dev.azure.com/brandon0360/BrewBuddy/_build/latest?definitionId=1&branchName=master)

BrewBuddy 项目的来源。包含主板硬件/布局、固件和 3d 打印外壳的来源。

</article>

[View on GitHub](https://github.com/bsatrom/brew-buddy)