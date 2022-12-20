# 用角度表示的可观测悬挂物的冗余

> 原文：<https://dev.to/steveblue/redux-with-observable-stores-in-angular-1b3b>

事实证明，2019 年是 ng-conf 的可观察商店年，几位发言者在 Angular 应用程序中倡导这种模式。

我最近跳离了一个在 Angular 中使用 NgRx 进行状态管理的大型项目，不得不说一开始我很大程度上被淹没了。对 NgRx 的一个常见抱怨是它需要很多样板文件。的确，在 NgRx 中实现选择器、动作、减少器和效果时，保持关注点的分离可能会失控。新手很难理解 NgRx 是如何实现 redux 的，更不用说处理所有可以生成的文件了。即使是经验丰富的高级工程师也会因这种经历而自惭形秽。

[![NgRx kinda felt like this](img/b30cb6dd53c6658db91c779c2c18044a.png)](https://i.giphy.com/media/l1KVcPKdsU0sRSSt2/giphy.gif)

Redux 没必要这么复杂。Redux 的目的是用单向数据流模式简化复杂应用程序中的状态管理。保持模式简单有一些好处。

*   不熟悉 Redux 的人可以更快地升级
*   使用更少的样板文件更快地扩展
*   不使用另一个库会使包变得更小
*   控制状态管理的行为方式

## RxJS 行为主体

碰巧 NgRx 并不是在 Angular 中实现 redux 模式的唯一方法。在 RxJS 中，我们已经有了可以使用的工具，可以创建一个可观察的商店。我说的这种模式叫做可观察商店。可观察商店的最简单表达如下。

```
this._state$ = new BehaviorSubject(initialState); 
```

Enter fullscreen mode Exit fullscreen mode

RxJS 有`BehaviorSubject`，它本质上给了我们一个可观察的 API，但也维护状态。`BehaviorSubject`采取初始状态。

## 可观察商店

如果我们想抽象出在应用程序中创建一个可观察商店的能力，它可能是这样的。

```
 export interface AbstractState {
  [key: string]: any;
}

export class Store {

  private _state$: BehaviorSubject<AbstractState>;
  public state$: Observable<AbstractState>;

  constructor (initialState: AbstractState) {
    this._state$ = new BehaviorSubject(initialState);
    this.state$ = this._state$.asObservable() as Observable<AbstractState>;
  }

  get state(): AbstractState {
    return this._state$.getValue();
  }

  setState (nextState: AbstractState): void {
    this._state$.next(nextState);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

这就是抽象可观察商店的全部内容！

[![](img/2e40153b3e10447fff46c807c8209c4f.png)](https://i.giphy.com/media/WnCAPfflyfZjG/giphy.gif)

Store 类有一个私有属性，即 BehaviorSubject。名为`state$`的属性是公开的，供我们在整个应用程序中使用。我们可以调用`getState()`来检索状态，或者调用`setState`来改变状态。通过这样做，我们保留了一个可观察对象的所有特征，包括历史、错误处理等等。而且和 NgRx 比起来是那么的简单。

## 实现状态

然后，如果我们想在我们的应用程序中创建一些状态，它可能看起来像这样。

```
export interface SomeModel {
  name: string
}

export class LocalState {
  someModel: SomeModel[] = [];
}

@Injectable()
export class LocalStore extends Store {
  public state$: Observable<LocalState>;
  constructor () {
      super(new LocalState());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

关于上述实现的一些注意事项。注意我们已经声明了一个类来处理一些本地状态，然后为`LocalStore`声明了`state$`。这是为了确保我们与`LocalState`而不是`AbstractState`合作。然后在构造函数中，我们调用`super`，传入 LocalState，用适当的状态实例化`BehaviorSubject`。

## 在组件中使用状态

现在我们有了一些本地状态，是时候在组件中与它交互了。只需注入`LocalStore`，你甚至不需要`ngOnInit`来订阅状态变化。

```
 export class MyComponent {
  constructor(public store: LocalStore) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

在组件的模板中，你现在可以通过`async`管道使用状态。这样，视图将自动订阅状态块，并处理取消订阅。

```
<ul>
  <li *ngFor="let item of (store.state$ | async).someModel as SomeModel">{{item.name}}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

在 Angular 上勾搭一个可观察的店铺真的那么容易！到目前为止，我们只有这个状态的概念，这是 redux 模式的一部分。如果我们想实现 reducers 和 actions，会是什么样子呢？我们现在已经实现了我们自己的可观察商店！

现在你可以拿回 NgRx 从你身上夺走的一些“私人时间”了。

[![](img/c554956300f54451e2d3e5c921cf18a1.png)](https://i.giphy.com/media/l46C5MHNo0z9vXzvW/giphy.gif)

除了状态之外，还有更多的东西可以还原。如果您想用 redux 中的 action 和 reducer 模式来管理您的状态，但是是在这个定制的实现中，该怎么办呢？

## 动作和减速器

这只是实现 actions 和 reducers 的一种方式，它看起来很像 NgRx，但是样板文件要少得多。

首先，让我们创建一个枚举，在这里我们定义动作，并为动作的外观创建一个接口。

```
export enum LocalActions {
  ADD = '[SomeModel] Add',
  REPLACE = '[SomeModel] Replace',
  FETCH = '[SomeModel] Fetch'
}

export interface LocalAction {
  type: string;
  payload?: SomeModel[];
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以向 LocalStore 添加一个 reducer 方法来处理不同的操作。

```
reducer(state: LocalState, action?: LocalAction) {
  switch (action.type) {
    case LocalActions.ADD:
      return {
        ...state,
        someModel: [...state.someModel, action.payload]
      };
    case LocalActions.REPLACE:
      return {
        ...state,
        someModel: action.payload
      };
    case LocalActions.FETCH:
     this._fetch$ = this.service.fetchSomeModel().pipe(
       map(res => this.actions.emit({ type: LocalActions.REPLACE, 
                                      payload: res }))
       ).subscribe();
} 
```

Enter fullscreen mode Exit fullscreen mode

注意 FETCH 动作调用了一个服务方法。为了保持关注点的分离，我们可以将所有的 API 请求保存在它们自己的服务上，然后将其注入到 LocalState 类中。

```
@Injectable()
export class LocalStore extends Store {
  public state$: Observable<LocalState>;
  private _fetch$: Subscription;
  constructor (public service: LocalService) {
      super(new LocalState());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让 LocalStore 在状态改变时自动调用 reducer，我们需要更新它所扩展的 Store 类。在这里，我们将为 store 的操作添加一个订阅，我们在这里将其声明为 EventEmitter，这样从 store 扩展的所有类现在都可以发出操作。

```
@Injectable()
export class Store {

  private _subscription$: Subscription;
  private _state$: BehaviorSubject<AbstractState>;
  public state$: Observable<AbstractState>;
  public actions: EventEmitter<AbstractAction> = new EventEmitter();

  constructor (initialState: AbstractState) {
    this._state$ = new BehaviorSubject(initialState);
    this.state$ = this._state$.asObservable() as Observable<AbstractState>;
    this._subscription$ = from(this.actions).pipe(
        map((a: AbstractAction) => this.reducer(this.state, a)),
        map((s: AbstractState) => this.setState(s))
    ).subscribe();
  }
  ...
  reducer(state: AbstractState, action?: AbstractAction) {
      return state;
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们应用程序的任何地方，比如在我们上面声明的组件中，我们都可以发出后端请求，并用`FETCH`动作填充状态！

```
this.store.actions.emit({ type: LocalActions.FETCH }); 
```

Enter fullscreen mode Exit fullscreen mode

## 又发生了什么？

让我们来看看这个特定的动作会发生什么。

在`Store`中，我们分派动作的发射器有一个调用 reducer 的订阅。

```
this._subscription$ = from(this.actions).pipe(
    map((a: AbstractAction) => this.reducer(this.state, a)), 
```

Enter fullscreen mode Exit fullscreen mode

在 reducer 中，我们对服务发出一个 http 请求，当请求成功时，用响应分派另一个动作。

```
case LocalActions.FETCH:
this.service.fetchSomeModel().pipe(
       map(res => this.actions.emit({ type: LocalActions.REPLACE, 
                                      payload: res })) 
```

Enter fullscreen mode Exit fullscreen mode

在 reducer 中，传入`REPLACE`动作会覆盖状态。

```
case LocalActions.REPLACE:
  return {
    ...state,
    someModel: action.payload
  }; 
```

Enter fullscreen mode Exit fullscreen mode

由于对 EventEmitter on 状态的订阅也通过为我们调用`setState()`来更新状态，因此视图将自动获取对状态的更改。

```
from(this.actions).pipe(
        map((a: AbstractAction) => this.reducer(this.state, a)),
        map((s: AbstractState) => this.setState(s))
    ).subscribe(); 
```

Enter fullscreen mode Exit fullscreen mode

这意味着在我们的组件中，我们只需要分派一个动作来更新视图。异步管道为我们处理状态的订阅。

```
<ul>
  <li *ngFor="let item of (store.state$ | async).someModel as SomeModel">{{item.name}}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！这里重要的一点是，redux 可以简单，也可以复杂。通过用可观察的存储为你自己的状态编码一个机制，你可以理解在相当复杂的库中状态管理的幕后发生了什么。与 NgRx 相比，Redux 可以用最少的样板文件在 Angular 中实现，并且仍然在我们的应用程序中提供关注点的分离。

## 但不要相信我的话。

查看这些博客文章和视频，了解关于实现可观察商店的更多信息。

[利用可观察存储简化前端状态管理](https://blog.codewithdan.com/simplifying-front-end-state-management-with-observable-store/)Dan Wahlin。

Jure Bajt 在 Angular 中的状态管理和可观察的商店服务。

查看托马斯·伯利森的立面+ RxJS 。

我第一次接触到可观察商店的想法是从托马斯·伯利森的文章中。几年前，我的团队基于他在 ng-conf 上的一次演讲设计了一个完整的 AngularJS 应用程序。我对结果非常满意。几年后，当我读到他的帖子 [View Facades + RxJS](https://blog.angularindepth.com/angular-you-may-not-need-ngrx-e80546cc56ee) 时，我选择尝试服务门面和可观察商店。从那以后我再也没有回头。抱歉 NgRx。

在 ng-conf 2019 上，关于使用 RxJS 向应用程序提供状态的多个演示风靡一时。随着 YouTube 视频的发布，我会在这里发布它们的链接。

Deborah Kurata 提出的 RxJS 的数据构成。