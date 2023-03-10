# 如何在 Visual Studio 中启用 docker 支持 ASP.NET 应用程序

> 原文：<https://dev.to/niranjankala/how-to-enable-docker-support-asp-net-applications-in-visual-studio-28p7>

# 简介

在本文中，您将了解如何在 Visual Studio 中为 ASP.NET 应用程序启用 docker 支持。我们将创建 ASP.NET 核心应用程序 docker 支持，并在现有应用程序中启用 docker 支持。

# 先决条件

*   Windows
*   Visual Studio 2017 或更高版本。NET 核心跨平台开发工作量
*   在新应用程序中启用 Docker 支持

创建 Visual Studio web 项目时，也可以在项目中获得 Docker 支持。NET 核心或完整的框架。如果你选择。NET 核心框架，您可以选择在新项目向导中添加 Docker 支持，但对于完整的框架，我们可以在以后的上下文菜单“解决方案资源管理器”中添加 Docker 支持。参见下面的步骤来创建一个支持 Linux 容器的. NET 核心项目:

[![Alt Text](img/dfa6192040beafe188b357ac9f411b7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zwCcVVqO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-3fktFlZe1ls/XEodRn7XtsI/AAAAAAAABro/752MAL86VKwwC4BhYjIbgQ8VjG-kikcsgCHMYCw/clip_image001_thumb1%3Fimgmax%3D800)

Visual Studio 中的 Docker 工具理解。NET 核心和。NET 完整框架，所以生成的文件将很好地反映这些不同的目标平台。

要添加 Docker 对完整框架的支持，请参阅前面的 post-to 容器化. NET 应用程序

# 在新应用中启用 Docker 支持

创建项目后，您可以通过在“解决方案资源管理器”中右键单击该项目，然后选择“添加”子菜单下的“Docker 支持”选项来添加 Docker 支持。

[![Enable docker support in visual studio](img/2c1f68240cd97af44565bf17b890bd6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uS4CxgNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-KioN8jDs-Bs/XEodUOHruII/AAAAAAAABrw/eKw06MrlthEd8dDn_sgouRfBM15SGCSZgCHMYCw/clip_image003_thumb6%3Fimgmax%3D800)

Visual Studio 将添加 DockerFile 和。对于将用于构建 docker 容器映像的项目，首先要引用基本映像 dotnet:2.2-aspnetcore-runtime。

[![Enable docker support in visual studio](img/277dcb33f82e465f6f6cff908dd71430.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w6SkCNZg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-Iscki3i_kmo/XEodWV8Es3I/AAAAAAAABr4/nFW2l9Gs5nk1GbWJYHF2XabGNm28Du02gCHMYCw/clip_image005_thumb2%3Fimgmax%3D800)

**注意:**要构建这个容器，你需要在你的机器上切换 Docker tools for Windows 来运行 Linux 容器。如果它的目标是不同的操作系统类型，那么您将在构建期间得到错误，因为您不能混合 Linux 容器和 Windows 容器。

Docker 支持还添加了生成的 YAML 文件。YAML 文件可以与 docker-compose 一起使用，对一组容器执行 docker 命令，而不是一次只对一个容器执行，以便多个容器可以在微服务场景中一起工作。

```
docker build -f "D:\DevWorkSpaces\GitHub\WebDevLearning\WebDev\WebDev.Containerized.MVCWeb\Dockerfile" -t webdevcontainerizedmvcweb:dev 
```

[![Docker file to build image](img/2a78a2826b40401840908040f751d187.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8o7Fqx8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-ApaPR9s2eCo/XEodZcO_EGI/AAAAAAAABsA/sWwh_p8c-FYfr8bEex-efG7fa-lOo7FegCHMYCw/clip_image007_thumb4%3Fimgmax%3D800)

# 从 CLI 构建 Docker 映像

在管理模式下打开命令提示符，并在项目文件夹中运行以下命令:

```
C:\Users\niranjansingh\Source\Repos\WebDevLearning\WebDev\WebDev.AspNETMVC>docker build . 
```

[![Build docker image for asp.net application](img/55dd653e6a93637de9e5335be7f72834.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2862LMQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-OjImm3L9gbw/XEojAc4R5YI/AAAAAAAABs4/UoMxABnoZ8E8LXvKog_0g26ZOqBsNcJkgCHMYCw/clip_image001_thumb%25255B10%25255D%3Fimgmax%3D800)

# 在 Docker 环境下运行应用

因为。NET 核心框架应用程序，只需选择运行箭头按钮后的 Docker 选项来运行应用程序。之后，应用程序将根据 Docker 文件中提供的设置构建和创建 Docker 映像。

[![clip_image009](img/c882638f9e3e2c9e7453ebaafd6cbbd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dDPQ5rq5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-k4_RQaLP1qE/XEodb5Ud6XI/AAAAAAAABsI/r_H1BubyMwk7j4eqb1kilZwitrqT60iWQCHMYCw/clip_image009_thumb3%3Fimgmax%3D800)

对于我的案例，应用程序的目标是“Linux ”,而我的系统上的 Docker for Windows 被配置为运行 Windows contains，因此它不会构建我的案例。因此，在构建应用程序之前，请记住切换特定的目标操作系统容器。

对于. NET framework 应用程序，将 docker-compose 作为启动项目。在此之后，修改。yml 文件来构建和运行包含。

[![image](img/50eadb00b9540dbb500e375ce8865c71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IUFUBOhr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-GfTEhGwRzOo/XEodeMCJYBI/AAAAAAAABsQ/_VU7OvEGu2EBs9p8lFqIhaVidQ3Hm0ijwCHMYCw/image_thumb11%3Fimgmax%3D800)

您将在中的“Docker”位置看到 Docker 编写按钮。NET 完整框架应用程序。

[![image](img/fa0014c846d1d3864c87a713874c3174.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1LoUxBVu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-jYQFOKHu0To/XEodgyZCXWI/AAAAAAAABsY/CNPqzWh4MDEpsor6hKW48XKt1h81lXTlwCHMYCw/image_thumb16%3Fimgmax%3D800)

点击 Debug 按钮，让 docker 分解，在 yml 文件配置的基础上构建并运行 docker 镜像。

这里，我们已经完成了在 Visual Studio 中为 ASP.NET 应用程序构建 docker 映像的基本步骤。