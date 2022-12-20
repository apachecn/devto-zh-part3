# GitLab CI 现在与 Zeit 集成

> 原文：<https://dev.to/sirhamy/gitlab-ci-integration-with-zeit-now-15k1>

在过去的一个多月里，我一直在做一个名为[我会得癌症吗？](https://dev.to/projects/willigetcancer)这给了你患癌症几率的信息。当决定使用什么工具时，我想尝试一下无服务器，因为虽然[我以前在自己的服务器上建过网站](https://dev.to/projects/iamhamy)，但工作量很大，我想看看能不能用#优化来做得更快更便宜。

对于这个任务，我选择了 [Zeit Now](https://zeit.co/now) 平台，因为它有一个相当好的免费层，并且在一些 [NextJS](https://nextjs.org/) 文档中，并且被证明易于使用。我开始手动将我的项目部署到 Now，然后将其别名化到我的 url，但这很快就变得很烦人，所以我决定实现 CI/CD 来帮助我的管道自动化。

几个月前，我转而使用 GitLab 作为我的主要回购平台，并且已经利用 [GitLab CI](https://about.gitlab.com/product/continuous-integration/) 构建了部署管道，因此对我来说，1)在一个有效的平台上构建，2)在我所熟知的领域内构建，是一个自然的选择。

这就是我来的地方，在这里我将详细说明如何连接您的 GitLab repo，以便它将您的项目部署到 Now，然后将其别名化到您的自定义域。

# GitLab CI 和 Now CLI

现在，在我们确切地了解如何进行集成之前，我们需要了解一些关于我们将使用的各个工具的先决条件，以便我们知道最终集成中会发生什么。

## GitLab CI

GitLab CI 是 GitLab 生态系统中的一个流程，它启动了可定制的构建/部署流程。

基本上，GitLab CI 从提交开始，在 repo 中寻找一个名为`.gitlab-ci.yml`的顶层文件，如果找到，解析它以决定它应该做什么。这就是 CI 定制的用武之地。如果找到了该文件，并且格式正确，GitLab 将旋转您选择的容器映像(从一些批准的容器中选择),并提供一个到容器内 shell 的接口，您可以从该接口运行任意命令。

通过这个简单的功能，你实际上可以做很多事情。举个例子，您可能从 shell 本地运行的命令现在可以通过 GitLab 自动执行:

*   `npm install`
*   `git build`
*   `docker run my-test-container`
*   `now // this is the one we really care about for this tutorial`

你可以在 GitLab 的官方 CI/CD 文档中了解更多信息。

## 现在的 cli

*   现在的 cli 是什么
*   如何使用它
*   如何设置您的自定义域

[now cli](https://zeit.co/docs/) 为 now apis 提供了符合人体工程学的包装。出于我们的目的，我们实际上只需要做两件事:1)将我们的项目部署到 now 服务器，2)将我们的域命名为最新的 now 部署。

在本地运行时，我们可以用两个 now 命令来完成这个任务:

```
cd myProjectDirectory
now # this does the deploy
now alias mysupercoolsite.tld # this aliases your custom domain to the last deploy 
```

*阅读更多关于[如何使用 now](https://zeit.co/docs/v2/domains-and-aliases/adding-a-domain) 设置自定义域名的信息。*

# GitLab CI 与现在的整合

现在，我们对将要使用的各个工具有了一点了解，我们可以继续集成了。

为此，我将把现有的 GitLab CI 脚本从[我会得癌症吗？](https://dev.to/projects/willigetcancer)按现状讨论这些事情的作用。

```
# in GitLab repo .gitlab-ci.yml

# 1
image: node

# 2
stages:
  - deploy

# 3
build_and_deploy:
  stage: deploy
  script:
    - cd web # navigate to source directory
    - npm install
    - npm install -g --unsafe-perm now
    - now --token=$NOW_TOKEN
    - now alias willigetcancer.xyz --token=$NOW_TOKEN
  only:
    - master 
```

*代码摘自[SIRHAMY/will-I-get-cancer](https://gitlab.com/SIRHAMY/will-i-get-cancer/blob/master/.gitlab-ci.yml)，2019.03.10*

*   `image: node`

在每个 GitLab CI 文件的开始，我们必须告诉它我们希望它使用什么基本容器映像来运行。它来自 Docker Hub，因此如果您希望在管道中使用的映像存在于那里，您应该不会有问题。在这里，我说我想使用正式的节点映像，因为我使用 npm 进行包管理，并希望它全部预安装/配置。

*   阶段

这可能正是你的直觉。这里你说你的管道需要的阶段和你希望它们运行的顺序。稍后，当我们进入具体步骤时，您会注意到我们标记了给定步骤应该在哪个阶段运行，并且它将在该阶段运行。

*   步伐

在这里，我创建了一个名为`build_and_deploy`的步骤，并将其配置在我的`deploy`步骤中。它所做的是导航到我的源文件所在的文件夹，安装所有依赖项，安装`now`可执行文件，使用我在 now 中创建的令牌和在 GitLab CI 中配置为变量的[立即运行，然后用我的自定义域作为我的新部署的别名。](https://docs.gitlab.com/ee/ci/variables/)

在底部，我将部署步骤配置为仅在主分支被更新时发生，以防止随机特性分支泄漏到产品中。

# 鳍

就是这样！这篇文章有点罗嗦，但希望它能帮助你建立自己的 Now / GitLab 集成。如果你有任何问题，我很乐意回答！

如果你想要更多这样的内容，我会定期在[HAMY 上写一些我正在构建的东西。实验室](https://dev.to/)和你可以[通过订阅](https://iamhamy.xyz/subscribe)通过电子邮件获得更新。