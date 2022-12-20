# 一些教程的基本设置

> 原文：<https://dev.to/thegroo/basic-setup-for-some-tutorials-51m>

正如我在[上一篇文章](https://dev.to/thegroo/back-to-writing-304a)中解释的那样，现在我又开始写作了，我将从所需的基本设置开始，以便能够运行我将在不久的将来在这里分享的代码。

我最初的帖子很可能是关于 Apache Kafka 的开发，与 T2 Spring Boot 的应用程序集成，因为这是我过去几年来主要工作的堆栈。

Apache Kafka 是一个开源的流处理软件平台，由 LinkedIn 开发并捐赠给 Apache Software Foundation，通过其最新的改进，特别是对于流，它现在是一个非常合适的替代方案，可以直接运行与微服务应用程序位于同一位置的流管道，这是可能的，因为 Kafka-streams 是作为一个简单的库提供的。这也意味着数据离您的应用非常近，并且可供您的应用使用，从而减少了延迟，并为增强您的服务和为您的应用增加业务价值开辟了许多可能性。我们将在以后的文章中详细介绍，现在，让我们开始设置。这是我们需要的:

*   如果你不熟悉 Git，请查看一下[Git 是什么？对于快速有用的命令，我想推荐](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F) [git 简单指南](http://rogerdudler.github.io/git-guide/)，当然还有很棒的[官方文档和免费在线书籍](https://git-scm.com/doc)。
*   Java 8 到最新的 Java。Java 8 将会存在很长一段时间，所以你可以考虑使用它。如果你正在学习 java 并计划加入工作队伍，我建议你现在就开始学习 Java 8。目前，有许多发行版，它们都应该可以工作，我目前在 Ubuntu Linux 中运行 OpenJDK 11，但还有更多选项，一些:

    *   [打开 JDK](https://openjdk.java.net/install/)
    *   [亚马逊的 Corretto](https://aws.amazon.com/corretto/)
    *   [IBM JDK 公司](https://developer.ibm.com/javasdk/downloads/)
    *   [开放式 JDK 的枢纽分布](https://docs.pivotal.io/pivotal-distribution-openjdk/index.html)
    *   [甲骨文 JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)——如[本常见问题解答](https://www.oracle.com/technetwork/java/javase/overview/oracle-jdk-faqs.html)所述，非个人使用需要许可证。
*   [Docker](https://docs.docker.com/install/) -运行本地开发堆栈并与其他开发人员共享它确实很有帮助。

*   Docker Compose -帮助为本地开发创建完全本地的、易于管理的基础设施。

*   Apache Maven - Maven 是一个固执己见的软件项目管理工具，它使用许多可用的插件来帮助管理项目依赖性和更多。

*   运行 Java 项目的首选 IDE。

## 一些额外的支持在你的终端上

我真的喜欢给我喜欢的终端添加一些额外的工具支持，在 Linux 上是 Gnome Terminator，在 MAC 上是 iTerm，我最喜欢的是["哦，我的 zsh"](https://ohmyz.sh/) ，它的安装说明和功能在[他们的文档](https://github.com/robbyrussell/oh-my-zsh/wiki)中有更好的描述，你需要[先安装 zsh](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH) ，并配置你的终端默认使用 zsh 或者切换到它。

哦，我的 zsh 有一些很好的现有插件，用于不同的命令别名，你可以很容易地配置你自己的别名和完整的 javascript 函数，并在你的终端上执行复杂的指令，这对于在我们的本地开发机器上实现自动化是非常方便的。

## 想学 Java

如果你是 java 的新手，你可以找到很多免费的在线资料，所以这完全取决于你:

*   [Java 书籍](https://www.java67.com/2013/11/10-free-java-programing-books-download-PDF-HTML.html)
    *   布鲁斯·埃凯尔的《用 Java 思考》
*   [免费 Udemy Java 教程](https://www.udemy.com/java-tutorial/)
*   Java 教程仍然有用，它曾经是一个很好的参考，但是自从...Oracle 发生了，它只是不再被照顾，所以它是 Java 8，很可能不会更新。

## IDEs

作为一名长期的 Java 开发人员，我使用过许多不同的 ide，文本编辑器，Visual Age for Java，Visual Cafe，JBuilder，Together，Eclipse，最后，在过去的 4 年里，我一直在使用 IntelliJ Ultimate，也尝试过 VSCode with java，但我缺乏快捷方式和机械记忆，现在对我来说是一个障碍。

我从这些工具中学到了什么？事实证明，最好的 IDE 是您更熟悉的，并且知道加快您的工艺的快捷方式和技巧，所以选择您喜欢的任何一种，并确保一直学习它，总有其他东西要学习，可以改善您的开发体验，无论您选择哪种工具，这自然不限于您的 IDE。

作为一名开发人员，每天都会学到一点东西。太棒了。