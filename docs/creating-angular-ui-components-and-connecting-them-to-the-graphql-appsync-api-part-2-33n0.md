# 创建角度 UI 组件并将它们连接到 graph QL app sync API——第 2 部分

> 原文：<https://dev.to/beavearony/creating-angular-ui-components-and-connecting-them-to-the-graphql-appsync-api-part-2-33n0>

在本系列的第一部分中，我们设置了一个工作区，在一个共享库中配置了 AWS 云资源，添加了身份验证以保护私有数据，为 GraphQL API 生成了类型，编写了一个突变助手包装函数，并讨论了与此相关的各种主题。

[![beavearony image](img/d4a98c8b3973de5b2679b8ac142d8db9.png)](/beavearony) [## AWS Amplify GraphQL API 支持的 Angular/NX 工作空间入门——第 1 部分

### 迈克尔·古特曼

#angular #graphql #awsamplify #awsappsync](/beavearony/getting-started-with-a-angularnx-workspace-backed-by-an-aws-amplify-graphql-api---part-1-24m0)
We were busy, but until this point, we still haven't done much towards our UI. Let's create some components using the Angular CLI in this second part!

出于简洁的原因，我将省略一些样式，但是你可以在 GitHub 上找到它们[。](https://github.com/BeaveArony/amplify-angular-nx-todo)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [比瓦罗尼](https://github.com/BeaveArony) / [放大-角度-nx-todo](https://github.com/BeaveArony/amplify-angular-nx-todo)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 由 AWS Amplify GraphQL 后端支持的 NX 多应用程序设置

[![](img/a0933fe2c7de5900d55d5895db66a8cf.png)](https://raw.githubusercontent.com/BeaveArony/amplify-angular-nx-todo/master/app-screenshot.png)

## 入门指南

要部署此项目，请点击此按钮...

[![Deploy to Amplify Console](img/655d874f17e48001226716c9475089d5.png)](https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/BeaveArony/amplify-angular-nx-todo) 

...或者经历以下步骤:

1.  克隆此回购

```
git clone https://github.com/BeaveArony/amplify-angular-nx-todo.git
```

2.  转到目录并安装依赖项

```
npm install

# or

yarn
```

3.  初始化放大项目

```
amplify init
```

4.  在您的帐户中创建资源

```
amplify push
```

5.  启动应用程序

```
npm start
```

</article>

[View on GitHub](https://github.com/BeaveArony/amplify-angular-nx-todo)

## 构建 UI

1.  包装整个用户界面的 todo 页。该组件将由 app-component 导入，并将成为该库的唯一容器/智能组件
2.  创建新待办事项的表单
3.  用于切换*已完成*属性的可见性的过滤器组件
4.  待办事项列表组件及其待办事项组件
5.  显示错误消息的组件

```
ng g c --project=todo-ui --export  todo-page
ng g c --project=todo-ui -c=OnPush create-form
ng g c --project=todo-ui -c=OnPush todo-list
ng g c --project=todo-ui -c=OnPush todo-item
ng g c --project=todo-ui -c=OnPush filter-bar
ng g c --project=todo-ui -c=OnPush error-alert 
```

我想像 AWS 认证组件的应用程序风格。为了有助于样式，将`apps/todo-app/src/styles.css`的内容替换为:

```
/* You can add global styles to this file, and also import other style files */
@import '~aws-amplify-angular/theme.css';

html,
body {
  height: 100%;
}
body {
  margin: 0;
  background-color: var(--color-accent-brown);
}

/* Amplify UI fixes */
.amplify-form-input {
  width: calc(100% - 1em);
  box-sizing: border-box;
}
.amplify-alert {
  margin-top: 2em;
}
.amplify-greeting > * {
  white-space: nowrap;
}
.amplify-greeting-flex-spacer {
  width: 100%;
}

.card,
.amplify-authenticator {
  width: var(--component-width-desktop);
  margin: 1em auto;
  border-radius: 6px;
  background-color: var(--color-white);
  box-shadow: var(--box-shadow);
}
.card-container {
  width: 400px;
  margin: 0 auto;
  padding: 1em;
}

@media (min-width: 320px) and (max-width: 480px) {
  .card,
  .amplify-authenticator {
    width: var(--component-width-mobile);
    border-radius: 0;
    margin: 0.5em auto;
  }
  .card-container {
    width: auto;
  }
  .amplify-alert {
    left: 4%;
  }
} 
```

我们正在建立 html / body 和 card 类，它们看起来非常类似于 Amplify 组件样式。我们正在重用之前导入的 CSS 变量。amplify-authenticator 在移动视图中有圆角，所以我们也在媒体查询中对此进行了更改。

我们还修复了 html 输入标签的样式，因为它们在移动设备中向右延伸得太远了。

amplify angular authenticator 组件仍然存在一些样式问题，但是我认为对于一个简单的 UI 组件来说，支持一般的注册/登录过程已经足够好了。

## 待办事宜-页面

todo-page 组件是 todo-ui 模块的起点。它是唯一导出的组件，其他所有组件都在这个库内部。我们可以在稍后阶段引入路由，甚至延迟加载这个模块，以便更快地启动我们的应用程序。

该组件也将是处理 API 通信并向该模块中的其他表示组件提供数据的唯一容器组件。因为我们还没有在我们的组件中编写任何代码，所以我们用目前为止得到的内容来构建我们的视图。所有组件都是由`ng generate`命令生成的，可以在我们的 HTML 标记中引用。

将`libs/todo-ui/src/lib/todo-page/todo-page.component.html`更改为此

```
<div class="card">
  <div class="card-container">
    <my-create-form></my-create-form>
    <my-filter-bar></my-filter-bar>
  </div>
</div>
<my-error-alert></my-error-alert>
<div class="card">
  <div class="card-container"><my-todo-list></my-todo-list></div>
</div> 
```

并在我们的根应用程序模块`apps/todo-app/src/app/app.module.ts`中导入 todo-ui 模块，就像这样

```
// ... other imports
import { TodoUiModule } from '@my/todo-ui';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AppsyncModule, TodoUiModule],
  bootstrap: [AppComponent]
})
export class AppModule {} 
```

要在应用程序中显示待办事项页面，请将`apps/todo-app/src/app/app.component.html`更改为

```
<my-todo-page></my-todo-page> 
```

到目前为止，这只是我们 UI 的起点。你可以用`npm start`运行应用程序，浏览一个基本的骨架。

## AWS 认证器 UI-组件

AWS Amplify 为 Angular/Ionic、React 和 Vue 提供了几个嵌入式 UI 组件。我们可以选择

*   认证者
*   照片拾取器
*   唱片
*   聊天机器人

要将 AWS 验证器添加到我们的应用程序中，请更改`apps/todo-app/src/app/app.component.html`以包含它和 todo-page 组件。

```
<amplify-authenticator></amplify-authenticator>
<my-todo-page *ngIf="hydrated && isLoggedIn"></my-todo-page> 
```

`apps/todo-app/src/app/app.component.ts`和
的内容

```
import { Component, OnInit } from '@angular/core';
import { AppSyncClient } from '@my/appsync';
import { AmplifyService } from 'aws-amplify-angular';

@Component({
  selector: 'my-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  hydrated = false;
  isLoggedIn = false;

  constructor(private amplifyService: AmplifyService) {}

  ngOnInit(): void {
    AppSyncClient.hydrated().then(client => (this.hydrated = !!client));
    this.amplifyService.authStateChange$.subscribe(authState => {
      if (authState.state === 'signedOut' && this.isLoggedIn) {
        AppSyncClient.clearStore().then(() =>
          console.log('User signed out, store cleared!')
        );
      }
      this.isLoggedIn =
        authState.state === 'signedIn' || authState.state === 'confirmSignIn';
    });
  }
} 
```

AppSyncClient 持久化它的 redux 存储，所以我们应该只在存储被恢复时呈现 todo-ui。AppSyncClient 提供了一个`AppSyncClient.hydrated()`函数，我们可以使用它。
AmplifyService 提供了一个可观察的`authStateChange$`,我们可以订阅它，只在用户登录时显示 todo-ui。为了在用户退出时不留下任何缓存项，我们在该事件时清除了存储。当客户端没有恢复或者没有用户使用`*ngIf`指令登录时，`<my-todo-page>`组件是隐藏的。默认情况下，`<amplify-authenticator>`会显示一条问候消息，当用户登录时会显示一个退出链接，所以我们不需要隐藏它。

我们可以通过传入一个配置对象
来配置这个组件

```
<amplify-authenticator [signUpConfig]="signUpConfig"></amplify-authenticator> 
```

或者将框架改为 Ionic 并隐藏某些部分，比如退出链接

```
<amplify-authenticator
  framework="ionic"
  [hide]="['SignOut']"
></amplify-authenticator> 
```

## 创建演示界面

我们从表示组件开始，它们不使用任何 API，只通过`@Input()`获取数据，并使用 Angular 的`EventEmitter`通过`@Output()`发出事件。稍后我们
在 todo-page 组件中连接它们。

### 型号

创建一个文件`libs/todo-ui/src/lib/model.ts`来保存特定于这个 UI 库的类型。在我们的例子中，我们只输入 Todo 的可见性状态。

```
export type TodoFilterType = 'SHOW_ALL' | 'SHOW_ACTIVE' | 'SHOW_COMPLETED'; 
```

### 创建-表单组件

当提交反应式表单时，这个创建表单组件发出一个新的 todo 名称。当必填的文本字段为空时，我们还会禁用提交按钮。一旦我们发出一个新的 todo，我们清除输入字段。使用 form 标签和 submit 按钮可以让我们在输入 todo 文本后点击“Enter”。

`libs/todo-ui/src/lib/create-form/create-form.component.html`

```
<form [formGroup]="form" (ngSubmit)="createTodo()">
  <input type="text" formControlName="todoName" required />
  <button type="submit" [disabled]="!form.valid">+</button>
</form> 
```

`libs/todo-ui/src/lib/create-form/create-form.component.ts`

```
import {
  ChangeDetectionStrategy,
  Component,
  EventEmitter,
  Output
} from '@angular/core';
import { FormControl, FormGroup } from '@angular/forms';

@Component({
  selector: 'my-create-form',
  templateUrl: './create-form.component.html',
  styleUrls: ['./create-form.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CreateFormComponent {
  @Output() created = new EventEmitter<string>();
  form = new FormGroup({ todoName: new FormControl('') });

  createTodo() {
    const todoName = this.form.value.todoName.trim();
    if (todoName) {
      this.created.emit(todoName);
      this.form.setValue({ todoName: '' });
    }
  }
} 
```

### 滤棒组件

filter-bar 组件包含一个切换按钮栏，用于更改 todo 列表的可见性。选定的按钮将被禁用和样式，使其看起来活跃。我们可以使用放大角度主题中的 CSS 变量。

`libs/todo-ui/src/lib/filter-bar/filter-bar.component.html`

```
<nav>
  <button
    *ngFor="let btn of buttons"
    (click)="filter(btn.value)"
    [disabled]="currentFilter === btn.value"
  >
    {{ btn.name }}
  </button>
</nav> 
```

`libs/todo-ui/src/lib/filter-bar/filter-bar.component.css`

```
nav {
  margin-top: 0.5em;
  display: flex;
  justify-content: space-between;
}
button {
  border: 1px solid var(--color-blue);
  height: var(--button-height);
  width: 128px;
  color: var(--color-blue);
  text-transform: uppercase;
  cursor: pointer;
}

button:hover {
  background-color: var(--color-accent-blue);
}

button:disabled {
  background-color: var(--color-blue);
  color: white;
  border: none;
  cursor: auto;
} 
```

`libs/todo-ui/src/lib/filter-bar/filter-bar.component.ts`

```
import {
  ChangeDetectionStrategy,
  Component,
  EventEmitter,
  Output
} from '@angular/core';
import { TodoFilterType } from '../model';

@Component({
  selector: 'my-filter-bar',
  templateUrl: './filter-bar.component.html',
  styleUrls: ['./filter-bar.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class FilterBarComponent {
  @Output() filtered = new EventEmitter<TodoFilterType>();

  currentFilter: TodoFilterType = 'SHOW_ALL';
  buttons: { value: TodoFilterType; name: string }[] = [
    { value: 'SHOW_ALL', name: 'All' },
    { value: 'SHOW_ACTIVE', name: 'Active' },
    { value: 'SHOW_COMPLETED', name: 'Completed' }
  ];

  filter(query: TodoFilterType) {
    this.currentFilter = query;
    this.filtered.emit(this.currentFilter);
  }
} 
```

我们根据所选择的按钮发出一个过滤查询字符串。父 todo-page 组件将对此进行监听，并将查询作为输入传递给其子 todo-list 组件，这样它就可以根据 *completed* 属性过滤列表。

### 待办事项组件

todo-list 呈现 todo-item 组件的列表。这些项目负责显示所提供的输入值。我们使用 HTML 实体作为图标。当点击删除图标时，我们发出`deleted`事件，当点击该项目的其余部分时，发出一个`toggled`事件来改变该 todo 项的*完成*属性。

`libs/todo-ui/src/lib/todo-item/todo-item.component.html`

```
<div class="item" *ngIf="todo">
  <div
    class="checkmark"
    [class.checked]="todo.completed"
    (click)="toggled.emit()"
  >
    {{ todo.completed ? '✔' : '🗸' }}
  </div>
  <div
    class="content"
    [ngStyle]="{ textDecoration: todo.completed ? 'line-through' : 'none' }"
    (click)="toggled.emit()"
  >
    {{ todo.name }}
  </div>
  <div class="delete" (click)="deleted.emit()">✗</div>
</div> 
```

`libs/todo-ui/src/lib/todo-item/todo-item.component.ts`

```
import {
  ChangeDetectionStrategy,
  Component,
  EventEmitter,
  Input,
  Output
} from '@angular/core';
import { ListTodos_listTodos_items } from '@my/appsync';

@Component({
  selector: 'my-todo-item',
  templateUrl: './todo-item.component.html',
  styleUrls: ['./todo-item.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class TodoItemComponent {
  @Input() todo: ListTodos_listTodos_items;
  @Output() toggled = new EventEmitter<boolean>();
  @Output() deleted = new EventEmitter<boolean>();
} 
```

### 待办事项组件

todo-list 组件通过先前创建的 todo-item 组件呈现每个 todo 项目。为了对输入做出反应以过滤 todos，我们使用 getters 和 setters。组件生命周期挂钩`ngOnChanges`在这里也可以工作。

可见性过滤器是我们的应用程序中唯一的客户端状态，在我们的组件中进行管理。AWSAppSyncClient 在幕后使用的 Apollo 客户端将数据保存在一个规范化的 redux 存储中。

当我们考虑我们的架构时，我们有许多选择:

*   使用行为主体管理角度提供程序中的状态
*   编写操作和 reducers 来扩展底层 redux 存储
*   为我们的客户端状态使用单独的状态管理库
*   扩展 Apollo 客户机以使用 apollo-link-state，它提供用 GraphQL 操作查询和改变本地状态
*   保持它在我们的组件中
*   ...

扩展底层 redux 存储有点冒险，因为 AWSAppSyncClient 可以随时更改实现内部。我们会被某个版本卡住，或者需要重构我们的客户端状态。

对于这个简单的应用程序，使用行为主题可能是最简单的。对于重要的应用程序，我会使用状态管理库。使用 apollo-link-state 感觉是最干净的解决方案，将本地和远程状态放在一个地方。

```
<!-- libs/todo-ui/src/lib/todo-list/todo-list.component.html -->
<my-todo-item
  *ngFor="let todo of visibleTodos"
  [todo]="todo"
  (toggled)="toggled.emit(todo)"
  (deleted)="deleted.emit(todo)"
></my-todo-item>
<div *ngIf="!visibleTodos || visibleTodos?.length === 0">No todos</div> 
```

```
// libs/todo-ui/src/lib/todo-list/todo-list.component.ts
import {
  ChangeDetectionStrategy,
  Component,
  EventEmitter,
  Input,
  Output
} from '@angular/core';
import { ListTodos_listTodos_items } from '@my/appsync';
import { TodoFilterType } from '../model';

@Component({
  selector: 'my-todo-list',
  templateUrl: './todo-list.component.html',
  styleUrls: ['./todo-list.component.css'],
  changeDetection: ChangeDetectionStrategy.Default
})
export class TodoListComponent {
  @Output() toggled = new EventEmitter<ListTodos_listTodos_items>();
  @Output() deleted = new EventEmitter<ListTodos_listTodos_items>();

  private _filter: TodoFilterType;
  private _todos: ListTodos_listTodos_items[];

  @Input()
  public get filter(): TodoFilterType {
    return this._filter;
  }
  public set filter(value: TodoFilterType) {
    this._filter = value;
    this.visibleTodos = this.getVisibleTodos(this.todos, value);
  }

  @Input()
  public get todos(): ListTodos_listTodos_items[] {
    return this._todos;
  }
  public set todos(value: ListTodos_listTodos_items[]) {
    this._todos = value;
    this.visibleTodos = this.getVisibleTodos(value, this.filter);
  }

  visibleTodos: ListTodos_listTodos_items[];

  private getVisibleTodos(
    todos: ListTodos_listTodos_items[],
    filter: TodoFilterType
  ) {
    switch (filter) {
      case 'SHOW_ALL':
        return todos;
      case 'SHOW_COMPLETED':
        return todos.filter(t => t.completed);
      case 'SHOW_ACTIVE':
        return todos.filter(t => !t.completed);
      default:
        return todos;
    }
  }
} 
```

每当过滤器查询或提供的 todo 发生变化时，我们计算可见的 todo 并将它们存储在*visible todo*中。

## 错误提示组件

如果单击关闭按钮，这个可重用的组件会收到一条错误消息并发出一个事件。

```
<div class="card" *ngIf="errorMessage">
  <div class="card-container">
    <div class="flex-row">
      <span class="icon">⚠</span>
      <div class="msg">{{ errorMessage }}</div>
      <a class="close" (click)="closed.emit()">×</a>
    </div>
  </div>
</div> 
```

```
.flex-row {
  display: flex;
  justify-content: space-between;
}
.close {
  color: var(--color-gray);
  font-size: 32px;
  cursor: pointer;
}
.msg {
  width: 100%;
  margin: 10px 2em;
}
.icon {
  color: var(--red);
  font-size: 32px;
  width: 38px;
} 
```

```
import {
  ChangeDetectionStrategy,
  Component,
  Input,
  Output,
  EventEmitter
} from '@angular/core';

@Component({
  selector: 'my-error-alert',
  templateUrl: './error-alert.component.html',
  styleUrls: ['./error-alert.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ErrorAlertComponent {
  @Input() errorMessage: string | null = null;
  @Output() closed = new EventEmitter();
} 
```

## 观看 todos

现在我们已经构建了表示组件，我们可以回到包装组件，它处理所有事件，与我们的 API 交互并显示可能的错误。

```
// imports ...
@Component({
  selector: 'my-todo-page',
  templateUrl: './todo-page.component.html',
  styleUrls: ['./todo-page.component.css'],
  changeDetection: ChangeDetectionStrategy.Default
})
export class TodoPageComponent implements OnInit {
  todos: ListTodos_listTodos_items[];
  errorMessage: string | null = null;

  ngOnInit() {
    AppSyncClient.watchQuery<ListTodos, ListTodosVariables>({
      query: LIST_TODOS
    }).subscribe(todoList => {
      this.todos = todoList.data
        ? todoList.data.listTodos.items.sort((a, b) =>
            a.createdOnClientAt.localeCompare(b.createdOnClientAt)
          )
        : [];
    }, this.handleError);
  }

  handleError = (err: ApolloError) => {
    this.errorMessage = err.message;
  };
} 
```

我们在 *ngOnInit* 生命周期钩子中倾听我们 todos 的任何局部变化。为此使用 *watchQuery()* ，通知我们的组件本地操作，如添加或删除项目。为了监听远程更改，我们需要设置一个 GraphQL 订阅。幸运的是 *watchQuery()* 是通用的，所以我们可以指定先前生成的查询和变量类型。这在订阅块中为我们提供了完整的智能感知！如果我们收到数据，我们在客户端的 *createdOnClientAt* 属性上对它们进行排序。

> *watchQuery()* 返回一个 zen-observable，而不是来自 rxjs 的！因此，我们不能在这里使用异步管道。我们可以编写一个转换器，或者简单地使用 apollo-angular 来完成这项工作。

这里我们必须考虑变化检测。如果这个组件使用 OnPush ChangeDetectionStragy，我们的 UI 将不会对来自这个可观察对象的变化做出反应。我们可以像上面一样更改为默认策略，或者注入*changededetorref*来调用订阅块中的 *markForCheck()* 。

大概是这样，重点是变化:

```
import { ChangeDetectorRef } from '@angular/core';
// ...
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class TodoPageComponent {
  constructor(private cd: ChangeDetectorRef) {}

  ngOnInit() {
    AppSyncClient.watchQuery<ListTodos, ListTodosVariables>({
      query: LIST_TODOS
    }).subscribe(todoList => {
      // ...
      this.cd.markForCheck();
    });
  }
} 
```

Angular 的异步管道会自动为我们处理这个问题！

更改 html 文件中的一行，将我们的 todo 传递给 todo-list 组件:

```
<my-todo-list [todos]="todos"></my-todo-list> 
```

当*关闭*事件发出
时，通过传递错误消息并将其设置为 *null* 来与错误警报组件交互

```
<my-error-alert
  (closed)="errorMessage = null"
  [errorMessage]="errorMessage"
></my-error-alert> 
```

## 创建待办事项

要在我们的列表中显示项目，我们需要能够创建一个新的 todo。因此，让我们在组件类中实现一个处理程序，并向它传递 create-form 组件的 *created* 事件的事件有效载荷。

```
<my-create-form (created)="create($event)"></my-create-form> 
```

```
// ...
export class TodoPageComponent {
  // ...
  create(name: string) {
    executeMutation<CreateTodoVariables, CreateTodo_createTodo>({
      mutation: CREATE_TODO,
      variablesInfo: {
        input: {
          name,
          completed: false,
          createdOnClientAt: new Date().toISOString()
        }
      },
      cacheUpdateQuery: [LIST_TODOS],
      typename: 'Todo'
    }).catch(this.handleError);
  }
} 
```

在一条语句中发生了很多事情。我们来分解一下！

我们调用我们的通用助手函数 *executeMutation()* 并传递给它一个对象。该对象需要下列属性:

*   **突变**:graph QL 突变操作
*   **variablesInfo** :我们想要发送给我们的 API 的输入对象
*   **cacheUpdateQuery** :我们希望在缓存中更新的查询列表
*   **typename** :属性 *__typename* 的值

由于我们向列表中添加了一个新条目，我们需要更新缓存中的 *LIST_TODOS* 查询。就像把它添加到*的 cacheUpdateQuery* 的数组中一样简单。这样做将调用两次 watchQuery observable。首先是使用临时 ID 的乐观响应，就像我们期望的那样，如果一切顺利，我们的 API 会做出响应，最后是来自服务器的实际结果。

如果我们在尝试创建 todo 时收到一个错误，那么第二个调用将不再包含这个新的 todo，从而否定乐观 UI 更新。此外，我们正在处理返回承诺的 *catch* 部分中的错误，并使用我们的错误警告组件显示它。通过这种方式，用户可以得到刚刚发生的事情的提示，即使错误消息是非常技术性的。

> 注意，一切都是类型安全的，甚至是 *typename* 字符串！

## 更新待办事宜

为了更新 todo 的 completed 属性，我们监听 todo-list 组件的 *toggled* 事件。我们将有效载荷传递给 *toggle()* 函数，在这个过程中析构它。

```
<my-todo-list [todos]="todos" (toggled)="toggle($event)"></my-todo-list> 
```

```
// ...
export class TodoPageComponent {
  // ...
  toggle({
    id,
    completed,
    createdOnClientAt,
    name
  }: ListTodos_listTodos_items) {
    executeMutation<UpdateTodoVariables, UpdateTodo_updateTodo>({
      mutation: UPDATE_TODO,
      variablesInfo: {
        input: {
          completed: !completed,
          id,
          createdOnClientAt,
          name
        }
      },
      cacheUpdateQuery: null,
      typename: 'Todo'
    }).catch(this.handleError);
  }
} 
```

这看起来非常类似于创建突变。我们提供更新的项目，这次我们不需要更新缓存。Apollo 客户端会自动处理这个问题，所以我们可以将空数组传递给 T2 的 cacheUpdateQuery。

> 作为输入类型，看起来很奇怪。这是列表中某一项的类型，但我想这是我们在自动生成时必须付出的代价！

## 删除待办事项

为了删除 todo，我们监听 todo-list 组件的 *deleted* 事件。我们将有效载荷传递给 *toggle()* 函数，只析构 *id* 。

```
<my-todo-list
  [todos]="todos"
  (toggled)="toggle($event)"
  (deleted)="delete($event)"
></my-todo-list> 
```

```
// ...
export class TodoPageComponent {
  // ...
  delete({ id }: ListTodos_listTodos_items) {
    executeMutation<DeleteTodoVariables, DeleteTodo_deleteTodo>({
      mutation: DELETE_TODO,
      variablesInfo: {
        input: { id }
      },
      cacheUpdateQuery: [LIST_TODOS],
      typename: 'Todo'
    }).catch(this.handleError);
  }
} 
```

从列表中删除一个项目需要我们再次处理缓存。

## 处理过滤器

剩下的唯一事情就是监听过滤器栏组件，并将值传递给 todo-list 组件。我们从*‘SHOW _ ALL’*开始，但是不要对过滤器变量做任何其他事情。

```
<my-filter-bar (filtered)="filter = $event"> </my-filter-bar>
<!-- ... -->
<my-todo-list
  [todos]="todos"
  [filter]="filter"
  (toggled)="toggle($event)"
  (deleted)="delete($event)"
></my-todo-list> 
```

```
filter: TodoFilterType = 'SHOW_ALL'; 
```

就是这样。请看一下[这个回购](https://github.com/BeaveArony/amplify-angular-nx-todo)看完整的源代码。

## 带它兜风

让我们跑起来，以便有机会欣赏我们的作品。这里有几样东西供你玩玩:

*   创建至少两个用户，查看每个用户如何拥有自己的待办事项
*   添加新待办事项
*   尝试响应式用户界面
*   打开开发工具，勾选*离线*复选框，做一些操作，最后再次上线。
*   打开 redux dev-tools(如果安装的话)，检查 redux-offline 的 *outbox* ，apollo 客户端的 InMemoryCache 的规范化实体，以及 redux-persist 的补液动作
*   看看存储在 IndexedDB 中的东西和本地存储中的 JWT
*   在 *watchQuery()* 中设置一个断点，观察乐观响应的作用
*   在 *create()* 中将名称变量改为 *null* ,享受列表中发生的撤销以及显示的精彩错误消息
*   重新加载应用程序以欣赏持久缓存
*   登录 AWS 管理控制台，分析创建的 DynamoDB 表条目

## 重述

我们用有角度的组件创建了我们的 UI。包装组件通过连接表示组件、监听缓存中的变化以及使用我们在第 1 部分中设置的助手与 API 对话来协调一切。

## 接下来

在[第 3 部分](https://dev.to/beavearony/personal-growth-with-git-flow-and-aws-amplify-console-part-3-1f6c)中，我们将探索如何使用 amplify 控制台简单添加 CI/CD 管道，甚至看看如何添加第二个前端应用程序。