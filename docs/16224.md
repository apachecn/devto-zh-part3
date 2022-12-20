# 用 GolangCI-Lint 改进 Go 代码

> 原文：<https://dev.to/renatosuero/improve-the-go-code-with-golangci-lint-2do6>

这是在将提交发送到存储库之前运行的一个很好的工具(也许您可以使用 [git hook](https://dev.to/shameemreza/3-tricks-to-automate-development-tasks-with-git-hooks-2dah) )。

还有另一种选择 [gometalinter](https://github.com/alecthomas/gometalinter) 但是 golangci-lint 比 gometalinter 更快。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[golangier 功能区](https://github.com/golangci/golangci-lint)

### Go 用快速棉绒跑步器

<article class="markdown-body entry-content container-lg" itemprop="text">

[![golangci-lint logo](img/7afbd0be4d46c9f3a784ba93702092ed.png)](https://github.com/golangci/golangci-lintassets/go.png)

### 戈朗契功能区

Go 用快速棉绒跑步器

* * *

`golangci-lint`跑得很快。它并行运行 linters，使用缓存，支持`yaml`配置，与所有主要的 IDE 集成，并包含几十个 linters。

## 安装`golangci-lint`

*   [在我的机器上](https://golangci-lint.run/usage/install/#local-installation)；
*   [在 CI/CD 系统上](https://golangci-lint.run/usage/install/#ci-installation)。

## 证明文件

文档托管在[https://golangci-lint . run](https://golangci-lint.run)上。

## 徽章

[![Build Status](img/52f88f2aca03e142ecf4896e5d3fda25.png)](https://github.com/golangci/golangci-lint/workflows/CI/badge.svg)[![License](img/4c5aeda6d88c424fcb56ab6bde071536.png)](https://github.com/golangci/golangci-lint/LICENSE)[![Release](img/92a14301f15b5694b7aba04e2597f322.png)](https://github.com/golangci/golangci-lint/releases/latest)[![Docker](img/c9875443866e159a285ab732b2b1e3f9.png)](https://hub.docker.com/r/golangci/golangci-lint)[![GitHub Releases Stats of golangci-lint](img/4de21f4e887491b9f9a20469a92a0585.png)](https://somsubhra.github.io/github-release-stats/?username=golangci&repository=golangci-lint)

## 贡献者

这个项目的存在要感谢所有做出贡献的人。[如何投稿](https://golangci-lint.run/contributing/quick-start/)。

### 核心团队

<details><summary>About core team</summary>

GolangCI 核心团队是一群对项目和社区表现出持久热情的贡献者。GolangCI 核心团队拥有对回购的 GitHub 管理权限。

#### 责任

核心团队有以下职责:

1.  能够回答关于愿景和未来的高层次问题。
2.  能够审查长期/被遗忘的拉动式请求。
3.  偶尔检查问题，提供意见，并用 GitHub 问题标签进行分类。
4.  寻找 GolangCI 社区中有前途的成员…</details> </article>

[View on GitHub](https://github.com/golangci/golangci-lint)
To run the tools is pretty simple, you need to run:

```
golangci-lint run ./... 
```

Enter fullscreen mode Exit fullscreen mode

如果你想使用 docker，*你唯一需要做的就是配置这个卷来使用你的项目路径*。

```
docker run --rm -it -v $GOPATH/src/github.com/golangci/golangci-lint:/go/src/github.com/golangci/golangci-lint golangci-lint run ./... 
```

Enter fullscreen mode Exit fullscreen mode

乡亲们就这些，希望能对你们有用=)