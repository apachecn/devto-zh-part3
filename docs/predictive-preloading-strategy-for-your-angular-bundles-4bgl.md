# 为您的角形管束提供预测性预加载策略

> 原文：<https://dev.to/angular/predictive-preloading-strategy-for-your-angular-bundles-4bgl>

用户想要快速应用。在用户需要之前，尽可能快地将 JavaScript 包下载到浏览器中，可以对用户体验产生巨大的积极影响。知道如何改善这种体验很重要。

提高 Angular 应用程序用户体验的一个方法是战略性地决定预加载哪些包。您可以控制何时加载包以及加载哪些包。这就是为什么你应该探索选择一个内置的或创建自己的自定义角度预载策略。

在这个系列中，我们将探索预加载角束的几种选择。

以下是这一系列的文章

*   [预载所有角束](https://dev.to/john_papa/preload-all-angular-bundles-1b6l)
*   [选择预加载哪些角束](https://dev.to/john_papa/you-pick-which-angular-bundles-to-preload-5l9)
*   [当检测到良好的网络连接时，预载角束](https://dev.to/azure/preload-angular-bundles-when-good-network-connectivity-is-detected-j3a)
*   [您的角形管束的预测预加载策略](https://dev.to/john_papa/predictive-preloading-strategy-for-your-angular-bundles-4bgl)

## 侦察前方

“按需”策略在用户执行特定动作时预载一条或多条路线。您决定哪个操作将导致路线预加载。例如，您可以设置在用户悬停在按钮或菜单项上时预加载一条路线。

您可以通过创建一个实现`PreloadingStrategy`接口的类来创建自定义的`OnDemandPreloadService`，并在根中提供它。然后，当你想告诉 Angular 预加载函数时，你必须实现`preload`函数并返回`load()`函数。

注意`OnDemandPreloadService`类中的`preload`函数检查可观察的`preloadOnDemand$`。它通过管道传输可观察对象，并使用`mergeMap` RxJs 操作符切换到新的可观察对象。这个新的可观测值取决于局部`preloadCheck`函数。

`preloadCheck`函数检查`preloadOptions`(来自原始可观察值)是否有一个`routePath`与一条`data.preload`属性设置为`true`的路线相匹配。因此，在这里，我们选择预加载一些路由，并在明确请求时加载一些路由。

```
@Injectable({ providedIn: 'root', deps: [OnDemandPreloadService] })
export class OnDemandPreloadStrategy implements PreloadingStrategy {
  private preloadOnDemand$: Observable<OnDemandPreloadOptions>;

  constructor(private preloadOnDemandService: OnDemandPreloadService) {
    this.preloadOnDemand$ = this.preloadOnDemandService.state;
  }

  preload(route: Route, load: () => Observable<any>): Observable<any> {
    return this.preloadOnDemand$.pipe(
      mergeMap(preloadOptions => {
        const shouldPreload = this.preloadCheck(route, preloadOptions);
        return shouldPreload ? load() : EMPTY;
      })
    );
  }

  private preloadCheck(route: Route, preloadOptions: OnDemandPreloadOptions) {
    return (
      route.data &&
      route.data['preload'] &&
      [route.path, '*'].includes(preloadOptions.routePath) &&
      preloadOptions.preload
    );
  }
} 
```

## 路线定义

该策略要求您指出哪些路线可以预加载。您可以通过添加`data.preload`属性并在路由定义中将它设置为`true`来实现这一点，如下所示。

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

请注意，仪表板和英雄路线都将`preload.data`属性设置为`true`。但是，恶棍路线没有这个属性设置。在这个场景中，英雄和仪表板已经启用了预加载，但是恶棍只会在用户导航到这条路线时加载。

## 设置自定义 OnDemandPreloadService

然后在设置您的`RouterModule`时，将包括`preloadingStrategy`在内的路由器选项传递给`forRoot()`功能。

```
@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: OnDemandPreloadStrategy
    })
  ],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

## 决定何时预载

这里缺少的部分是你用来告诉应用程序预加载哪条路径以及何时预加载的机制。注意下面代码中的服务`OnDemandPreloadService`。你可以调用这个服务的`startPreload`函数，传递你希望预加载的路线。然后，下一个主题是`OnDemandPreloadService`服务(就像发布或发送信息一样)。那么无论是谁，无论是什么人，只要听了这个信息，就可以按照它去做。

这就是`OnDemandPreloadStrategy`策略的用武之地，因为它在监听。

```
export class OnDemandPreloadOptions {
  constructor(public routePath: string, public preload = true) {}
}

@Injectable({ providedIn: 'root' })
export class OnDemandPreloadService {
  private subject = new Subject<OnDemandPreloadOptions>();
  state = this.subject.asObservable();

  startPreload(routePath: string) {
    const message = new OnDemandPreloadOptions(routePath, true);
    this.subject.next(message);
  }
} 
```

## 绑定到鼠标悬停事件

现在，你的应用程序已经准备好预载路线，当你决定这样做。你可以通过绑定一个 DOM 事件比如`mouseover`并触发`OnDemandPreloadService`的`startPreload`函数来尝试。

```
<a
  [routerLink]="item.link"
  class="nav-link"
  (mouseover)="preloadBundle('heroes')"
  >heroes</a
> 
```

注意，下面的代码接受路由路径，并将其传递给`preloadOnDemandService.startPreload`函数。

```
preloadBundle(routePath) {
  this.preloadOnDemandService.startPreload(routePath);
} 
```

## 都在一起

让我们退后一步，看看这一切是如何运作的。

1.  一个用户悬停在你的锚标签上
2.  `mouseover`绑定调用组件中的一个函数，传递路由路径(本例中为‘heroes ’)
3.  该代码调用`PreloadOnDemandService`服务的`startPreload`，并将路由路径传递给它
4.  服务的下一个主题是 RxJS 主题，它被公开为一个可观察对象
5.  `OnDemandPreloadStrategy`得到了那个可观察对象的句柄，它知道它什么时候“下一步”
6.  `OnDemandPreloadStrategy`通过管道将其输入`mergeMap`并评估预加载路径
7.  如果它决定预加载，`OnDemandPreloadStrategy`用`load()`函数返回一个新的可观察值
8.  如果它决定不预加载，`OnDemandPreloadStrategy`返回一个带有`EMPTY`可观察值的可观察值(不预加载)
9.  角度路由器监听策略的`preload`功能的响应，并相应地预加载或不预加载。

## 试试看

应用这个策略后，用`ng serve`重新构建并运行你的应用。打开您的浏览器，打开您的开发者工具，然后进入`http://localhost:4200`。当您检查浏览器中的 Network 选项卡时，您可能会看到没有任何包已经预加载(除了默认导航到的路由，如果它是延迟加载的)。

然后将鼠标悬停在 HTML 元素上，该元素将触发您绑定的`mouseover`事件。在你的浏览器中检查你的网络标签，你会看到这个包将被预加载。

## 决定什么适合你的应用

现在你知道如何创建自己的预加载策略，比如`OnDemandPreloadService`，你如何评估这是否是你的应用的正确策略？

这肯定是一个更复杂的策略。这对你的用户有益吗？你的用户经常在选择搜索结果之前停留在搜索结果上吗？这通常会引发一个懒惰的加载包吗？如果是这样，也许这可以给预负荷一个跳跃。

如果您能够在加载新的包之前确定用户的行为和工作流经常遵循特定的路径，那么这种策略可能是有益的。

您可以将此应用于许多场景，例如悬停在 HTML 元素上、单击按钮或滚动到屏幕的特定区域。

最终决定权在你。我建议在选择这个选项或任何预加载策略之前，在各种有效和常见的用户工作流下以各种网络速度进行测试。这些数据将帮助您决定这是否是适合您的策略，或者另一个策略是否对您的应用程序用户更有益。

## 资源

*   获取 [VS 代码](https://code.visualstudio.com/?wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度要素](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular-essentials&wt.mc_id=devto-blog-jopapa)
*   获取 VS 代码[角度片段](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular2&wt.mc_id=devto-blog-jopapa)