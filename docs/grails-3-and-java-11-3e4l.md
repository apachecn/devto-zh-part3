# Grails 3 和 Java 11+？

> 原文：<https://dev.to/erichelgeson/grails-3-and-java-11-3e4l>

> 整理 Oracles 新 JDK 发布时间表和 Grails 3。
> *原贴于【https://grails3book.com/】T2*

甲骨文宣布他们将大幅加快 JDK 版本的发布速度——每 6 个月发布一次,没有[重叠的公众支持](https://jaxenter.com/end-life-comes-early-jdk-8-140824.html)。

Java 9 是这个加速发布周期中的第一个。虽然这些 JDK 的发布来得快，但结束得也同样快。当你读这篇文章的时候，JDK 9 和 10 已经停产了。JDK 11 是 JDK 的下一个长期支持(LTS)版本。

有了这些消息，人们对于 Java 版本和 Grails 应该在何时以及如何计划什么将支持什么还有些困惑。我将提供一些事实(和我的一些观点)来说明这对作为 Grails 开发人员的您意味着什么。

先回答几个具体问题。

# Grails 3 会在 JDK 9+上构建吗？

Grails 3.3.x 基于 Spring-Boot 1.5。Spring-Boot 1.5 不支持也不会支持 JDK 9+版本的构建。

[来自 Spring-boot 的维基](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-with-Java-9#requirements)

> Spring Boot **2** 是第一个支持 Java 9 的版本(Java 8 也支持)。如果你正在使用 1.5 并且希望使用 Java 9，你应该升级到 2.0，因为**我们没有计划在 Spring Boot 1.5.x** 上支持 Java 9。

这是*明确的*——除了 JDK7/8，你不会在任何地方构建 Grails 3 应用程序(没关系，继续阅读！)

# Grails 3 会在 JDK9+上运行吗？

这是一个非常不同的问题

答案是肯定的——Groovy 运行(带有一些可以禁用的警告)。您还需要将 [`jaxb`显式添加到您的 CLI 或您的依赖项](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-with-Java-9#jaxb)中——但它会运行。如果您使用其他第三方库，您将需要验证它们是否也能在 JDK9+上运行。

对于用户环境来说，这是一个好消息，在这种环境中，您无法控制自己的运行时，并且被迫运行在较新的 JDK 上。

# 如果我在 JDK8 上，会支持多久？

自 2019 年 1 月日[起，甲骨文已停止发布公开的 JDK8 更新。甲骨文](http://www.oracle.com/technetwork/java/eol-135779.html)[计划不重叠公开支持的二进制文件](https://jaxenter.com/end-life-comes-early-jdk-8-140824.html)不给开发者喘息的机会。

我的推荐是切换到众多 OpenJDK 8 ( [祖鲁](https://www.azul.com/downloads/zulu/)、 [amzn](https://aws.amazon.com/corretto/) 、 [adpt](https://adoptopenjdk.net) 、 [librca](https://bell-sw.com/pages/java-8u212) 等中的一个！)至少支持到 2020 年 10 月以上的发行版，或向 Oracle 支付支持费用。如果你在任何 PaaS 上运行，比如 AWS Elastic Beanstalk，你已经在 OpenJDK 上运行了。 [sdkman](http://sdkman.io/) 也让在您的工作站上设置 OpenJDK 变得很容易。)

# 什么时候可以用 Java 11 搭配 Grails？

spring-Boot 2.1[发布(2018 年 10 月 30 日。)](https://spring.io/blog/2018/10/30/spring-boot-2-1-0)Grails 团队已经在基于它开发 Grails 4 了。今年(2019 年)第二季度为定向发布。

# 现在谈几点我的看法。

Grails 构建在许多项目之上，如 Spring-Boot、Hibernate、Gradle 等。所有这些项目都需要在 Grails 能够支持最新的 JDK 之前支持它。这个 IMO 是个好东西——这意味着在我开始使用它之前，许多兼容性问题和错误修复都可以解决。

## 现在我们该怎么办？

*   Spring-Boot 1.5 将一直保持到 2019 年 8 月 1 日开发完成，Grails 3 将继续使用它。

*   立即将您的应用升级到 Grails 3.3.10，使升级到 4.0.0 更加简单。参见我的 Grails 4 升级笔记

*   如果您使用 Oracle JDK 8，请切换到 OpenJDK 8 获取安全更新，或者向 Oracle 支付扩展支持费用。

*   继续在 JDK 8 上构建，如果需要的话，在 JDK 11 上运行应该没问题。

*   坚持 LTS JDK 版本，如 8 月和 11 日。

*   不要采用短期支持(STS)或生命周期结束 JDK，如 9/10/13/等

# 结论

我使用 Grails，所以我不必担心这些兼容性问题——我得到了一个很好的包，里面的东西都是最新的，可以一起工作。我很乐意让这种 JDK 式的疯狂在我被卷入之前烟消云散——我有一些功能要提供给用户，而不是担心只支持 6 个月的 JDK。