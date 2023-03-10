# 独立部署 EJB 服务器应用程序和客户端

> 原文：<https://dev.to/mhddurrah/deployment-of-ejb-server-application-and-client-independently-3eaf>

一个朋友让我在 EJB 和 EJB 的客户项目上工作，每个项目都有一个独立的部署包。

我很久以前就习惯用 Netbeans 来做这件事，但是我决定用 maven 来做。我发现 Apache 为 maven 提供了 EAR 和 EJB 应用程序的插件:
[【https://maven.apache.org/plugins/maven-ear-plugin/】](https://maven.apache.org/plugins/maven-ear-plugin/)用于将工件部署为 Java EE Enterprise Archive(EAR)
生成 Java Enterprise JavaBean (EJB)文件以及相关的客户端 jar。虽然文档不够清晰，但这几乎是完美的。然后我寻找如何使用它的人，幸运的是，我在 github 上找到了一个例子，它在某种程度上是好的，但没有很好地组织起来，我在 maven 上有构建问题。

通过努力重组 pom 结构并修复一些配置，我能够将 EJB 应用程序构建为一个独立的 EAR 文件(由于对 Hibernate 的依赖性，它应该是 EAR 而不是 JAR)。该插件还为您生成 EJB jar 和 EJB 客户端 jar，使用 maven，您可以轻松地将其添加为客户端应用程序的依赖项。

用 JSF、primefaces 和第一步中创建的 EJB 客户端 jar 构建 EJB 客户端应用程序非常简单。然后我使用 Glassfish 服务器来部署这两个工件。

这只是构建独立的企业 Java 应用程序的一个简单尝试，但是对于其他需求，事情可能会变得复杂。

您可以查看 github 的源代码和构建/部署说明
[【https://github.com/mhddurrah/ejb-application】](https://github.com/mhddurrah/ejb-application)
[【https://github.com/mhddurrah/ejb-jsf-client】](https://github.com/mhddurrah/ejb-jsf-client)
，如果有任何更新或其他问题/疑问，请随时联系我。