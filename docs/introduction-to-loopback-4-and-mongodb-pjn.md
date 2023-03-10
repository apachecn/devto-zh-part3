# Loopback 4 和 MongoDB 简介

> 原文：<https://dev.to/offhourscoding/introduction-to-loopback-4-and-mongodb-pjn>

大家好，感谢你们的来访，欢迎来到我在 dev.to 上的第一篇帖子。在这篇帖子中，我们将看看如何使用 Loopback 4 和 MongoDB 创建一个待办事项 API。

## 简介

我非常喜欢用 NodeJS 和 Express 构建后端 API，但是我经常发现自己一遍又一遍地创建/复制大量相同的代码。我最近开始了一个新项目，正在寻找一种简单的方法来创建我的 API。有很多后端框架可以选择，但我最终决定选择 [Loopback 4](https://loopback.io) 。

Loopback 已经存在一段时间了，但是他们最近发布了版本 4，这是对以前版本的巨大改变。在这篇文章中，我们将看看如何使用 Loopback 4 和 MongoDB 创建一个基本的 Todo list API。

## 安装环回 4 CLI

我们首先要做的是安装 Loopback 的 CLI。打开一个终端并执行以下命令。

```
npm i -g @loopback/cli 
```

Enter fullscreen mode Exit fullscreen mode

这将为我们安装最新版本，在撰写本文时是@loopback/cli version: 1.13.0

## 创建项目

安装 CLI 后，让我们用它来创建我们的应用程序。

```
$ lb4 app
? Project name: todo-list
? Project description: ToDo List API written in Loopback 4 and MongoDB
? Project root directory: todo-list
? Application class name: TodoListApplication
? Select features to enable in the project (Press <space> to select, <a> to toggle all, <i> to invert selection)
❯◉ Enable tslint: add a linter with pre-configured lint rules
 ◉ Enable prettier: install prettier to format code conforming to rules
 ◉ Enable mocha: install mocha to run tests
 ◉ Enable loopbackBuild: use @loopback/build helpers (e.g. lb-tslint)
 ◉ Enable vscode: add VSCode config files
 ◉ Enable docker: include Dockerfile and .dockerignore
 ◉ Enable repositories: include repository imports and RepositoryMixin
(Move up and down to reveal more choices) 
```

Enter fullscreen mode Exit fullscreen mode

我正在将我们的应用程序的名称设置为 todo-list，并给它一个描述。对于其余选项，我们使用默认值。

## 回环 4 项目结构

浏览刚刚为我们创建的项目，有很多东西需要了解。现在，让我们集中讨论 Loopback 4 的关键概念。

| 概念 | 描述 |
| --- | --- |
| 模型 | 与其他框架类似，模型用于定义一段数据的格式。与其他系统不同，您不需要向模型本身添加操作(创建、读取、更新、删除)。就我个人而言，我喜欢将模型定义和操作分开。 |
| 数据源 | 数据源是我们的 API 存储/获取数据的地方。这可能是来自文件的内存存储，或者在我们的例子中是来自 MongoDB 数据库。 |
| 贮藏室ˌ仓库 | 这花了我最长的时间去理解，因为与我用过的其他框架相比，这是最大的变化。存储库是模型和控制器之间的抽象层。模型定义了数据的格式，存储库添加了您可以使用模型执行的行为类型，控制器公开了 API 端点并与存储库进行交互。 |
| 控制器 | 与编写 Express API 相比，控制器是放置 API 端点逻辑和处理对 API 的请求/响应的地方。 |

有关文件夹结构的更多信息，请务必在这里查看官方文档。

## 型号

我们从创建模型开始我们的旅程。对于我们的 API，我们将为用户提供拥有不同待办事项列表的能力，并且在每个列表中拥有多个待办事项。为此，我们将创建一个 Todo 模型和一个 TodoList 模型。

### 待办事宜模型

使用 Loopback 的 CLI，我们可以通过提供模型的名称、类型和模型内部的属性来创建模型。

我们的 Todo 模型将具有以下属性

| 财产 | 描述 |
| --- | --- |
| 身份证明（identification） | 这将是 MongoDB ID。请注意，我们将它设置为字符串类型 |
| 标题 | 这将是我们 todo 项目的标题 |
| 描述 | 这是一个可选字段，用户可以在其中提供待办事项的更多详细信息 |
| 不完全的 | 标识项目是否完整的布尔值 |

在终端中使用 lb4 命令，我们像这样创建我们的模型...

```
$ lb4 model
? Model class name: Todo
? Please select the model base class Entity (A persisted model with an ID)
? Allow additional (free-form) properties? No
Let's add a property to Todo
Enter an empty property name when done

? Enter the property name: id
? Property type: string
? Is id the ID property? Yes
? Is it required?: No
? Default value [leave blank for none]:

Let's add another property to Todo
Enter an empty property name when done

? Enter the property name: title
? Property type: string
? Is it required?: Yes
? Default value [leave blank for none]:

Let's add another property to Todo
Enter an empty property name when done

? Enter the property name: description
? Property type: string
? Is it required?: No
? Default value [leave blank for none]:

Let's add another property to Todo
Enter an empty property name when done

? Enter the property name: isComplete
? Property type: boolean
? Is it required?: No
? Default value [leave blank for none]:

Let's add another property to Todo
Enter an empty property name when done

? Enter the property name:
   create src/models/todo.model.ts
   update src/models/index.ts

Model Todo was created in src/models/ 
```

Enter fullscreen mode Exit fullscreen mode

### TodoList 模型

我们将对我们的待办事项列表模型做同样的事情。

```
$ lb4 model
? Model class name: TodoList
? Please select the model base class Entity (A persisted model with an ID)
? Allow additional (free-form) properties? No
Let's add a property to TodoList
Enter an empty property name when done

? Enter the property name: id
? Property type: string
? Is id the ID property? Yes
? Is it required?: No
? Default value [leave blank for none]:

Let's add another property to TodoList
Enter an empty property name when done

? Enter the property name: title
? Property type: string
? Is it required?: Yes
? Default value [leave blank for none]:

Let's add another property to TodoList
Enter an empty property name when done

? Enter the property name:
   create src/models/todo-list.model.ts
   update src/models/index.ts

Model TodoList was created in src/models/ 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们只指定了 todo 列表的 id 和标题。稍后我们将手动定义 todo 列表和 todo 之间的关系。现在，让我们继续构建 API 的样板文件。

## 创建 MongoDB 数据源

使用 Loopback 的 datasource 命令，我们可以告诉我们的 API 在哪里存储/获取数据。对于我们的示例，我们选择 MongoDB，并向它提供连接信息。

```
$ lb4 datasource
? Datasource name: db
? Select the connector for db: MongoDB (supported by StrongLoop)
? Connection String url to override other settings (eg: mongodb://username:password@hostname:port/database):
? host: localhost
? port: 27017
? user:
? password: [hidden]
? database: todo-list
? Feature supported by MongoDB v3.1.0 and above: Yes
   create src/datasources/db.datasource.json
   create src/datasources/db.datasource.ts
npm WARN todo-list@1.0.0 No license field.

+ loopback-connector-mongodb@4.2.0
added 10 packages from 14 contributors and audited 3961 packages in 14.925s
found 3 vulnerabilities (1 low, 2 moderate)
  run `npm audit fix` to fix them, or `npm audit` for details
   update src/datasources/index.ts

Datasource db was created in src/datasources/ 
```

Enter fullscreen mode Exit fullscreen mode

请注意，CLI 会自动为我们安装 loopback-connector-mongodb 包。这是一个 loopbacks 用来与 MongoDB 通信的库。这简化了我们的代码，因为我们不必处理与数据库的连接/断开，也不必担心创建自己的查询。这可以同时成为一个优点和一个缺点。

## 储存库

接下来是我们的仓库。这里，我们将数据源和模型链接在一起。尽管超出了本教程的范围，但是您可以为每个存储库拥有不同的数据源。这允许您相对容易地与存储在不同来源的数据进行交互。

### TodoList 知识库

```
$ lb4 repository
? Please select the datasource DbDatasource
? Select the model(s) you want to generate a repository TodoList? Please select the repository base class DefaultCrudRepository (Legacy juggler bridge)
   create src/repositories/todo-list.repository.ts
   update src/repositories/index.ts

Repository TodoListRepository was created in src/repositories/ 
```

Enter fullscreen mode Exit fullscreen mode

### 创建待办事宜库

```
$ lb4 repository
? Please select the datasource DbDatasource
? Select the model(s) you want to generate a repository Todo? Please select the repository base class DefaultCrudRepository (Legacy juggler bridge)
   create src/repositories/todo.repository.ts
   update src/repositories/index.ts

Repository TodoRepository was created in src/repositories/ 
```

Enter fullscreen mode Exit fullscreen mode

## 控制器

最后一部分是生成我们的控制器。作为我们业务逻辑的一部分，每个待办事项必须链接到一个待办事项列表。为此，我们将创建两个控制器。一个处理我们的待办事项，另一个处理每个列表中的待办事项。您会注意到我们没有公开 todo API 目录。这是因为我们不希望允许用户创建未分配给待办事项列表的待办事项。

### TodoList 控制器

```
$ lb4 controller
? Controller class name: TodoList
? What kind of controller would you like to generate? REST Controller with CRUD functions
? What is the name of the model to use with this CRUD repository? TodoList
? What is the name of your CRUD repository? TodoListRepository
? What is the type of your ID? string
? What is the base HTTP path name of the CRUD operations? /todo-lists
   create src/controllers/todo-list.controller.ts
   update src/controllers/index.ts

Controller TodoList was created in src/controllers/ 
```

Enter fullscreen mode Exit fullscreen mode

Loopback 的 CLI 通过询问控制器的名称、它所链接的存储库的控制器类型、ID 类型和我们想要使用的 API 端点来引导我们完成这个过程。

### TodoListTodo 控制器

对于这个控制器，我们准备选择一个空的控制器，自己编码。因为它将嵌套在 TodoList 控制器中，所以与标准的 CRUD 操作相比，您会注意到一些变化。

```
$ lb4 controller
? Controller class name: TodoListTodo
? What kind of controller would you like to generate? Empty Controller
   create src/controllers/todo-list-todo.controller.ts
   update src/controllers/index.ts

Controller TodoListTodo was created in src/controllers/ 
```

Enter fullscreen mode Exit fullscreen mode

todo-list-todo . controller . ts

```
import {TodoListRepository} from './../repositories/todo-list.repository';
import {
  repository,
  Filter,
  CountSchema,
  Where,
  Count,
} from '@loopback/repository';
import {
  post,
  requestBody,
  param,
  get,
  patch,
  getWhereSchemaFor,
  del,
} from '@loopback/rest';
import {Todo} from '../models';

export class TodoListTodoController {
  constructor(
    @repository(TodoListRepository)
    protected todoListRepo: TodoListRepository,
  ) {}

  @post('/todo-lists/{id}/todos', {
    responses: {
      '200': {
        description: 'TodoList.Todo model instance',
        content: {
          'application/json': {schema: {'x-ts-type': Todo}},
        },
      },
    },
  })
  async create(
    @param.path.string('id') id: string,
    @requestBody() todo: Todo,
  ): Promise<Todo> {
    return await this.todoListRepo.todos(id).create(todo);
  }

  @get('/todo-lists/{id}/todos', {
    responses: {
      '200': {
        description: "Array of Todo's belonging to TodoList",
        content: {
          'application/json': {
            schema: {type: 'array', items: {'x-ts-type': Todo}},
          },
        },
      },
    },
  })
  async find(
    @param.path.string('id') id: string,
    @param.query.object('filter') filter?: Filter,
  ): Promise<Todo[]> {
    return await this.todoListRepo.findTodos(id);
    // return await this.todoListRepo.todos(id).find(filter);
  }

  @patch('/todo-lists/{id}/todos', {
    responses: {
      '200': {
        description: 'TodoList.Todo PATCH success count',
        content: {'application/json': {schema: CountSchema}},
      },
    },
  })
  async patch(
    @param.path.string('id') id: string,
    @requestBody() todo: Partial<Todo>,
    @param.query.object('where', getWhereSchemaFor(Todo))
    where?: Where,
  ): Promise<Count> {
    return await this.todoListRepo.todos(id).patch(todo, where);
  }

  @del('/todo-lists/{id}/todos', {
    responses: {
      '200': {
        description: 'TodoList.Todo DELETE success count',
        content: {'application/json': {schema: CountSchema}},
      },
    },
  })
  async delete(
    @param.path.string('id') id: string,
    @param.query.object('where', getWhereSchemaFor(Todo))
    where?: Where,
  ): Promise<Count> {
    return await this.todoListRepo.todos(id).delete(where);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！通过使用 Loopback 4 的 CLI，我们将生成所有的 API 逻辑。我不知道你怎么想，但这比手工操作要快得多。我们需要自己动手做一些事情，现在我们来看看。

## 模型之间的关系

在我们的 API 中，我们需要在 todo 列表和 todo 项之间定义一对多的关系。一个待办事项列表可以有多个待办事项，但一个待办事项只能分配给一个待办事项列表。

### todo-list.model.ts

首先，我们将通过添加一个 hasMany 属性来添加修改我们的 todo-list 模型。打开 todo-list.model.ts，修改如下

```
import {hasMany, Entity, model, property} from '@loopback/repository';
import {Todo} from './todo.model';

@model({})
export class TodoList extends Entity {
  @property({
    type: 'string',
    id: true,
  })
  id?: string;

  @property({
    type: 'string',
    required: true,
  })
  title: string;

  @hasMany(() => Todo)
  todos?: Todo[];

  constructor(data?: Partial<TodoList>) {
    super(data);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我们正在导入 hasMany 属性和 Todo 模型。我们还添加了一个名为 todos 的新属性，这是一个 todo 数组。

### todo-list.repository.ts

我们已经告诉我们的模型使用类型 Todo。现在，我们将 Todo 存储库导入到我们的 todo-list repo 中，这样我们就可以访问它们的底层操作。

```
import {TodoRepository} from './todo.repository';
import {
  DefaultCrudRepository,
  HasManyRepositoryFactory,
  repository,
} from '@loopback/repository';
import {TodoList, Todo} from '../models';
import {DbDataSource} from '../datasources';
import {inject, Getter} from '@loopback/core';

export class TodoListRepository extends DefaultCrudRepository<
  TodoList,
  typeof TodoList.prototype.id
> {
  public readonly todos: HasManyRepositoryFactory<
    Todo,
    typeof TodoList.prototype.id
  >;
  constructor(
    @inject('datasources.db') dataSource: DbDataSource,
    @repository.getter(TodoRepository)
    protected todoRepositoryGetter: Getter<TodoRepository>,
  ) {
    super(TodoList, dataSource);
    // We associate the todos property to be hasmany of type Todo.
    this.todos = this.createHasManyRepositoryFactoryFor(
      'todos',
      todoRepositoryGetter,
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### todo.model.ts

最后一步是告诉 Todo 它属于哪个列表。我们通过向 todo 模型
添加一个 todoListId 属性来实现这一点

```
import {Entity, model, property} from '@loopback/repository';

@model({settings: {}})
export class Todo extends Entity {
  @property({
    type: 'string',
    id: true,
  })
  id?: string;

  @property({
    type: 'string',
    required: true,
  })
  title?: string;

  @property({
    type: 'string',
  })
  description?: string;

  @property({
    type: 'boolean',
  })
  isComplete?: boolean;

  @property()
  todoListId: string;

  constructor(data?: Partial<Todo>) {
    super(data);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## MongoDB 怪癖

MongoDB 连接器中目前存在一个 bug，由于 id 属性没有在对象 id 中转换，因此在查找嵌套信息时会返回一个空数组。这影响了我们的代码，因为在 todoslist 中查找 todo 的正常方法。为了解决这个问题，我们将创建自己的查找逻辑。再次打开 todo-list.repository.ts，并添加以下函数。

```
 async findTodos(id: string): Promise<Todo[]> {
    return await this.todoRepo.find().then(todos => {
      return todos.filter(todo => {
        return todo.todoListId === id;
      });
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

在 todolist.controller.ts 中，我们将修改 get 方法以使用新函数。将 find 函数修改如下。

```
 async find(
    @param.path.string('id') id: string,
    @param.query.object('filter') filter?: Filter,
  ): Promise<Todo[]> {
    return await this.todoListRepo.findTodos(id);
    // return await this.todoListRepo.todos(id).find(filter);
  } 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于当前 bug 的信息，您可以在这里跟踪 is [和](https://github.com/strongloop/loopback-next/issues/2085)

## 测试

要启动我们的 API 服务器，打开您的终端并执行

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

您使用其他 API 测试工具 list Postman，现在仍然可以，但是 Loopback 的一个特性是它们的 API explorer，它为您提供了查看和测试您的 API 的能力。通过在浏览器中导航到 localhost:3000 来检查它。

## 结论

在短短几分钟内，我们无需编写太多代码就能构建一个完全可用的 API。我将继续玩 Loopback 4，并期待分享我的经验。

你正在使用什么后端框架？你看过回环 4 了吗？

完整的代码可以在我的 GitHub 上找到，[这里](https://github.com/offhourscoding/lb4-todo-list-mongodb)。

这篇文章也发表在我的网站上[下班时间](https://offhourscoding.com/loopback-4-todo-list-api-with-mongodb/)