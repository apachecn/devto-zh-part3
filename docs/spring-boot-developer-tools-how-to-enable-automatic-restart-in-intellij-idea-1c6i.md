# Spring Boot 开发者工具:如何在 IntelliJ IDEA 中启用自动重启

> 原文：<https://dev.to/suin/spring-boot-developer-tools-how-to-enable-automatic-restart-in-intellij-idea-1c6i>

这篇博文描述了我们如何使用 Spring Boot 的开发者工具自动重启我们的应用程序。完成这篇博文后，我们:

*   了解开发人员工具有哪些特性，以及自动重启如何支持我们的应用程序开发。
*   可以集成 IntelliJ IDEA 和开发工具中的自动重启功能。

让我们开始吧。

## 自动重启

作为 Spring Boot 框架的一个模块提供的开发者工具是一个可以使应用程序开发体验更好的模块。

开发人员工具由五个功能组成:

1.  属性默认值
2.  自动重启
3.  肝负荷
4.  全局设置
5.  远程应用程序

在上述特性中，对我们的开发过程速度影响更大的是第二个特性——自动重启。每当类路径上的文件发生更改时，使用此选项的应用程序都会自动重新启动，方法是监视文件夹中文件的修改，如。/out 或者。/build。

开发者工具的安装非常简单；将下面的一行代码添加到 build.gradle 的`dependencies`指令中。

```
dependencies {
    ....
    runtimeOnly 'org.springframework.boot:spring-boot-devtools'
        ...
} 
```

## 集成 IntelliJ 和开发者工具

然而，仅仅通过添加上面的一行程序，开发者工具还不能自动重启我们的应用程序。我们需要另外完成两个步骤。

首先，我们必须启用自动构建项目。打开偏好设置.../设置...->构建、执行、部署->编译器并允许自动构建项目。

[![](img/3276b8793b5f3cb32d71ea41a12739ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZPjLABOz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y88li2t3kevif1mqjmyf.png)

其次，我们更改注册表配置。按 command+shift+A(如果您使用的是 Windows，请按 Ctrl+Shift+A)，然后搜索注册表。

[![](img/2e564c82d9da1ff6a422408ae1101c98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JngEWOOx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y2d09z4neqdtrlbjzfy8.png)

在注册表中，滚动列表找到以下配置并启用它:

```
compiler.automake.allow.when.app.running 
```

[![](img/be61c379c4732f895d5b41c13cf6d6af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AEuREtHJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/443dy5jgizopmgm09qc1.png)

通过完成上面的两个步骤，我们现在能够开发我们的应用程序，而不需要手动重启。

最后，必须注意的是如何启动应用程序。必须通过运行`main`功能而不是 Gradle 任务`bootRun`来启动应用程序。

[![](img/5472a3749c1d8a1a75281af2048d55ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wf1k08_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nwo8i3bqp43ig06l6toz.png)

## 总结

这篇博文教会了我们两件事:

*   我们了解了开发人员工具的特性，以及自动重启如何支持我们的应用程序开发。
*   我们从开发工具中学习了如何集成 IntelliJ IDEA 和自动重启特性。