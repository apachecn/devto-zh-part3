# KVision 简介

> 原文：<https://dev.to/rjaros/an-introduction-to-kvision-39i1>

[KVision](https://github.com/rjaros/kvision) 是为 Kotlin/JS 打造的开源 web 框架。它允许开发者用[科特林](https://kotlinlang.org/)语言构建现代网络应用。

这是一个相对较新的项目(一年前发布了第一个版本)，但它可能是针对前端的最全面、功能最全的 Kotlin 框架。

当然，你们中的许多人想知道为什么有人喜欢在 Kotlin 中使用 frontend，因为有这么多优秀的 JavaScript 或 TypeScript 工具和框架。您可以阅读:

[![cassiozen image](img/573c18e0523f8d56b755b63891f701f1.png)](/cassiozen) [## 用于 JS 开发人员的 Kotlin(第 1 部分)

### 卡西乌斯·苏萨 2 月 25 日 19 日 9 分钟阅读

#javascript #kotlin #android](/cassiozen/kotlin-for-js-devs-part-1-5bld)article by Cássio Souza to find a lot of different reasons. I fully agree with them all.

## 我为什么要创建一个新的框架？

我怀念用对象构建用户界面的时代。不是来自文件、模板或表单，而是具有可重用和可扩展组件的层次结构。自从第一个 HTML 页面创建以来，我们在 web 开发中就没有这样做过，尽管外面的世界已经这样做了很多年了。 [Turbo Vision](https://en.wikipedia.org/wiki/Turbo_Vision) ， [OWL](https://en.wikipedia.org/wiki/Object_Windows_Library) ， [VCL](https://en.wikipedia.org/wiki/Visual_Component_Library) ， [MFC](https://en.wikipedia.org/wiki/Microsoft_Foundation_Class_Library) ， [WinForms](https://en.wikipedia.org/wiki/Windows_Forms) ， [QT](https://en.wikipedia.org/wiki/Qt_(software)) ， [AWT](https://en.wikipedia.org/wiki/Abstract_Window_Toolkit) ， [Swing](https://en.wikipedia.org/wiki/Swing_(Java)) ， [JavaFX](https://en.wikipedia.org/wiki/JavaFX) 甚至是刚出生的[Flutter](https://en.wikipedia.org/wiki/Flutter_(software))——这些只是 OOP UI 发展史上的几个例子。KVision 遵循这条道路——它是为完全面向对象的编程语言创建的面向对象框架。

## 值得注意的特征

KVision 能提供什么？

*   100%类型安全和完全编译的工作环境。
*   类型安全 DSL 生成器。
*   基于[引导](https://getbootstrap.com/)样式、排版和组件。
*   利用 [Snabbdom](https://github.com/snabbdom/snabbdom) 快速虚拟 dom 实现。
*   集成了来自[字体 awesome](https://fontawesome.com/) 、 [Bootstrap Select](https://github.com/silviomoreto/bootstrap-select) (带有 [AJAX](https://github.com/truckingsim/Ajax-Bootstrap-Select) 扩展)、 [Awesome Bootstrap Checkbox](https://github.com/flatlogic/awesome-bootstrap-checkbox) 、 [Trix 编辑器](https://trix-editor.org/)、 [Bootstrap 日期选择器](https://github.com/smalot/bootstrap-datetimepicker)、 [Bootstrap touchspin](https://github.com/istvan-ujjmeszaros/bootstrap-touchspin) 、 [Bootstrap 文件输入](http://plugins.krajee.com/file-input)、[手柄](http://handlebarsjs.com/)、 [Chart.js](https://www.chartjs.org/) 和 [Navigo](https://github.com/krasimir/navigo) 的库和组件。
*   包括复杂的布局容器，包括 CSS flexbox、CSS grid 和 Bootstrap responsive 12 列网格。
*   包括方便的表单实现，支持许多不同的输入组件和易于使用的验证。
*   对[可观察的](https://github.com/rjaros/kotlin-observable-js)数据模型的数据绑定支持。
*   基于 [Jed](http://messageformat.github.io/Jed/) 库和 [gettext](https://www.gnu.org/software/gettext/) 翻译的国际化支持。
*   易于使用的拖放。
*   类型安全的 REST 连接。
*   服务器端 [Ktor](https://ktor.io) 、 [Jooby](https://jooby.org) 和 [Spring Boot](https://spring.io/projects/spring-boot) 框架的创新集成接口。
*   支持使用[electronic](https://electronjs.org)构建跨平台的桌面应用程序。

## 一种语言统治一切

您只能用 Kotlin 语言在 KVision 中编写应用程序。不需要 CSS，HTML 或者 JavaScript。这种模式越来越受欢迎。你可以在 Scala、Dart 或 Go 语言社区中找到类似的努力。但在我看来，你不会发现任何东西像 KVision 这样对开发者友好和一致。和科特林一起工作真的很快，很有效率，也很有趣！

## 你好世界！

没有“你好，世界！”的介绍会是什么呢举例。所以现在你有了。-)

```
div {
    +"Hello World!"
} 
```

Enter fullscreen mode Exit fullscreen mode

当然只是一个片段。您将在 [KVision GitHub 页面](https://github.com/rjaros/kvision)和 [kvision-examples](https://github.com/rjaros/kvision-examples) 资源库中找到更多示例。还可以看看综合 [KVision 指南](https://kvision.gitbook.io/kvision-guide/)。

如果你喜欢这个想法-只是玩代码，叉项目，填补一个问题或创建一个拉请求。我计划在不久的将来为 Dev.to 写更多关于 KVision 的文章！