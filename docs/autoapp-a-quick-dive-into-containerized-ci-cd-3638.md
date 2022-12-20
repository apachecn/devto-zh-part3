# autoapp:快速了解集装箱化 CI/CD

> 原文：<https://dev.to/derek/autoapp-a-quick-dive-into-containerized-ci-cd-3638>

我最近一直在深入研究和审查许多新技术，以提高自己和团队的生产率。我参与的一些事情:

*   [k8s](https://kubernetes.io/docs/concepts/)
*   [三角帆](https://spinnaker.io)
*   [无人机](https://drone.io)
*   dockers 注册表:
    *   [码头](https://quay.io)
    *   [GCR](https://cloud.google.com/container-registry/)
    *   [私有 docker-registry](https://docs.docker.com/registry/deploying/)

因此，我想我还不如创建一个实验项目，并记录下过程中的步骤，供其他人学习和参与。让我先说明一下，这主要是出于教育目的，不建议用于生产用途。还要注意，在命令行`$`中确实需要一些安慰。

欢迎拉取请求😍

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【dereken】](https://github.com/derekahn)/[【auto app】](https://github.com/derekahn/autoapp)

### 带有简单 go 应用程序的 CI/CD 示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# 自动应用程序<g-emoji class="g-emoji" alias="robot" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f916.png">🤖</g-emoji>

[![license](img/5b18157bd173af5405c74fe1de8b69f8.png)](https://github.com/derekahn/autoapp/LICENSE)[![Go Report Card](img/1c06ed891dd4c27dfeadf59404c6d9f8.png)](https://goreportcard.com/report/github.com/derekahn/autoapp)[![Codacy Badge](img/86745c9ed4dea3d53b9944c6d3406832.png)](https://www.codacy.com/app/git.derek/autoapp?utm_source=github.com&utm_medium=referral&utm_content=derekahn/autoapp&utm_campaign=Badge_Grade)[![Codacy Badge](img/cc1b07404d81ff7153b90582d3a4b25b.png)](https://www.codacy.com/app/git.derek/autoapp?utm_source=github.com&utm_medium=referral&utm_content=derekahn/autoapp&utm_campaign=Badge_Coverage)[![Build Status](img/dad00b5ea1280c736f170bff72e54057.png)](https://cloud.drone.io/derekahn/autoapp)[![Docker Repository on Quay](img/5660ec9b3988301a04b50a7339951627.png "Docker Repository on Quay")](https://quay.io/repository/derekahn/autoapp)[![GitHub tag (latest SemVer)](img/63a027802fba982207ea17e9125c0952.png)](https://camo.githubusercontent.com/09d7775f8467a3a81e06a411ebfa27fa1e67b179eea14b9f68616954121eae1f/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f7461672f646572656b61686e2f6175746f6170702e737667)

一个 CI/CD 示例，带有一个利用多级 docker 构建的简单 go 应用程序。

## 奔跑

### <g-emoji class="g-emoji" alias="whale" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f433.png">🐳</g-emoji>化

```
# creates a new image
$ make build
# runs the image
$ make run
```

Enter fullscreen mode Exit fullscreen mode

### <g-emoji class="g-emoji" alias="hamster" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f439.png">🐹</g-emoji>化

```
# installs deps etc
$ make install

# execute binary
$ ./bin/cmd
```

Enter fullscreen mode Exit fullscreen mode

## 例子

[![example view](img/61133b020a584b80ecb697d2e0489350.png)](https://github.com/derekahn/autoapp./assets/example.png)

## 环境变量

```
# defaults to "8080"
PORT=3000

# defaults to "Gopher"
NAME="Edgar Allan Poe"

# forces tests to fail
FAILED=true
```

Enter fullscreen mode Exit fullscreen mode

## 命令

运行`make help`列出可用命令:

```
 $  make help
Choose a command run in autoapp:

 install    Install missing dependencies. Builds binary in ./bin
 build      Creates a docker image of the app
 fail       Forces a failed docker build of the app due to failing tests
 run        Runs the current docker image on port 8080
 clean      Clean build files. Runs `go clean` internally
 fmt        Runs gofmt on all source files
 test       Runs all the tests.
 coverage   Tests
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/derekahn/autoapp)

意见:

*   我个人真的💚[无人机](https://drone.io)；它的易用性，文档，和容器化是惊人的！至少与[特拉维斯](https://travis-ci.org/)、[圈-ci](https://circleci.com/) 或[詹金斯](https://jenkins.io/)相比。有趣的事实...它几乎可以部署在任何东西上。此外，它看起来是 k8s 上短命吊舱的完美匹配。
*   我当然更喜欢 [Quay 的](https://quay.io) UI/UX，这也是目前最受欢迎的🤷🏽‍♂️.但它也是最贵的，所以我想你会得到你所付出的。
*   [大三角帆](https://spinnaker.io)是一个🐻。但不用说它超级强大灵活。一旦你知道如何浏览深奥的文档，你就可以释放这种力量。他们确实有 [slack](https://join.spinnaker.io/) 和[论坛](https://community.spinnaker.io/)，但是我仍然发现在配置等方面获得帮助/建议有点困难。但是一旦它开始运行，一切都是✅.