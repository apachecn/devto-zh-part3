# 使用 Docker 清单将多拱 Docker 映像发布到单个 Repo

> 原文：<https://dev.to/toolboc/publish-multi-arch-docker-images-to-a-single-repo-with-docker-manifests-329n>

## 简介

Docker 是开发人员武器库中极其强大的工具。它允许在隔离的容器中运输应用程序，容器中包含运行应用程序所需的所有依赖项，而无需修改主机操作系统(除了安装 Docker 本身之外)。这样，只需从 Docker 存储库中下载一个包含应用程序的已发布映像，就可以在各种场景中测试、部署和发布应用程序。这在大多数情况下都是可行的，但是如果您在像 Raspberry Pi 这样的 arm32 设备上拉下一个旨在用于 x64 架构的图像，会发生什么情况呢？(剧透:它不会运行)如果 repo 根据你的主机 OS 自动为你的架构提供正确的映像不是很好吗？

如果你不熟悉 Docker 及其特性/功能，我的同事

[![softchris image](img/11287239c3fc0c954046778309092f72.png)](/softchris)

## [克里斯·诺林](/softchris)

[https://twitter.com/chris_noring Cloud Developer Advocate at Microsoft, Google Developer Expert](/softchris)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)克里斯 _ 诺林](https://twitter.com/chris_noring) [ ![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)软糖克里斯](https://github.com/softchris)

recently published a[![softchris image](img/11287239c3fc0c954046778309092f72.png)](/softchris) [## 5 部分 Docker 系列，从初学者到大师

### 克里斯·诺林 3 月 10 日 192 分钟阅读

#docker #beginners #devops #showdev](/softchris/5-part-docker-series-beginner-to-master-3m1b)that will take you from beginner to master. Once you have the basics down, you should be ready to proceed with the content in this article.

在本文中，我们将假设您了解如何将图像推送到 Docker repo，并且您希望发布一个自动解析到主机架构的多拱图像。让我们从为什么我们想要或者需要这样做的一些概述开始。

## 为什么我们有时需要多拱图像

您是否曾经有意或无意地试图从一个 Docker 映像启动一个容器用于一个外来的架构？您可能已经注意到您遇到了以下错误:

*exec 用户进程导致“exec 格式错误”*

此错误表明，有问题的映像是为无法在主机操作系统下运行的体系结构构建的。这方面的一个例子是下载为 x64 架构创建的映像，并尝试在 arm32 平台上运行它，如 Raspberry Pi。所述设备的处理器架构(在本例中为 Raspberry Pi)不能运行 x64 代码。这是不可能的。

图像出版商试图用各种方法来解决这个问题。更流行的方法之一是将图像发布到多个存储库，每个存储库对应一个架构。例如， [nginx/nginx](https://hub.docker.com/_/nginx) 的主回购在 https://hub.docker.com/_/nginx 的[进行。自述文件提到了对各种平台的支持，包括:amd64、arm32v6、arm32v7、arm64v8、i386、ppc64le 和 s390x。](https://hub.docker.com/_/nginx)

您会注意到 arm32v7 回购的托管地:[https://hub.docker.com/r/arm32v7/nginx/](https://hub.docker.com/r/arm32v7/nginx/)

而 amd64 回购托管在:
[https://hub.docker.com/r/amd64/nginx/](https://hub.docker.com/r/amd64/nginx/)

这意味着为了拉 arm32 风味的 nginx 我们会发出:
*docker 拉 arm32v7/nginx*

同样，对于 arm64 的味道，我们会发出:
*docker pull amd64/nginx*

这是解决问题的一种方法，但它需要设置单独的回购协议，这不允许我们用一个一刀切的命令来解决我们的主机架构，如:
*docker pull nginx/nginx*

因此，如果我们想在 kubernetes 上为 nginx 部署一个能够在 x64 机器和 Raspberry Pi 上运行的一刀切的 helm 图表，这实际上并不可行，因为我们需要创建两个图表，其中的值指向我们预期架构的适当映像报告。

您可能还注意到,/arm32v7/*和/amd64/*回购需要特殊权限才能发布到，因为它们在技术上都是社区组织。因此，你可能会看到一些像 Alex Ellis 这样的发行商使用自己的公开回购来指定回购名称中的架构，例如: [alexellis2/nginx-arm](https://hub.docker.com/r/alexellis2/nginx-arm) 。

关键的一点是，在这些情况下，维护人员通过创建特定于架构的 repos 来允许多拱映像。

让我们看看回购维护者使用标签解决这个问题的另一种方式。在针对 [linki/chaoskube](https://hub.docker.com/r/linki/chaoskube/tags) 的 Docker repo 中有一个很好的例子。

请注意，每个映像都是按平台标记的，并且都托管在一个存储库中:

v 0 . 13 . 0-arm 64v 8
v 0 . 13 . 0-arm 32v 6
v 0 . 13 . 0-PPC 64 le

这更接近于我们想要实现的目标，但是仍然需要为每个特定的体系结构设置一个特定的标签，所以我们也不能执行一刀切的安装。我们需要为预期的架构安装正确的标签。

## 码头工人舱单

使用特定于架构的标签和关联的 Docker 清单的组合，我们可以从 repo 中获得通用的架构无关图像。让我们先介绍清单的概念，然后我们将讨论如何做。

从 [Docker 文档](https://docs.docker.com/engine/reference/commandline/manifest/):

Docker 清单包含有关图像的信息，例如图层、大小和摘要。docker manifest 命令还为用户提供了额外的信息，如操作系统和构建映像的架构。

理想情况下，清单列表是从不同 os/arch 组合的功能相同的映像中创建的。由于这个原因，清单列表通常被称为“多拱图像”。然而，用户可以创建指向两个图像的清单列表——一个用于 amd64 上的 windows，一个用于 amd64 上的 darwin。

## Docker 清单入门

Docker 清单功能目前需要启用实验功能。

要在 Linux 上实现这一点，您可以导航到 *~/。docker* 并在 *config.json* 文件中添加
**【实验】:【启用】，**
，这样看起来就像这样:

```
{
        "experimental":"enabled",
        "auths": {
                "https://index.docker.io/v1/": {
                        "auth": "0000000000000000000000=="
                }
        },
        "HttpHeaders": {
                "User-Agent": "Docker-Client/18.09.7 (linux)"
        }
} 
```

要在 Windows 上启用，突出显示系统托盘中的 Docker 图标，选择“设置”，然后选择“守护程序”，并选中复选框以启用“实验功能”。

通过运行
*docker 版本*
检查实验功能是否启用，并确认实验部分设置为:“真”

## 在真实项目中使用 Docker 清单

我目前在 Github 上主持一个[项目，该项目需要为多种架构构建容器映像。该项目还采用了一个](https://github.com/toolboc/azure-iot-edge-device-container) [kubernetes helm 图表来将这些图像](https://github.com/toolboc/azure-iot-edge-device-container/tree/master/helm/azure-iot-edge-device-container)部署到一个集群中，并且需要是通用的。理想情况下，当我执行“docker pull tool BOC/azure-IOT-edge-device-container”时，我希望它为我发出命令的主机架构解析适当的映像。例如，如果我在我的 AMD64 桌面上运行这个命令，我想下载针对 x64 架构的最新映像。类似地，如果我在 Raspberry Pi 上运行这个命令，我想下拉目标为 arm32 的最新图像。

我将向您展示我是如何使用 docker manifest 命令来实现这一点的。

首先，我为我的目标平台构建所有映像，并按如下方式标记它们:

*tool BOC/azure-IOT-edge-device-container:x64-最新*
*tool BOC/azure-IOT-edge-device-container:arm 32-最新*
*tool BOC/azure-IOT-edge-device-container:aarch 64-最新*

接下来，我创建一个包含这些图像的清单:

*docker manifest create tool BOC/azure-IOT-edge-device-container:latest \
tool BOC/azure-IOT-edge-device-container:x64-latest \
tool BOC/azure-IOT-edge-device-container:arm 32-latest \
tool BOC/azure-IOT-edge-device-container:aarch 64-latest*

现在我们来回顾一下:
*docker 清单检查工具 BOC/azure-IOT-edge-device-container:最新*的输出

{
"schemaVersion": 2，
" media type ":" application/vnd . docker . distribution . manifest . list . v2+JSON "，
" manifests ":[
{
" media type ":" application/vnd . docker . distribution . manifest . v2+JSON "，
"size": 3254，
" digest ":" sha 256:80f 269 ee91e 360538 e01 F5 b 03

请注意，输出已经自动选取并适当地注释了架构和操作系统。

如果我们需要手动指定注释，我们可以运行:
*docker manifest annotate toolboc/azure-IOT-edge-device-container:latest \ toolboc/azure-IOT-edge-device-container:arm 32-latest-arch arm-OS Linux*

如果我们想要更新清单中引用的图像，我们可以适当地重新构建和标记，然后运行:

*docker 清单创建 tool BOC/azure-IOT-edge-device-container:最新\
-修改 tool BOC/azure-IOT-edge-device-container:x64-最新\
-修改 tool BOC/azure-IOT-edge-device-container:arm 32-最新\
-修改 tool BOC/azure-IOT-edge-device-container:aarch 64-最新*

最后，为了推送我们的回购，我们发出:
*docker 清单推送工具 BOC/azure-IOT-edge-device-container:最新的*

假设我们的图像已经被推送到 repo，我们可以通过跳到 Raspberry Pi 和 AMD64 桌面并运行:
*docker pull tool BOC/azure-IOT-edge-device-container*来进行测试

如果一切顺利，我们应该能够通过运行
*docker inspect toolboc/azure-IOT-edge-device-container*来验证是否为每个架构下载了合适的映像

## 结论

我们已经展示了通过利用清单，使用一刀切的 docker pull 命令为多种架构交付 Docker 映像是可能的。虽然这种机制很强大，但它仍被认为是“实验性的”,目前只能从 cli 进行操作。这有点遗憾，因为如果能够在 Dockerhub 内部查看和处理清单就好了。我的期望是，这个特性仍然在某种程度上的建设中，并可能证明在未来更有用一点。我希望这篇文章有助于向您准确展示将多拱图像发布到单个 Docker repo 所需的内容。如果您有任何反馈或建议，请在评论中告诉我！

直到下一次，

黑客快乐！