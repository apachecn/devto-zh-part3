# 预载所有角束

> 原文：<https://dev.to/angular/preload-all-angular-bundles-1b6l>

用户想要快速应用。在用户需要之前，尽可能快地将 JavaScript 包下载到浏览器中，可以对用户体验产生巨大的积极影响。知道如何改善这种体验很重要。

提高 Angular 应用程序用户体验的一个方法是战略性地决定预加载哪些包。您可以控制何时加载包以及加载哪些包。这就是为什么你应该探索选择一个内置的或创建自己的自定义角度预载策略。

在这个系列中，我们将探索预加载角束的几种选择。

以下是这一系列的文章

*   [预载所有角束](https://dev.to/john_papa/preload-all-angular-bundles-1b6l)
*   [选择预加载哪些角束](https://dev.to/john_papa/you-pick-which-angular-bundles-to-preload-5l9)
*   [当检测到良好的网络连接时，预载角束](https://dev.to/azure/preload-angular-bundles-when-good-network-connectivity-is-detected-j3a)
*   [您的角形管束的预测预加载策略](https://dev.to/john_papa/predictive-preloading-strategy-for-your-angular-bundles-4bgl)

## 预载全部

Angular 的默认预加载策略是不预加载。因此，如果你想预加载，你必须改变这种行为。

你可以通过使用内置预加载策略`PreloadAllModules`告诉 Angular 预先加载所有的惰性加载模块。简单地从`@angular/router`导入它，如下所示。

```
import { RouterModule, Routes, PreloadAllModules } from '@angular/router'; 
```

然后在设置您的`RouterModule`时，将包括`preloadingStrategy`在内的路由器选项传递给`forRoot()`功能。

```
@NgModule({
  imports: [
    RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })
  ],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

## 试试看

现在用`ng serve`重新构建并运行你的应用。打开您的浏览器，打开您的开发者工具，然后进入`http://localhost:4200`。当您在浏览器中查看 Network 选项卡时，您会看到所有的软件包都已经预加载。

现在，当您导航到您的惰性加载包时，您将看到它们已经被加载，因此体验很快。如果您真的希望看到这种改变，请在您的开发工具中启用对较慢连接(如 3G)的限制，然后刷新页面。预加载现在在网络选项卡中更加明显。

## 决定什么适合你的应用

现在你知道如何使用内置的`PreloadAllModules`策略，你如何评估这是否是你的应用的正确策略？嗯，这个看你的情况了。

如果你的应用程序有几个来自延迟加载的包，并且都很大，那么当你的应用程序加载时，这些包都会开始转移到你的浏览器中。你不能控制先加载哪一个，所以如果用户点击一条路线，它很可能不会是第一个预加载的。

如果你的用户很少去这些路线会怎么样？预先加载它们有意义吗？

如果你觉得用户可以从预先加载的路线中受益，这可能是一个不错的选择。

最终决定权在你。我建议在选择这个选项或任何预加载策略之前，在各种有效和常见的用户工作流下以各种网络速度进行测试。这些数据将帮助你决定这是否是你的正确策略，或者另一个策略是否对你的应用程序的用户更有益。

## 资源

*   获取 [VS 代码](https://code.visualstudio.com/?wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度要素](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular-essentials&wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度片段](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular2&wt.mc_id=devto-blog-jopapa)