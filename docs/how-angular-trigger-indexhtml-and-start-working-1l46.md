# 角度如何触发 index.html 并开始工作

> 原文：<https://dev.to/casperns/how-angular-trigger-indexhtml-and-start-working-1l46>

[![Angular start - start](img/80055d9739c215eea9b7b517d0d466ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1G8B4vt_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1lw7vq4qxrwbfa0hdqv.JPG)

当我们使用 CLI 工具时，我们可以轻松地创建和构建 angular 应用程序，并在 CLI 上执行几个命令后开始开发。

但是棱角如何开始工作，又如何触发 index.html 呢？

这通常是角度开始工作的方式。

* * *

**main . ts**
T3![Angular main.ts - main.ts](img/0addb5849164e4a00a3464549fc5eef5.png)T5】

**main.ts** 文件，这是第一个被执行的代码。 **main.ts** 的工作是引导应用程序。它加载所有内容并控制应用程序的启动。

最重要的是，这是引导程序通过将应用程序模块传递给方法来启动 angular 应用程序的那一行。AppModule 是指 **app.module.ts** 文件。

* * *

**app . module . ts**T2[T4](https://res.cloudinary.com/practicaldev/image/fetch/s--qV8Dm7TB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f70dczsmfvitierapzj7.JPG)

当 angular 启动时，文件中最重要的部分是@NgModule 中的引导数组。

它基本上有一个所有组件的列表，这些组件在 Angular 分析我们的**index.html**文件时应该是已知的，这个循环在这里结束。

它是封闭的，因为在这里，我们引用我们的应用程序组件。

> 默认情况下，angular 不会扫描我们所有的文件。所以，如果我们不告诉它新组件的存在，angular 就不知道。

 **[![Angular app.component.ts - app.component.ts](img/e81433d8774d290b7f6afa49ea1d9cf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ACDqlRx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lnsz7vwgveeg35k1idj4.JPG)**

 *** * *

**总结**

*   Angular 从 main.ts 开始。
*   然后，我们启动一个 angular 应用程序，并将 app.module.ts 作为参数传递。在 app.module.ts 中我们告诉 angular: *“有一个 app 组件是你自己尝试启动时应该知道的”。*
*   angular 现在分析这个应用程序组件，读取我们经过的设置，那里有*选择器 app-root* 。
*   现在，angular 可以在 index.html 中处理 *app-root* ，并且知道*选择器*的规则。
*   *选择器*应该插入应用程序组件，并有一些 HTML 代码-一个附加到他的模板- html 组件。
*   这就是角度应用的开始。**