# NgRx 中从哪里开始数据加载

> 原文：<https://dev.to/jonrimmer/where-to-initiate-data-load-in-ngrx-358l>

在 NgRx 中，从数据源(比如 REST API 或 DB)加载数据是使用一个效果来完成的。然而，某些东西必须首先调度触发效果的动作。我看到了一些不同的建议/方法来做到这一点。

在我们的例子中，我们将从服务中加载一组`Order`实体。我们将介绍两个动作:`LoadOrdersRequested`和`LoadOrders`。其中的第一个将启动数据加载，然后一个效果将执行加载并分派一个`LoadOrders`动作，该动作将加载的数据放入存储中。

处理`LoadOrdersRequested`的效果如下:

```
@Effect()
loadOrdersRequested$ = this.actions$.pipe(
  ofType<LoadOrdersRequested>(ActionTypes.LoadOrdersRequested),
  // Don't load if we've already loaded.
  withLatestFrom(this.store.select(getAllOrdersLoaded)),
  filter(([_, loaded]) => !loaded),
  // Don't handle more than one load request at a time.
  exhaustMap(() => this.ordersService.fetchAllOrders().pipe(
    map(result => new LoadOrders(result))
  ))
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了启动数据加载，我们需要从某个地方调度`LoadOrdersRequested`动作。有四个主要选项:

1.  当应用程序启动时。
2.  当容器组件被初始化时。
3.  当应用程序导航到某条路线时。
4.  当用户执行一个动作时。

第四种可能是当用户单击一个按钮来显式加载或重新加载一些数据时。对于本文，我们将集中讨论前三个。

### app 启动时

优点:

*   保证加载数据。

缺点:

*   如果有大量数据要加载，内存/性能问题。

#### 在你的 AppComponent 中

最简单的方法是从您的`AppComponent`的 init 生命周期方法:
中分派`LoadOrdersRequested`动作

```
export class AppComponent implements OnInit  {
  constructor(private store: Store<AppState>) {}

  ngOnInit() {
    this.store.dispatch(new LoadOrdersRequested());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack blitz . com/edit/angular-ngrx-initiate-load-at-app-start-app-component](https://stackblitz.com/edit/angular-ngrx-initiate-load-at-app-start-app-component)

#### 以一种效果出现

NgRx 提供了一个在应用程序启动时调度的`INIT`动作。这似乎是一个开始数据加载的好地方，但是有一个问题。`INIT`动作在效果被订阅之前被调度，所以这不起作用:

```
@Effect()
init$ = this.actions$.pipe(
  ofType(INIT),
  map(() => new LoadOrdersRequested())
); 
```

Enter fullscreen mode Exit fullscreen mode

相反，NgRx 团队[推荐](https://github.com/ngrx/platform/issues/103)使用`defer` RxJS 操作符:

```
@Effect()
init$ = defer(() => new LoadOrdersRequested()); 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我们想让我们的效果潜在地触发*其他*效果，这种方法是行不通的。这是因为，虽然`defer`延迟了`LoadOrdersRequested`动作的创建，直到`init$`可观察值被订阅(在效果模块初始化期间)，但是动作将在初始化完成之前*被分派。因此，我们正在寻找的`LoadOrdersRequested`效果可能还没有被注册，这取决于效果系统订阅不同效果的顺序。*

我们也许可以通过对效果重新排序来缓解这个问题，但是更好的解决方案是使用`asyncScheduler`来延迟`LoadOrdersRequested`动作的调度:

```
 import { asyncScheduler, of } from 'rxjs';

...

@Effect()
$init = of(new LoadOrdersRequested, asyncScheduler); 
```

Enter fullscreen mode Exit fullscreen mode

虽然`INIT`不行，但是还有一个内置动作我们*可以*用:`ROOT_EFFECTS_INIT` :

```
@Effect()
$init = this.actions$.pipe(
  ofType(ROOT_EFFECTS_INIT),
  map(() => new LoadOrdersRequested())
); 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack blitz . com/edit/angular-ngrx-inite-load-at-app-start-init](https://stackblitz.com/edit/angular-ngrx-initiate-load-at-app-start-init)

#### APP _ 初始值设定项

Angular 提供了`APP_INITIALIZER`作为应用启动时运行代码的方式，你可以从那里调度动作:

```
@NgModule({
  ...
  providers: [
    {
      provide: APP_INITIALIZER,
      useFactory: (store: Store<AppState>) => {
        return () => {
          store.dispatch(new LoadOrdersRequested());
        };
      },
      multi: true,
      deps: [Store]
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack blitz . com/edit/angular-ngrx-initiate-load-at-app-start-app-initializer](https://stackblitz.com/edit/angular-ngrx-initiate-load-at-app-start-app-initializer)

### 初始化容器组件时

优点:

*   您只在需要时加载数据。
*   从组件中可以清楚地看到它所依赖的数据。

缺点:

*   您要么需要大量的操作，要么将同一个操作分派到几个地方。
*   该组件不太纯粹，因为它有加载数据的副作用。
*   您可能会忘记从需要数据的组件调度操作。如果您通常通过另一个组件来访问这个组件，而这个组件是由*启动数据加载的，那么这个 bug 可能会被掩盖。例如，您通常会在打开详细信息页面之前打开列表页面。然后，有一天，你直接导航到详细信息页面，它坏了。*

```
@Component({ ... })
export class OrdersComponent implements OnInit {
  order$: Observable<Order>;

  constructor(private store: Store<AppState>) {
    this.order$ = this.store.select(getOrder);
  }

  ngOnInit() {
    this.store.dispatch(new LoadOrdersRequested());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack blitz . com/edit/angular-ngrx-initiate-load-in-component](https://stackblitz.com/edit/angular-ngrx-initiate-load-in-component)

### 当 app 导航到一条路线时

优点:

*   少重复。路由层次结构的根处的单个守卫可以加载所有子路由的数据，即使它们是直接导航到的。
*   组件更纯粹，因为它们只从选定的状态映射到它们的模板输出。

缺点:

*   很直白:守卫会触发任何子路由的*数据加载，即使它的组件不需要它。*
*   从查看组件来看，它需要哪些数据来工作并不明显。如果它被移动到路由器层次结构中的其他地方，它就会中断。
*   如果需要某些特定数据的路由分散在整个路由器层次结构中，则用处不大，因为您需要在不同的位置包含防护。

#### 路由器防护

```
@Injectable()
export class OrdersGuard implements CanActivate {
  constructor(private store: Store<AppState>) {}

  canActivate(): Observable<boolean> {
    return this.store.pipe(
      select(getAllOrdersLoaded),
      tap(loaded => {
        if (!loaded) {
          this.store.dispatch(new LoadOrdersRequested());
        }
      }),
      filter(loaded => loaded),
      first()
    );
  }
}

const ROUTES: Route[] = [
  {
    path: 'orders',
    component: OrdersList,
    canActivate: [OrdersGuard],
    children: [
      ...
    ]
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack blitz . com/edit/angular-ngrx-initiate-load-router-guard](https://stackblitz.com/edit/angular-ngrx-initiate-load-router-guard)

一个基本的防护可以只调度`LoadOrdersRequested`动作，依靠效果过滤掉不必要的加载请求。但是，通过检查`allOrdersLoaded`的状况，警卫可以延迟导航，直到装载完成。

#### 路由器解析器

```
@Injectable()
export class OrdersResolver implements Resolve<boolean> {
  constructor(private store: Store<DatasetsState>) { }

  resolve(): Observable<boolean> {
    return this.store.pipe(
      select(allDatasetsLoaded),
      tap(loaded => {
        if (!loaded) {
          this.store.dispatch(new AllDatasetsRequested());
        }
      }),
      filter(loaded => loaded),
      first()
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://stack blitz . com/edit/angular-ngrx-initiate-load-router-resolve](https://stackblitz.com/edit/angular-ngrx-initiate-load-router-resolve)

使用解析器的工作方式与守卫非常相似。主要区别在于，解析是在与守卫稍微不同的环境下运行的，并且应该返回要合并到激活的路由的数据中的对象。但是，我们不应该这样做，因为组件应该从存储中检索数据，而不是从激活的路由中检索数据。因此，解析应该只返回一个布尔值。

#### 路由器动作效果

```
@Effect()
loadOrders$ = this.actions$.pipe(
  ofType<RouterNavigationAction>(ROUTER_NAVIGATION),
  withLatestFrom(this.store.select(allOrdersLoaded)),
  filter(([action, loaded]) => 
    action.payload.routerState.url.includes('/orders') && !loaded
  ),
  map(() => new LoadOrdersRequested())
); 
```

Enter fullscreen mode Exit fullscreen mode

优点:

*   把事情保持在 NgRx 之内，所以感觉更习惯。

缺点:

*   要求您检查新路由是否匹配，而不是像 guard 方法那样依赖路由器本身来完成这项工作。如果有人在你的路由器配置中改变了一个路径，但是在你的效果中忘记这么做，这可能会导致加载错误。

### 从选择器内部的检查中分派动作

```
export function getAllOrders(store: Store<AppState>) { 
  return createSelector(
    getOrders,
    state => {
      if (!state.allOrdersLoaded) {
        store.dispatch(new LoadOrdersRequested());
      }

      return state.orders;
    }
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我实际上还没有在野外看到过这种情况，但这是我想到的一种方法。

优点:

*   当且仅当数据被查询使用时，保证加载数据。

缺点:

*   违反了选择器应该是纯函数的原则。
*   如果您对重用和组合您的选择器不严格，您可能最终会得到一些触发负载的选择器和一些不触发负载的选择器，因为它们遵从触发负载的选择器。

#### 未来的可能性

听起来似乎有棱角的常春藤可能会开启在组件上使用元编程的可能性，以一种更加声明性的方式来配置像存储依赖这样的事情。

### 结论

我不确定这些方法中的任何一种在所有情况下都明显胜过所有其他方法。根据需要加载的数据源数量、数据量以及路由器树的复杂性和布局来选择方法可能是最好的。

例如，如果你有一个小而简单的应用程序，数据量很小，急切地在 INIT 加载所有东西可能是最好的主意。

但是，如果您有一个大型应用程序，该应用程序分为不同的要素，每个要素都需要从单独的源加载数据，则最好在每个要素的路径层次结构的根处使用一个防护来根据其要求调度加载操作。

如果您有一个复杂的应用程序，各种容器有部分重叠的数据需求，最好让每个容器分派动作来加载它们需要的内容。