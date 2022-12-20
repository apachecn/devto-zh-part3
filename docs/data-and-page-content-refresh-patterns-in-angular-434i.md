# 角度的数据和页面内容刷新模式

> 原文：<https://dev.to/eyassh/data-and-page-content-refresh-patterns-in-angular-434i>

我之所以推荐在角度类型脚本代码中一直使用 RxJS `Observable` s，并且[只在离 UI 声明最近的地方解包它们(通常使用`| async`管道)](https://blog.eyas.sh/2018/12/use-asyncpipe-when-possible/)，部分原因是因为它使得对可观察对象的其他转换变得可用和方便。两个这样的例子包括重试和刷新逻辑。

重新加载/刷新组件显示的数据的两个常见原因包括:

1.  应用程序中的用户操作导致数据改变(特别是，如果它以可能导致复杂状态改变的方式改变，等等)。)，和/或
2.  所显示的数据会随着时间而改变(例如，由于其在后端的状态的进展/改变，或者由另一个用户改变，等等)。)

让我们从一个简单的例子开始:

```
@Component({
  selector: 'task',
  template: `<ng-container *ngIf="(task$ | async) as task">
    <h1>{{task.name}}</h1>
    <p>Status: {{task.status}}</p>
    <sub-task *ngFor="let subTask of task.subtasks" [subTask]="subTask"/>`
})
export class TaskComponent {
  constructor(private readonly http: HttpClient) {}
  readonly task$ = this.http.get('/api/tasks/foo');
} 
```

假设用户添加了一个“标记为完成”按钮，这会改变所有子任务的服务器端状态。如何从服务器上获取关于我方状态的最新权威数据？这里有一个方法:

```
export class TaskComponent {
  constructor(private readonly http: HttpClient) {}

  private readonly refreshToken$ = new BehaviorSubject(undefined);
  private readonly task$ = this.refreshToken$.pipe(
    switchMap(() => this.http.get('/api/tasks/foo')));

  markAsComplete() {
    this.http.post('/api/tasks/foo', { state: State.Done })
      // N.B. contrary to my advice elsewhere, I'm happy to
      // directly subscribe here because this subscribe
      // callback has side effects.
      // Further, I don't worry about unsubscribing since
      // this returned Observable is a one-shot observable
      // that will complete after a single request.
      .subscribe(() => this.refreshToken$.next(undefined));
  }
} 
```

以这种方式添加刷新逻辑将对我们的模板代码产生最小的影响，并且看起来相对干净。更好的是，添加额外的变异函数只需要调用`refreshToken$.next`来确保加载新数据。

> 其他模式和示例，包括**定期轮询更新**，以及**使用输入观察值请求数据**(例如`ActivatedRoute.params`)在[的完整博客文章](https://blog.eyas.sh/2018/12/data-and-page-content-refresh-patterns-in-angular/)中提供。

作为一个单子，一个可观察对象是一个整洁的功能结构。您可以使用一组丰富的运算符来转换它。在 RxJS 中，这些还包括用于错误处理和重试的`catchError`、定时事件以及将多个单子组合成多个项目的单子。将可观测量视为另一个单子，反应式编程成为函数式编程的简单扩展。

在尽可能长的数据生命周期内处理这些可观察数据意味着您可以利用这些构造，使用简单的操作符来转换不可变的数据，而不是将这些数据解包到可变的标量中。

[在此阅读更多内容](https://blog.eyas.sh/2018/12/data-and-page-content-refresh-patterns-in-angular/)