# 迷你 Redux 商店的扫描操作员

> 原文：<https://dev.to/fallenstedt/scan-operator-for-mini-redux-stores-41m9>

[![toaster](img/613141f958b24310cf99c36301d4c035.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wDHaZdJo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zniup3zx6m0ydqfpv9y6sgtf-wpengine.netdna-ssl.com/wp-content/uploads/2017/01/Toast_hero-hero.jpg)

我被安排在一个没有像 Redux 或 ngrx 这样的状态管理系统的 Angular 项目中。我认为这是一个使用 RxJS 引入状态管理的机会。

有`n+1`篇关于反应式编程的博文。简而言之，反应式编程关注的是异步数据。这些数据可以来自 API，也可以来自用户事件。

[![toast](img/fa4637f9ced143fd248c26e885b4dbb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XBgw1MD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0h31j6ziyfxqjtw0wnsd.gif)

任务是构建一个 toast 通知系统。类似于 Angular Material 的 Snackbar。这些要求是:

*   每个 toast 通知都会自动过期
*   用户可以提前关闭每个 toast 通知
*   而且一次可以有很多 toast 通知。

我最终使用 RxJS 中的`scan`操作符来实现数据持久性。你可以把扫描操作符想象成 JavaScript 的`reduce`方法。

这是在 Angular 服务内部完成的，但是，您可以在任何使用 RxJS 的项目中使用这些概念。

```
// toast.service.ts
// getter so no bad developer uses the store incorrectly.
  get store() { return this._store$; }

// Dispatch "actions" with a subject
  private _action$ = new Subject();

// Create a store which is just an array of 'Toast' 
  private _store$: Observable<Toast[]> = this._action$.pipe(
    map((d: ToastAction) => (!d.payload.id) ? this.addId(d) : d), // add id to toast to keep track of them.
    mergeMap((d: ToastAction) => (d.type !== ToastActionType.Remove) ? this.addAutoExpire(d) : of(d)), // concat a hide toast request with delay for auto expiring
    scan(this.reducer, []) // magic is here!
  );

// dispatch method 
  public dispatch(action: ToastAction): void {
    this._action$.next(action);
  }

// generate ids for the toast
  private addId(d: ToastAction): ToastAction {
    return ({
      type: d.type,
      payload: { ...d.payload, id: this.generateId() }
    });
  }

// If a user does not click on the toast to clear it, then it should auto expire
  private addAutoExpire(d: ToastAction) {
    const signal$ = of(d);
    const hide$ = of({ type: ToastActionType.Remove, payload: d.payload }).pipe(delay(this.config.duration));
    return concat(signal$, hide$);
  }

// generates a random string
  private generateId(): string {
    return '_' + Math.random().toString(36).substr(2, 9);
  }

// The reducer which adds and removes toast messages.
  private reducer(state: Toast[] = [], action: ToastAction): Toast[] {
    switch (action.type) {
      case ToastActionType.Add: {
        return [action.payload, ...state];
      }
      case ToastActionType.Remove: {
        return state.filter((toast: Toast) => toast.id !== action.payload.id);
      }
      default: {
        return state;
      }
    }
  }
} 
```

能够在这种情况下使用 scan 是完美的。我可以使用 reducer 函数将传入的数据流缩减为一个对象数组。

要使用该商店，您可以订阅`store`或在您的 Angular 模板中引用它。

```
// toast.component.ts
export class ToastComponent {
  public state$: Observable<Toast[]> = this.toastService.store;

  constructor(public toastService: ToastService) { }
  public add() {
    this.toastService.dispatch({ type: ToastActionType.Add, payload: { message: 'hi', status: ToastStatus.Info } });
  }
  public remove(payload: Toast) {
    this.toastService.dispatch({
      type: ToastActionType.Remove,
      payload
    });
  }
   ... 
```

但这是理想的吗？是的，它可以工作，但是，一个开发者不应该关心`ToastActionType`和构造对象。工作量很大。让我们在我们的 ToastService 中创建一些助手方法，这样任何开发人员都可以为我们想要的 toast 类型调用' enqueueSuccess '或' enqueueInfo'。

```
// toast.service.ts
@Injectable({
  providedIn: 'root'
})
export class ToastService {

  get store() { return this.store$; }

  private action$: Subject<ToastAction> = new Subject<ToastAction>();
  private store$: Observable<Toast[]> = this.action$.pipe(
    map((d: ToastAction) => (!d.payload.id) ? this.addId(d) : d),
    mergeMap((d: ToastAction) => (d.type !== ToastActionType.Remove) ? this.addAutoExpire(d) : of(d)),
    scan(this.reducer, [])
  );

  constructor() { }

  public enqueueSuccess(message: string): void {
    this.action$.next({
      type: ToastActionType.Add,
      payload: {
        message,
        status: ToastStatus.Success
      }
    });
  }

  public enqueueError(message: string): void {
    this.action$.next({
      type: ToastActionType.Add,
      payload: {
        message,
        status: ToastStatus.Error
      }
    }
    );
  }

  public enqueueInfo(message: string): void {
    this.action$.next({
      type: ToastActionType.Add,
      payload: {
        message,
        status: ToastStatus.Info
      }
    });
  }

  public enqueueWarning(message: string): void {
    this.action$.next({
      type: ToastActionType.Add,
      payload: {
        message,
        status: ToastStatus.Warning
      }
    });
  }

  public enqueueHide(payload: Toast): void {
    this.action$.next({
      type: ToastActionType.Remove,
      payload
    });
  }

  private addId(d: ToastAction): ToastAction {
    return ({
      type: d.type,
      payload: { ...d.payload, id: this.generateId() }
    });
  }

  private addAutoExpire(d: ToastAction) {
    const signal$ = of(d);
    const hide$ = of({ type: ToastActionType.Remove, payload: d.payload }).pipe(delay(this.config.duration));
    return concat(signal$, hide$);
  }

  private generateId(): string {
    return '_' + Math.random().toString(36).substr(2, 9);
  }

  private reducer(state: Toast[] = [], action: ToastAction): Toast[] {
    switch (action.type) {
      case ToastActionType.Add: {
        return [action.payload, ...state];
      }
      case ToastActionType.Remove: {
        return state.filter((toast: Toast) => toast.id !== action.payload.id);
      }
      default: {
        return state;
      }
    }
  }
} 
```

现在我们的组件可以更容易地调用我们的服务:

```
// toast.component.ts

export class ToastComponent {
  public state$: Observable<Toast[]> = this.toastService.store;

  constructor(public toastService: ToastService) { }
  public add() {
    this.toastService.enqueueInfo('It works!');
    this.toastService.enqueueWarning('Oops!');
    this.toastService.enqueueError('Uh oh!');
    this.toastService.enqueueSucccess(':D');
  }
   ... 
```

```
<!-- toast.component.html --> 
  <button 
    *ngFor="let toast of (state$ | async)"
    (click)="remove(toast)">
    <span [innerHtml]="toast.message"></span>
  </button> 
```

你得到的是许多 toast 通知和一个非常好的 API 的强大服务。