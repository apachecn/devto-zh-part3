# Angular 8 有什么新功能

> 原文：<https://dev.to/sameer_zure/what-s-new-in-angular-8-3531>

终于，期待已久的 Angular 版本(由于 IVY buzz)发布了。Angular 8 包括 Ivy 和 Bazel 的预览。Angular 团队已经谈论 Ivy Renderer 一年多了，现在我们在版本 8 中至少有了同样的选择加入预览。为了测试 Ivy 或 Bazel，您需要手动激活它们。

除此之外，新版本的 framework 在包大小和运行速度方面做了很多改进。

这个版本中最重要的变化或特性是**差异加载**——不同的捆绑包适用于传统和现代浏览器。

其他变化包括延迟加载模块的路由配置中的动态导入、Web Worker 支持、对 Typescript 3.4.x 的支持

## 差动加载

差异加载是一个过程，通过该过程，浏览器根据其功能在传统(ES5)或现代(ES2015+) JavaScript 之间进行选择。因此 Angular 将创建一个遗留构建和一个现代构建，当应用程序被加载时，适当的包将被加载。
Angular 创建的包将使用 **nomodule** 属性注入。

```
<script nomodule src="…"> // Legacy
<script type="module" src="…"> // Modern 
```

nomodule 属性是一个布尔属性，它防止在支持模块脚本的浏览器中执行脚本，从而允许在现代浏览器中选择性地执行模块脚本，在传统浏览器中选择性地执行旧脚本。

## 懒加载修改-动态导入

Angular 的新版本改变了我们过去在路由器配置中延迟加载功能模块的方式。之前我们在路由器配置中做了如下操作:

```
{
    path: '/user',
    loadChildren: './user/user.module#UserModule'
} 
```

新语法:

```
{
    path: '/user',
    loadChildren: () => import(`./user/user.module`).then(mod => mod.UserModule)
} 
```

## 构建器 API

有许多运行复杂进程 CLI 命令。每个命令都使用一个名为 Architect 的内部工具来运行 CLI 构建器(最终使用另一个工具来完成任务)。在 Angular 8 中，CLI 构建器 API 可供开发人员通过添加或修改命令来自定义 Angular CLI。

## Web Worker 支持

Web Workers 允许您在后台线程中运行 CPU 密集型计算，释放主线程来更新用户界面。
我们可以在 CLI
中使用 **ng generate** 命令来生成 web worker

```
ng generate webWorker test-worker 
```

## 支持打字稿

Angular 8 将包括对其依赖项(如 RxJs & TypeScript)的最新版本的更新。Angular 现在使用的是 TypeScript 3.4。

这些是 Angular 的新增内容。虽然他们很好，但他们不是很关键。除此之外，重要的是 Ivy 的选择加入预览。所以有了 Angular 版本 8，你可以选择加入开始使用 Ivy 的预览版。随着差异加载的增加，您将获得显著的性能提升。

保持冷静&继续编码！