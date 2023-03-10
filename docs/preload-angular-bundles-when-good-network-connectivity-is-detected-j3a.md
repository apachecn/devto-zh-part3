# 当检测到良好的网络连接时，预加载角度束

> 原文：<https://dev.to/angular/preload-angular-bundles-when-good-network-connectivity-is-detected-j3a>

用户想要快速应用。在用户需要之前，尽可能快地将 JavaScript 包下载到浏览器中，可以对用户体验产生巨大的积极影响。知道如何改善这种体验很重要。

提高 Angular 应用程序用户体验的一个方法是战略性地决定预加载哪些包。您可以控制何时加载包以及加载哪些包。这就是为什么你应该探索选择一个内置的或创建自己的自定义角度预载策略。

在这个系列中，我们将探索预加载角束的几种选择。

以下是这一系列的文章

*   [预载所有角束](https://dev.to/john_papa/preload-all-angular-bundles-1b6l)
*   [选择预加载哪些角束](https://dev.to/john_papa/you-pick-which-angular-bundles-to-preload-5l9)
*   [当检测到良好的网络连接时，预载角束](https://dev.to/azure/preload-angular-bundles-when-good-network-connectivity-is-detected-j3a)
*   [您的角形管束的预测预加载策略](https://dev.to/john_papa/predictive-preloading-strategy-for-your-angular-bundles-4bgl)

> 点击这里查看 Suguru Inatomi 的帖子，了解类似的网络感知预加载策略

## 检查连接是否良好

“网络感知”策略使用浏览器的[网络信息 API](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/connection) 来检测连接和速度。如果速度合格，那么它预装包。如果没有，它就不管他们了。

> 您可以在这里检查[网络信息 API 的浏览器兼容性。](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/connection#Browser_compatibility)

您可以通过创建一个实现`PreloadingStrategy`接口的类来创建自定义的`NetworkAwarePreloadStrategy`，并在根中提供它。然后，当你想告诉 Angular 预加载函数时，你必须实现`preload`函数并返回`load()`函数。

注意，`NetworkAwarePreloadStrategy`类中的`preload`函数首先检查`navigator.connection`对象的`saveData`属性。一些移动设备有“数据保护”的设置。启用后，该属性将为`true`。因此，如果用户已经决定保存他们的数据，他们是自愿这样做的，我们不应该预加载包(这会消耗数据)。

接下来，代码检查连接的`effectiveType`是否是您认为太慢而无法预加载包的连接速度之一。下面的代码决定 2g 和 slow-2g don；t 不符合预加载条件，但 3g 和 better 可以。所以只要`saveData`为真，连接速度为 3g 或更好，就会预加载捆绑包。

```
export declare var navigator;

@Injectable({ providedIn: 'root' })
export class NetworkAwarePreloadStrategy implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    return this.hasGoodConnection() ? load() : EMPTY;
  }

  hasGoodConnection(): boolean {
    const conn = navigator.connection;
    if (conn) {
      if (conn.saveData) {
        return false; // save data mode is enabled, so dont preload
      }
      const avoidTheseConnections = ['slow-2g', '2g' /* , '3g', '4g' */];
      const effectiveType = conn.effectiveType || '';
      console.log(effectiveType);
      if (avoidTheseConnections.includes(effectiveType)) {
        return false;
      }
    }
    return true;
  }
} 
```

## 设置自定义的 NetworkAwarePreloadStrategy

然后在设置您的`RouterModule`时，将包括`preloadingStrategy`在内的路由器选项传递给`forRoot()`功能。

```
@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: NetworkAwarePreloadStrategy
    })
  ],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

## 试试看

应用这个策略后，用`ng serve`重新构建并运行你的应用。打开您的浏览器，打开您的开发者工具，然后进入`http://localhost:4200`。当您在浏览器中检查 Network 选项卡时，您可能会看到所有的软件包都已经预加载。

然后在浏览器中将节流切换到 2g 速度，然后刷新页面。那么路由将不再被预加载。

## 决定什么适合你的应用

现在你知道如何创建自己的预加载策略，比如`NetworkAwarePreloadStrategy`，你如何评估这是否是你的应用的正确策略？

如果移动和低带宽/低 WiFi 场景可能适合您的用户，这可能是一个有益的预加载策略。如果你不确定，你可以和你的业务用户(你的应用程序中的利益相关者)谈谈来发现这一点。

您还可以将此策略与其他定制策略结合使用。

最终决定权在你。我建议在选择这个选项或任何预加载策略之前，在各种有效和常见的用户工作流下以各种网络速度进行测试。这些数据将帮助您决定这是否是适合您的策略，或者另一个策略是否对您的应用程序用户更有益。

## 资源

*   获取 [VS 代码](https://code.visualstudio.com/?wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度要素](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular-essentials&wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度片段](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular2&wt.mc_id=devto-blog-jopapa)