# 创建和配置 Azure Web App 和 MySQL 来托管 Spring Boot 应用程序(2/7)

> 原文：<https://dev.to/azure/creating-and-configuring-azure-web-app-and-mysql-to-host-a-spring-boot-application-2-7-1n13>

这篇博客文章是“在 Azure 上部署 Spring Boot 和 Angular 应用程序”系列文章的一部分，下面是完整的文章列表:

*   [为 Azure (1/7)创建 Spring Boot 和角度应用程序](https://dev.to/jdubois/creating-a-spring-boot-and-angular-application-for-azure-1-7-2mb8)
*   创建和配置 Azure Web App 和 MySQL 来托管 Spring Boot 应用程序(2/7)
*   [使用 Azure Pipelines 构建、测试和部署 Spring Boot 和 Angular 应用程序(3/7)](https://dev.to/jdubois/using-azure-pipelines-to-build-test-and-deploy-a-spring-boot-and-angular-application-3-7-593j)
*   [与 Spring Boot 一起使用 Azure Application Insights(4/7)](https://dev.to/azure/using-azure-application-insights-with-spring-boot-4-7-263i)
*   [使用 Azure Application Insights 和 Angular (5/7)](https://dev.to/azure/using-azure-application-insights-with-angular-5-7-4kej)
*   [配置 Azure CDN 以提升角度性能(6/7)](https://dev.to/azure/configuring-azure-cdn-to-boost-angular-performance-6-7-5ebe)
*   [配置 Azure Redis 缓存以提升 Spring Boot 性能(7/7)](https://dev.to/azure/configuring-azure-redis-cache-to-boost-spring-boot-performance-7-7-52dl)

## 创建资源组

我们开始用 Azure 吧！前往 Azure 门户网站创建一个免费帐户(如果您目前没有)。我们在这里所做的应该不会花费太多，所以如果你只是测试一天，这应该只花费你几欧元，并且应该由你的免费积分支付。请注意，还有其他方式可以获得免费积分，例如，如果你是学生，你有 [GitHub 学生包](https://education.github.com/pack)，或者如果你的企业支付你一个 Visual Studio 企业帐户(这就是我们在这里将使用的)。

Azure 的一个伟大之处在于你可以创建“资源组”,在其中你可以添加和配置你的服务。这意味着我们在这里做的一切都将在一个特定的资源组中，我们将在测试结束时删除它:没有忘记运行资源并为它们付费的风险。

创建一个“spring-on-azure”资源组:

[![](img/963ac3a736be0c370a0bdbe688074b30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0krnXSuM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9kjv32v9lvk8kjv9xqod.png)

## 创建 Azure MySQL 数据库

在顶部的搜索框中，查找“MySQL”并选择“MySQL 的 Azure 数据库”。

使用我们之前配置的资源组创建一个 MySQL 数据库，不要忘记修改默认定价层！在这里，我们使用了最便宜的一个，大约每月 27 欧元(因此，如果您运行一整天，大约 1 欧元，这应该足够做这个演示)。

请记下您的数据库名称、用户名和密码，因为我们将在本文后面用到它们。

[![](img/b23640b89519c61e70f9f60d1fdc1775.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9-ot9BHG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xddq8nt4rzvka6xtmrec.png)

等待您的数据库被创建(您可以跳过并提前阅读一点！)，但我们还没有完成数据库设置:默认情况下，Azure 上的数据库受防火墙保护。虽然安全性非常重要，但是我们的应用程序和开发机器都可以访问我们的数据库也很有用，所以我们需要配置它。

在你的 MySQL 数据库中，进入“连接安全”，选择“允许访问 Azure 服务”。您还应该单击“添加客户端 IP”按钮，自动将您当前的 IP 添加到防火墙规则中。

[![](img/8d4367ab459f73d0d121b5d01b264c00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_sDThvc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/owjdl1y7ggoqrvae1keh.png)

现在 MySQL 服务器正在运行，您需要创建一个模式。在这个例子中，我们将使用 MySQL Workbench，但是您当然可以使用任何其他数据库工具。

使用我们刚刚设置的凭据连接到您的数据库。请注意，您的用户名的格式是“用户名@数据库”，并且您应该已经配置了 MySQL 防火墙来允许您的 IP 地址(参见上面的两段)。

[![](img/24d7b4b566c8d78a2acb9ee8e4c0f19e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MPn1xPv5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/17ih7e8z6ghx9of1futu.png)

现在您已经连接到了您的数据库，请创建我们将使用的“bugtracker”模式。因为这里我们使用 JHipster，所以我们只需要创建一个空模式(JHipster 使用 [Liquibase](http://www.liquibase.org/) 来自动更新数据库模式)，但是如果您没有使用 JHipster 或 Liquibase，这就是您可以运行 SQL 更新脚本的地方。

## 创建 Azure Web 应用

有几种方法可以在 Azure 上托管 Spring Boot 应用程序，最常见的方法是使用虚拟机、使用 Azure Web 应用程序或使用 Azure Kubernetes 服务。在这里，我们将使用 Azure Web Apps，因为它提供了一个完全托管的操作系统和 JDK(不像虚拟机，你必须自己设置和维护)，而且它非常容易使用，非常划算(与 Azure Kubernetes 服务相比)。请注意，Azure Web Apps 使用 Zulu OpenJDK，所以你有一个完全支持的、符合 TCK 的 JDK，即使是在旧的 JDK 版本上。

在 [Azure 门户](https://portal.azure.com/?WT.mc_id=devto-blog-judubois)中，搜索“应用服务”并创建一个新应用。请注意:

*   我们使用 Java 11，因为 Spring Boot 应用程序只是可执行的 Jar 文件(正如我的朋友 Josh Long 所说的“制造 Jar，而不是战争”)。
*   我们将默认的机器类型改为使用“B1 ”,它非常便宜，但功能强大，足以满足我们的需求。

关于定价的一句话，因为这里我们试图节省预算:在撰写本文时，B1 实例在第一个月是免费的，然后每月花费大约 30 美元。这对于我们的演示来说已经足够了！

[![](img/373a60c1cee8c8a340a8a87b97befc7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NKYrnm0N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8abgvjt1sy44d3vf29vq.png)

在创建您的 Web 应用程序之前，在线向导将建议一个特定的选项卡来添加监控:当然，您需要选择它来为您的应用程序创建一个新的“应用程序洞察”实例。我们将在本博客系列的以下部分对此进行配置:同样，这对于小型到中型的应用程序都是免费的，监控的好处是巨大的！

[![](img/fd5bdf13a04ab0eebbfe691c938facd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--21fteMMe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/54yc6u1l2ytsaz6n5ndv.png)

现在完成创建您的应用程序，并继续阅读将您的实例被供应！

## 配置 Azure Web App

一旦你的 Azure Web App 实例准备好了，你就需要配置它。选择您的 Web 应用程序并单击“配置”菜单，以修改应用程序设置。这些将作为环境变量公开，Spring Boot 将在启动时使用。

*   您应该已经有了一个`APPINSIGHTS_INSTRUMENTATIONKEY`键，我们稍后将使用它来配置 Azure Application Insights
*   对于您的数据库连接，添加`SPRING_DATASOURCE_URL`，它应该指向我们上面配置的 MySQL 实例。对于 JHipster 应用程序，该值应该类似于`jdbc:mysql://spring-on-azure-database.mysql.database.azure.com:3306/bugtracker?useUnicode=true&characterEncoding=utf8& useSSL=true&useLegacyDatetimeCode=false&serverTimezone=UTC`。当然，您需要用您的数据库名替换`spring-on-azure-database`。
*   以`username@database`的形式用您的数据库用户名设置一个`SPRING_DATASOURCE_USERNAME`变量。因此，如果你在一个名为`spring-on-azure-database`的数据库中创建了一个名为`juliendubois`的用户名，那么你需要在这里写上`juliendubois@spring-on-azure-database`。
*   用您的数据库密码设置一个`SPRING_DATASOURCE_PASSWORD`变量。
*   如果您想查看应用程序的调试消息，您可以配置`LOGGING_LEVEL_YOUR_PACKAGE_NAME`键，用应用程序中使用的包名替换`YOUR_PACKAGE_NAME`。在我们的示例应用程序中，应该是`LOGGING_LEVEL_IO_GITHUB_JDUBOIS_BUGTRACKER`(因为包名是`io.github.jdubois.bugtracker`)，我们将其设置为`DEBUG`。
*   如果您正在使用 JHipster，我们还建议您创建一个`JHIPSTER_SECURITY_AUTHENTICATION_JWT_BASE64_SECRET`密钥，并为它分配一个至少 256 位长的 base64 编码的令牌。这将是您的生产安全密钥，不应共享。

[![](img/ac2e4f77a11ed8514392db9ddd7e2e8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--69DberRu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qfilckir1onn2ndub5y.png)

你的 Azure 基础设施现在应该准备好了！在本博客系列的下一篇文章中，我们将使用 Azure Pipelines 来自动部署应用程序。