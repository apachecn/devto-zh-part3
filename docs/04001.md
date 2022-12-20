# 使用 Vaadin 框架构建 PWA 应用程序

> 原文：<https://dev.to/dmarko484/building-pwa-application-using-vaadin-framework-15ib>

这是即将到来的教程系列的第一个帖子，应该作为我个人对使用 [Vaadin](https://vaadin.com/) 框架构建 PWA 应用程序的思考。我使用 Vaadin 已经 5 年了，从它的第 7 版开始。我目前的系统是使用 Vaadin 8 构建的，因为对于复杂的代码库来说，迁移到当前版本(Vaadin 13 现在可用)不是很简单。我有几个 CRM 和 DMS 之类的系统，在生产中使用 Vaadin 8 构建并运行，非常成功。它非常稳定的环境，包含非常直观的 API，几乎不使用 JavaScript 就可以创建非常复杂的 web 应用程序。最终的应用程序代码库非常全面，并且只存在于 Java 中。最适合全栈开发者。

我的开发环境主要基于 [Spring Boot](https://spring.io/projects/spring-boot) 及其优秀的 Vaddin 集成。这让我可以使用 Spring Boot 周边的大型工具基础设施。

我的主要想法是使用最新的 Vaadin 13 版本创建 PWA 应用程序，该应用程序可用作我运行的其他现有 CRM / DMS 系统的副本。这将涵盖我必须以某种方式处理的几个主题。这个系列是记录这个过程的一种方式。

**构建应用程序的步骤**(可能会更改)

*   [准备堆栈——我们实际上要用什么？](https://dev.to/dmarko484/building-pwa-application-using-vaadin-framework-our-stack-and-useful-links-1nkl)
*   创建应用程序框架
*   创建基本的应用程序 UI 结构
*   首次上线 SSL 背后的生活
*   使用应用程序布局插件显示数据
*   创建模拟外部系统的虚拟后端
*   添加身份验证
*   离线
*   ...下一个来

本系列中使用的带有代码库的 GitLab 存储库[位于此处](https://gitlab.com/dmarko484/pwa-application-with-vaadin)。