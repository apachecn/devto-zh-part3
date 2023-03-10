# 文档是强大技术社区的关键

> 原文：<https://dev.to/andrii_fedotov/documentation-as-a-key-to-the-strong-tech-community-3h2k>

我相信所有伟大的项目都是从清晰的文档开始的。我的项目是一个新的开源协议，正在寻找建立一个围绕区块链网络安全的数字证书概念的社区。我看到我们的想法吸引了黑客马拉松、聚会和技术聊天的兴趣，但在 GitHub 上传播这些活动也是必不可少的。

我们准备了问答页面和简单的项目文档。但是现在我发现有必要对我们的 GitHub 库自述文件做更多的工作。据我所知，我们需要添加更多关于我们的代码挑战的问题，当前的功能，贡献政策，并留下一些关于开发团队的信息。我在网上看到了相当多关于 gif、彩色按钮、表格的建议，但不确定这不会激怒我们的回购客人。

我们还应该在我们的核心协议页面上添加什么，以使它更接近开源社区，并吸引它为项目做出贡献？我在这里添加了链接:

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[remme auth](https://github.com/Remmeauth)/[remme-core](https://github.com/Remmeauth/remme-core)

### 分布式公钥基础设施(dPKI)协议

<article class="markdown-body entry-content" itemprop="text">

# 刹车核心

[![CircleCI](img/4d3e1f6e8b119cb6c1bc8f6b57f4789c.png) ](https://circleci.com/gh/Remmeauth/remme-core) [ ![Docker Stars](img/1442d2582d8bb6e5df1e4609cb6213df.png)](https://hub.docker.com/r/remme/remme-core/)

## 如何运行节点

该节点在 Linux 和 macOS 上进行了测试。在 Windows 上运行可能需要对启动脚本进行重大修改。

目前无法将您自己的节点连接到测试网络。您将运行的所有节点都将在您自己的网络上工作。

你需要在你的机器上安装 Docker 和 Docker Compose。

### 对于最终用户来说

1.  从[版本](https://github.com/Remmeauth/remme-core/releases)版块(`<version_number>-release.zip`)下载最新版本。打开它。
2.  启动节点:打开解压文件夹内的终端，运行`./run.sh`。
3.  您现在可以使用我们的 RPC API 了。默认情况下，它在 [http://localhost:8080](http://localhost:8080) 上启动。API 端口可以在`config/network-config.env`文件中更改。

第一次运行时，您需要初始化 genesis 块。让它运行`./run.sh -g`。这将生成一个新的密钥对和生成块。

`run.sh`可用的标志有:

*   `scripts/run.sh`特色……

</article>

[View on GitHub](https://github.com/Remmeauth/remme-core)