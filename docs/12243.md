# 关于用 Java 进行 Docker 多阶段构建的完整指南

> 原文：<https://dev.to/charlesrich324/a-complete-guide-on-docker-multi-stage-build-with-java-1mi9>

随着 Docker CE 17.05 (EE 17.06)的发布，它引入了开发人员的世界，通过将映像构建过程划分为多个阶段，或者您可以称之为多阶段构建，来帮助创建瘦 Docker 映像。

使用 Docker 多阶段构建，您现在可以通过另一个阶段重用一个阶段中产生的工件。这个特性的最终好处是它有助于创建更小的图像。以前，为 Java 应用程序构建 Docker 映像很困难，因为它涉及到构建整个应用程序，然后将生成的工件打包到映像中。

java 开发人员不得不强制使用 Maven 或 Gradle 来构建 JAR 或 WAR 文件。使用 Maven 基本映像，需要从已配置的存储库中下载依赖项，以将它们保存在映像中，用于构建应用程序。本地存储库中 jar 的数量主要取决于 pom.xml 依赖项的数量。

使用旧的方式构建 Dockerfile 产生了几个问题:
通过使用 Maven，您可以限制映像中可用的功能。为此，你需要[下载 WildFly 并明确配置它](http://www.mastertheboss.com/soa-cloud/docker/getting-started-with-docker-and-wildfly)。
下载 WildFly 并显式配置。
当您构建驻留在映像中并在运行时导致映像大小不必要膨胀的工件时，所有 maven 依赖项都会被下载。
创建映像后，在打包工件和集成测试之前运行单元测试，这些测试不需要测试依赖关系就可以存在于[生产映像](https://docs.docker.com/compose/production/)中。
当您将主 docker 文件拆分为两个不同的 docker 文件时，需要分别维护多个 docker 文件。

让我们看看如何使用多阶段构建来解决这些问题。

什么是 Docker 多阶段构建？
首先，回忆一下什么是 Docker？

Docker 是一种允许创建和使用 Linux 容器的容器化技术。它允许您将应用程序与它们完整的运行时环境打包和隔离，并且容器化的应用程序可以更容易地从一个环境移动到另一个环境，同时保留它们的所有功能。这种容器化的应用程序可以转换成 docker 映像，以便于共享。

除此之外，多阶段构建允许 Dockerfile 文件中有多个 from 语句，因为每个 FROM 语句后面都有创建中间映像的指令。最后或最终的 FROM 语句是最终的基图像。对于第一个基本映像，您可以从 0 开始复制中间阶段的工件，作为- COPY - from=。未被覆盖的人工制品被丢弃。

Docker 多阶段构建的优势:
只需要一个 Docker 文件来构建整个流程，不需要单独的 Docker 文件来使用卷映射协调“构建”和“运行”之间的操作系统转移。
为满足运行时需求，需要适当选择最终图像的基础图像，这有助于减小运行时图像的整体大小。
标准的 WildFly base 图像用于在每次发布新标签时更新图像，而不是手动下载和配置发行版。

在你走之前…

在本文中，我们总结了一个使用 [Java 开发服务](https://www.tatvasoft.co.uk/technology/java-development.php)进行 Docker 多阶段构建的快速指南。它允许通过简单地使用一个 Docker 文件来生成一个产品级的 Docker 映像，并且还让您知道如何启动一个短暂的 Postgres 容器。最后，您需要使用 Makefile 所需的所有参数来调用 docker build 命令。你可以在 GitHub 上轻松获得任何应用的 Dockerfile 和 Makefile。编码快乐！