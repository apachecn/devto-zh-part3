# Angular 状态管理 Angular 中的简单状态管理，仅包含服务和 RxJS

> 原文：<https://dev.to/avatsaev/simple-state-management-in-angular-with-only-services-and-rxjs-41p8>

软件开发中最具挑战性的事情之一是状态管理。目前有几个 Angular 应用程序的状态管理库:NGRX，NGXS，Akita...它们都有不同的管理状态的风格，最流行的是 [NGRX](https://ngrx.io/) ，它很大程度上遵循了 React world 的 [FLUX/Redux](https://facebook.github.io/flux/docs/in-depth-overview.html#content) 原则(基本上使用单向数据流和不可变的数据结构，但是使用 RxJS 可观察的流)。

但是，如果您不想学习、设置、使用整个状态管理库，并且不想处理一个简单项目的所有样板文件，如果您想通过只使用您作为 Angular 开发人员已经非常熟悉的工具来管理状态，并且仍然获得状态管理库提供的性能优化和一致性(关于推送变更检测，单向不可变数据流)，又该怎么办呢？

免责声明:这不是一个反对国家管理图书馆的帖子。我们确实在工作中使用 NGRX，它确实帮助我们在非常大和复杂的应用程序中管理非常复杂的状态，但是正如我经常说的，NGRX 为简单的应用程序将事情复杂化，为复杂的应用程序将事情简化，记住这一点。

在这篇文章中，我将向您展示一种简单的管理状态的方法，只需使用 **RxJS** 和**依赖注入**，我们所有的组件树都将使用 OnPush 变更检测策略。

假设我们有一个简单的 Todo 应用程序，我们想要管理它的状态，我们已经设置了组件，现在我们需要一个服务来管理状态，让我们创建一个简单的 Angular 服务:

```
// todos-store.service.ts

@Injectable({provideIn: 'root'})
export class TodosStoreService {

} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们需要的是，一种提供待办事项列表的方法，一种添加、删除、过滤和完成待办事项的方法，我们将使用 getters/setters 和 RxJS 的 Behaviour Subject 来完成:

首先，我们创建在 todos 中读写的方法:

```
// todos-store.service.ts

@Injectable({provideIn: 'root'})
export class TodosStoreService {

  // - We set the initial state in BehaviorSubject's constructor
  // - Nobody outside the Store should have access to the BehaviorSubject 
  //   because it has the write rights
  // - Writing to state should be handled by specialized Store methods (ex: addTodo, removeTodo, etc)
  // - Create one BehaviorSubject per store entity, for example if you have TodoGroups
  //   create a new BehaviorSubject for it, as well as the observable$, and getters/setters
  private readonly _todos = new BehaviorSubject<Todo[]>([]);

  // Expose the observable$ part of the _todos subject (read only stream)
  readonly todos$ = this._todos.asObservable();

  // the getter will return the last value emitted in _todos subject
  get todos(): Todo[] {
    return this._todos.getValue();
  }

  // assigning a value to this.todos will push it onto the observable 
  // and down to all of its subsribers (ex: this.todos = [])
  private set todos(val: Todo[]) {
    this._todos.next(val);
  }

  addTodo(title: string) {
    // we assaign a new copy of todos by adding a new todo to it 
    // with automatically assigned ID ( don't do this at home, use uuid() )
    this.todos = [
      ...this.todos, 
      {id: this.todos.length + 1, title, isCompleted: false}
    ];
  }

  removeTodo(id: number) {
    this.todos = this.todos.filter(todo => todo.id !== id);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建一个方法来设置 todo 的完成状态:

```
// todos-store.service.ts

setCompleted(id: number, isCompleted: boolean) {
  let todo = this.todos.find(todo => todo.id === id);

  if(todo) {
    // we need to make a new copy of todos array, and the todo as well
    // remember, our state must always remain immutable
    // otherwise, on push change detection won't work, and won't update its view 

    const index = this.todos.indexOf(todo);
    this.todos[index] = {
      ...todo,
      isCompleted
    }
    this.todos = [...this.todos];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，一个可观察的来源将只为我们提供完整的待办事项:

```
// todos-store.service.ts

// we'll compose the todos$ observable with map operator to create a stream of only completed todos
readonly completedTodos$ = this.todos$.pipe(
  map(todos => todos.filter(todo => todo.isCompleted))
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的 todos 商店看起来像这样:

```
// todos-store.service.ts

@Injectable({providedIn: 'root'})
export class TodosStoreService {

  // - We set the initial state in BehaviorSubject's constructor
  // - Nobody outside the Store should have access to the BehaviorSubject 
  //   because it has the write rights
  // - Writing to state should be handled by specialized Store methods (ex: addTodo, removeTodo, etc)
  // - Create one BehaviorSubject per store entity, for example if you have TodoGroups
  //   create a new BehaviorSubject for it, as well as the observable$, and getters/setters
  private readonly _todos = new BehaviorSubject<Todo[]>([]);

  // Expose the observable$ part of the _todos subject (read only stream)
  readonly todos$ = this._todos.asObservable();

  // we'll compose the todos$ observable with map operator to create a stream of only completed todos
  readonly completedTodos$ = this.todos$.pipe(
    map(todos => todos.filter(todo => todo.isCompleted))
  )

  // the getter will return the last value emitted in _todos subject
  get todos(): Todo[] {
    return this._todos.getValue();
  }

  // assigning a value to this.todos will push it onto the observable 
  // and down to all of its subsribers (ex: this.todos = [])
  private set todos(val: Todo[]) {
    this._todos.next(val);
  }

  addTodo(title: string) {
    // we assaign a new copy of todos by adding a new todo to it 
    // with automatically assigned ID ( don't do this at home, use uuid() )
    this.todos = [
      ...this.todos, 
      {id: this.todos.length + 1, title, isCompleted: false}
    ];
  }

  removeTodo(id: number) {
    this.todos = this.todos.filter(todo => todo.id !== id);
  }

  setCompleted(id: number, isCompleted: boolean) {
    let todo = this.todos.find(todo => todo.id === id);

    if(todo) {
      // we need to make a new copy of todos array, and the todo as well
      // remember, our state must always remain immutable
      // otherwise, on push change detection won't work, and won't update its view
      const index = this.todos.indexOf(todo);
      this.todos[index] = {
        ...todo,
        isCompleted
      }
      this.todos = [...this.todos];
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的智能组件可以轻松访问和操作商店:

*(PS:与其手工管理不变性，我建议使用一些东西[不变性 JS](https://immutable-js.github.io/immutable-js) )*

```
// app.component.ts

export class AppComponent  {
  constructor(public todosStore: TodosStoreService) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- app.component.html -->

<div class="all-todos">

  <p>All todos</p>

  <app-todo 
    *ngFor="let todo of todosStore.todos$ | async"
    [todo]="todo"
    (complete)="todosStore.setCompleted(todo.id, $event)"
    (remove)="todosStore.removeTodo($event)"
  ></app-todo>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这是完整的最终结果:

[使用真实 REST API 的 StackBlitz 完整示例](https://stackblitz.com/edit/angular-rxjs-store?file=src%2Fapp%2Ftodos-store.service.ts)

[https://stackblitz.com/edit/angular-rxjs-store?view=preview](https://stackblitz.com/edit/angular-rxjs-store?view=preview)

这也是一种可扩展的状态管理方式，通过使用 Angular 强大的 DI 系统，您可以轻松地将其他商店服务注入到彼此之中，将它们的观察对象与管道操作符结合起来以创建更复杂的观察对象，并注入诸如 HttpClient 之类的服务以从您的服务器中提取数据。不需要所有的 NGRX 样板文件或安装其他状态管理库。尽可能保持简单和轻松。

* * *

在推特上关注我，了解更多有趣的与角度相关的东西:[https://twitter.com/avatsaev](https://twitter.com/avatsaev)