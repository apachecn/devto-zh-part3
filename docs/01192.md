# 您选择要预加载的角束

> 原文：<https://dev.to/angular/you-pick-which-angular-bundles-to-preload-5l9>

用户想要快速应用。在用户需要之前，尽可能快地将 JavaScript 包下载到浏览器中，可以对用户体验产生巨大的积极影响。知道如何改善这种体验很重要。

提高 Angular 应用程序用户体验的一个方法是战略性地决定预加载哪些包。您可以控制何时加载包以及加载哪些包。这就是为什么你应该探索选择一个内置的或创建自己的自定义角度预载策略。

在这个系列中，我们将探索预加载角束的几种选择。

以下是这一系列的文章

*   [预载所有角束](https://dev.to/john_papa/preload-all-angular-bundles-1b6l)
*   [选择预加载哪些角束](https://dev.to/john_papa/you-pick-which-angular-bundles-to-preload-5l9)
*   [当检测到良好的网络连接时，预载角束](https://dev.to/azure/preload-angular-bundles-when-good-network-connectivity-is-detected-j3a)
*   [您的角形管束的预测预加载策略](https://dev.to/john_papa/predictive-preloading-strategy-for-your-angular-bundles-4bgl)

## 选择加入

“选择加入”策略就像自助餐。您选择想要预加载的内容，剩下的内容根据需要进行加载。

创建定制的预加载策略是控制包预加载方式的一个好方法。定制的`OptInPreloadStrategy`预加载策略让您可以指定应该预加载哪些延迟加载的包。

你可以通过创建一个新的类来实现这个接口，并在根中提供它。然后，当你想告诉 Angular 预加载函数时，你必须实现`preload`函数并返回`load()`函数。

注意，`OptInPreloadStrategy`类中的`preload`函数检查包含`preload`属性的`data`属性的路由定义。如果`data.preload`属性存在并且是真值，那么`load()`函数将被返回。当`load()`函数返回时，路由器监听这个可观察到的消息，并调用网络来加载路由包。

```
import { Injectable } from '@angular/core';
import { PreloadingStrategy, Route } from '@angular/router';
import { Observable, EMPTY } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class OptInPreloadStrategy implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    return route.data && route.data['preload'] ? load() : EMPTY;
  }
} 
```

## 路线定义

该策略要求您指出哪些路线应该预加载。您可以通过添加`data.preload`属性并在路由定义中将它设置为`true`来实现这一点，如下所示。

```
export const routes: Routes = [
  { path: '', pathMatch: 'full', redirectTo: 'heroes' },
  {
    path: 'dashboard',
    loadChildren: () =>
      import('app/dashboard/dashboard.module').then(m => m.DashboardModule),
    data: { preload: true }
  },
  {
    path: 'heroes',
    loadChildren: () =>
      import('app/heroes/heroes.module').then(m => m.HeroesModule),
    data: { preload: true }
  },
  {
    path: 'villains',
    loadChildren: () =>
      import('app/villains/villains.module').then(m => m.VillainsModule)
  },
  { path: '**', pathMatch: 'full', component: PageNotFoundComponent }
]; 
```

请注意，仪表板和英雄路线都将`preload.data`属性设置为`true`。但是，恶棍路线没有这个属性设置。在这种情况下，英雄和仪表板将预加载，但恶棍只会在用户导航到这条路线时加载。

## 设置自定义 OptInPreloadStrategy

然后在设置您的`RouterModule`时，将包括`preloadingStrategy`在内的路由器选项传递给`forRoot()`功能。

```
@NgModule({
  imports: [
    RouterModule.forRoot(routes, { preloadingStrategy: OptInPreloadStrategy })
  ],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

## 试试看

在应用了这个策略并设置了一些路由定义的路由`data.preload = true`之后，使用`ng serve`重新构建并运行您的应用程序。打开你的浏览器，打开你的开发者工具，然后去`http://localhost:4200`。当您在浏览器中查看 Network 选项卡时，您会看到所有的软件包都已经预加载。

请注意，仪表板和英雄已经预装，但恶棍没有。如果您真的希望看到这种改变，请在您的开发工具中启用对较慢连接(如 3G)的限制，然后刷新页面。预加载现在在网络选项卡中更加明显。

## 决定什么适合你的应用

现在你知道如何创建自己的预加载策略，比如`OptInPreloadStrategy`，你如何评估这是否是你的应用的正确策略？

考虑先预加载哪些包有助于改善用户体验可能是有意义的。你可以和你的商业用户(你的应用程序中的利益相关者)交谈，发现他们认为你的应用程序中最常出现的地方。你也可以使用现有应用程序的分析或进行用户研究来确定这一点。

最终决定权在你。我建议在选择这个选项或任何预加载策略之前，在各种有效和常见的用户工作流下以各种网络速度进行测试。这些数据将帮助你决定这是否是你的正确策略，或者另一个策略是否对你的应用程序的用户更有益。

## 资源

*   获取 [VS 代码](https://code.visualstudio.com/?wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度要素](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular-essentials&wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度片段](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular2&wt.mc_id=devto-blog-jopapa)