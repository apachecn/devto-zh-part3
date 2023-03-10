# 如何在表单中添加分页

> 原文：<https://dev.to/loicpoullain/how-to-add-pagination-in-foalts-2cef>

FoalTS 是用于创建节点的 web 框架。Js 应用。它是用 TypeScript 编写的，提供了内置的开发工具，并有一大组常见情况下所需的集成组件(密码加密、身份验证、验证等)。

在构建 REST API 时，在 GET 请求上添加分页是非常常见的。本文向您展示了如何做到这一点。

首先创建一个新项目。

```
npm install -g @foal/cli
foal createapp todo-app
cd todo-app
npm run develop 
```

Enter fullscreen mode Exit fullscreen mode

然后**生成一个名为
的新模型**

```
foal generate entity todo 
```

Enter fullscreen mode Exit fullscreen mode

打开生成的文件`src/app/entities/todo.entity.ts`并完成其内容:

```
import { Column, Entity, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class Todo {

  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  text: string;

} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！

下一步是**创建一个控制器**，它将在`/todos` :
处理`GET`请求

```
foal generate controller todos --register 
```

Enter fullscreen mode Exit fullscreen mode

打开`src/app/controllers/todos.controller.ts`，添加新路线，列出待办事项:

```
import { Get, HttpResponseOK, ValidateQuery, Context } from '@foal/core';
import { getRepository } from 'typeorm';

import { Todo } from '../entities';

export class TodosController {

  @Get()
  @ValidateQuery({
    properties: {
      skip: { type: 'number' },
      take: { type: 'number' },
    },
    type: 'object',
  })
  async readTodos(ctx: Context) {
    const todos = await getRepository(Todo).find({
      skip: ctx.request.query.skip,
      take: ctx.request.query.take
    });
    return new HttpResponseOK(todos);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您向`http://localhost:3000/todos`发送一个`GET`请求，服务器将用一个空数组来响应，因为数据库是空的。

您可以使用一个 *shell 脚本*将 todos 添加到您的数据库中。

```
foal generate script create-todo 
```

Enter fullscreen mode Exit fullscreen mode

打开生成的文件`src/scripts/create-todo.ts`并完成其内容:

```
import { createConnection } from 'typeorm';
import { Todo } from '../app/entities';

export const schema = {
  properties: {
    text: { type: 'string' }
  },
  required: [ 'text' ],
  type: 'object',
};

export async function main(args) {
  // Create a new connection to the database.
  const connection = await createConnection();

  // Create a new task with the text given in the command line.
  const todo = new Todo();
  todo.text = args.text;

  // Save the task in the database and then display it in the console.
  console.log(
    await connection.manager.save(todo)
  );

  // Close the connection to the database.
  await connection.close();
} 
```

Enter fullscreen mode Exit fullscreen mode

用一些待办事项填充数据库:

```
npm run build:scripts
foal run create-todo text="Learn TypeScript"
foal run create-todo text="Use FoalTS with MongoDB"
foal run create-todo text="Discover the CLI"
foal run create-todo text="Create a REST API with Foal"
foal run create-todo text="Download VSCode"
foal run create-todo text="Read the docs of TypeORM" 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在是测试 API 的时候了！

```
curl http://localhost:3000/todos
curl http://localhost:3000/todos?skip=1
curl http://localhost:3000/todos?take=2
curl "http://localhost:3000/todos?skip=1&take=3"
curl http://localhost:3000/todos?skip=notanumber 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！