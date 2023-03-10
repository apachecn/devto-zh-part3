# 角度元素介绍

> 原文：<https://dev.to/angular/introduction-to-angular-elements-16i7>

你还没有进入角元素？那么是时候试一试了！在这篇文章中，我们将看到什么是真正的角度元素，为什么他们是有用的，以及如何创建我们的第一个平面组件的角度元素。

## 理论家课程:角元素入门

抬起头来！我在 [Egghead.io](https://egghead.io/courses/getting-started-with-angular-elements) 上创建了一个 40 分钟的课程，一步一步地向你介绍角元素。

以下是课程的介绍视频:[https://egghead . io/lessons/angular-course-intro-to-angular-elements](https://egghead.io/lessons/angular-course-intro-to-angular-elements)

这门课程会带你通过..

*   [将角度组件转换成本地自定义元素](https://egghead.io/lessons/angular-transform-an-angular-component-into-a-native-custom-element)
*   [将角度组件编译为独立的自定义元素](https://egghead.io/lessons/angular-compile-an-angular-component-as-standalone-custom-elements)
*   [使用 ngx-build-plus 编译角度元素](https://egghead.io/lessons/angular-use-ngx-build-plus-to-compile-angular-elements)
*   [使用输入和事件与角度元件通信](https://egghead.io/lessons/angular-communicate-with-angular-elements-using-inputs-and-events)
*   [在带有角度元素的 React 应用程序中使用角度组件](https://egghead.io/lessons/react-use-an-angular-component-inside-a-react-app-with-angular-elements)
*   [将数据从反应组件传递到带有角度元素的角度组件](https://egghead.io/lessons/react-pass-data-from-a-react-component-to-an-angular-component-with-angular-elements)
*   [用角度元素在角度中创建多个根组件](https://egghead.io/lessons/angular-create-multiple-root-components-in-angular-with-angular-elements)
*   [在带有角度元素的角度应用中使用角度组件](https://egghead.io/lessons/angular-use-angular-components-in-angularjs-applications-with-angular-elements)

**查看全程[这里](https://egghead.io/courses/getting-started-with-angular-elements)T3】。**

## 什么是角元素？

Angular Elements 是 Angular 生态系统中的新成员。Elements 允许您自动将每个角度组件转换(或包装)为“自定义元素”。定制元素是 Web 组件的总称之一。这为棱角状的物体打开了无数新的可能性

*   **动态实例化组件-** ，但不使用 ComponentFactoryWhatever，而是以从服务器发送的 HTML 字符串的形式，例如， *CMS 系统*的典型用例。它还允许我们使用浏览器自带的`document.createElement('...')` API 实例化一个组件。
*   **将角度组件编译为自定义元素**，在角度应用程序“外部”使用。假设您构建了一些想要在您的组织中重用的很酷的小部件。但不是每个人都用 Angular，甚至都有 SPA。在这种情况下，您仍然可以将角度组件(作为角度元素)编译成一个 JS 文件，并在需要时使用它
*   **升级你的 AngularJS 应用-** 有[不同的策略可用](https://github.com/angular/ngMigration-Forum/wiki/Migration-Paths-Overview)。角度元素是升级遗留 AngularJS 应用程序的新选项，只需为某些页面/组件嵌入角度元素。

## 为什么？

为什么我们需要角元素？有很多原因和用例:

*   实现更动态的角度应用(即 CMS 系统)
*   在非角度应用中嵌入角度组件(例如，在大型组织的团队之间共享)
*   增强现有的 HTML 页面(不是一切都是&应该创建为单页应用程序)
*   从 AngularJS 升级到 Angular(即通过在 AngularJS 应用程序中嵌入角度元素)
*   微前端
*   ...

## 创建你的第一个角度元素

前往我的博客[阅读全文](https://juristr.com/blog/2019/04/intro-to-angular-elements/?utm_source=devto&utm_medium=crosspost)