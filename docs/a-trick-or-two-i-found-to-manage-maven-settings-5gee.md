# 我发现的一两个管理 maven 设置的技巧

> 原文：<https://dev.to/sammyrulez/a-trick-or-two-i-found-to-manage-maven-settings-5gee>

# 我发现了一两个管理 maven 设置的技巧

几周前，我基于在 Bitbucket 管道上运行的 Maven 建立了一个最小的持续集成解决方案。为了框定这个问题，让我们假设这是一个带有多个工件源的 git 单一回购。其中一些工件是可运行的应用程序，但其他工件是我必须部署在私有 maven 存储库中的客户端库。现在我遇到了一个安全问题和一个道德问题:maven 将访问私有存储库的凭证存储在 settings.xml 配置文件中。尽管每个人都使用全局/系统范围的设置，但是也可以使用-s cli 开关用本地设置覆盖 xml。根据 maven 的文档，不可能从命令行传递存储库凭证。现在，我肯定不想提交一个包含我的私有 maven 存储库凭证的文件。为了持续集成，我甚至不想将 settings.xml 文件提交到我的 git repo 中。许多 CI 产品要求在根存储库中有一个配置文件，描述构建的动作流(bitbucket-pipelines.yml):这是必要的，也是我在项目中所能忍受的最大入侵级别。所以我使用 Maven 的环境变量替换特性来解决第一个问题。在一个定制的 settings.xml 文件中，我为我的私有 repo 定义了服务器条目，但是我只为环境变量放置了占位符。

然后我将 my.repo.user 和 my.repo.password 定义为 Bitbucket 管道系统中的环境变量。

但是我的 git repo 中仍然有一个流氓设置 xml。为了消除这种情况，我认为可以将它嵌入到 bitbucket-pipelines.yml 文件中，这是我想为 CI 内容维护的唯一文件。所以我对它进行了 base64 编码，并在构建过程中加入了解码步骤。

Et voilà一个干净安全的解决方案，将我的工件部署到来自 bitbucket(或类似 CI 产品)的私有回购中