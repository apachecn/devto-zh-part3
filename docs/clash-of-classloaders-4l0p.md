# 类装入器的冲突

> 原文：<https://dev.to/tech_sam/clash-of-classloaders-4l0p>

在代码世界中，有时情况就像你试图构建一个迪拜塔，但却卡在了第一层地下室，最近我遇到了类似的情况，我在 OAuth 项目中处理所有复杂的与安全相关的东西，并假设很快我将启用我的迪拜塔，但我却卡在了类加载器冲突之间。

在本文中，我将解释 spring boot 和 apache ignite 集成，以及 spring 如何覆盖默认的 java 类加载器。
你可以在 [GitHub](https://github.com/Earth-43/spring-boot-ignite)
上找到 spring-boot ignite 实现代码，我在 ignite 缓存中缓存了一个用户对象及其属性，比如角色，作为一个对象，所以在检索时，我得到了一个类似
的异常

`java.lang.IllegalStateException: Cached value is not of required type [cacheName=boot_ignite_cache, key=user_data, val=com.boot.ignite.bootignite.dto.User@1ae2cec9, requiredType=class com.boot.ignite.bootignite.dto.User]
at org.apache.ignite.cache.spring.SpringCache.get(SpringCache.java:77) ~[ignite-spring-2.7.0.jar:2.7.0]`

在花了大量的时间后，我意识到有一些严重的问题，我已经忘记了我的基础知识，并开始阅读关于类加载器，很快我就诊断出这个问题，spring 比我们想象的要友好得多。Spring 使用了一种叫做重启类加载器的东西，Spring Boot 提供的重启技术使用两个类加载器。

> 不变的类(例如，来自第三方 jar 的类)被加载到基类加载器中。您正在积极开发的类被加载到一个重启类加载器中。当应用程序重启时，重启类加载器被丢弃，并创建一个新的。这种方法意味着应用程序重启通常比“冷启动”快得多，因为基类加载器已经可用并被填充。

所以我的**用户对象**由 spring boot **重启类加载器**加载，ignite 对象由**默认类加载器**加载，这两个类是不同的。
解决方案要么不使用没人想要的 [spring dev tools](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-devtools.html) ，要么创建一个**spring-dev tools . properties**文件并排除这些类。

感谢阅读！！！继续成长，继续前进！！！