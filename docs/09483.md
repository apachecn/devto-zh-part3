# Koa 应用程序中的依赖注入

> 原文：<https://dev.to/sacha/dependency-injection-in-koa-app-1lml>

在处理复杂项目时，注入依赖关系非常有用。在这里，我们将看到我如何使用这种模式来使我的测试更容易编写，并能够模拟一些在您的测试中很难重现的用例，如:数据库失败、经济错误等...

本文基于这个存储库中的代码示例: [koa-template-samples](https://github.com/SachaCR/koa-template-samples) 。他自己基于 [koa 模板](https://github.com/SachaCR/koa-template)库。

为了实现依赖注入，我使用了一个名为 [koa-depsi](https://www.npmjs.com/package/koa-depsi) 的小中间件。您可以查看他的 README.md 文件，了解它是如何工作的。

我鼓励您克隆 [koa-template-samples](https://github.com/SachaCR/koa-template-samples) 存储库来查看这个例子的完整源代码。

首先，我们将看到如何通过工厂函数创建我们的 Koa 应用程序。

```
// /lib/app.js

function createApp(dependencies = {}) {
  const app = new Koa()

  app.use(koadepsi(dependencies))

  app.use(todosRouter.routes())
  app.use(todosRouter.allowedMethods())
  return app
}

module.exports = createApp 
```

Enter fullscreen mode Exit fullscreen mode

*完整代码[此处](https://github.com/SachaCR/koa-template-samples/blob/master/lib/app.js#L11)T3】*

这个工厂函数允许我们使用不同依赖注入按需创建 Koa 应用程序。我们以后会不会看到，写我们的测试会很有用。

现在看看路由处理器 **createTodo** 。

```
// /lib/routes/todos/createTodo.js

const controller = require('../../controllers/todos')

async function createTodoHandler(ctx) {
  const dbClient = ctx.deps.dbClient
  const todo = await controller.createTodo(ctx.request.body, dbClient)

  ctx.status = 201
  ctx.body = todo
} 
```

Enter fullscreen mode Exit fullscreen mode

*完整代码[此处](https://github.com/SachaCR/koa-template-samples/blob/master/lib/routes/todos/createTodo.js)T3】*

路由处理器需要控制器，并从请求上下文中获得到数据库的连接。它调用控制器并将 dbClient 作为参数传递给控制器。

注意:*如果你愿意，可以直接将你的控制器插入你的路线。您可以按照自己喜欢的方式来实现这个模式。*

现在我们来看看控制器:

```
// /lib/controllers/todos/createTodo.js

async function createTodo(todoData, dbClient) {
  return await dbClient.query( todoData )
} 
```

Enter fullscreen mode Exit fullscreen mode

*完整代码[此处](https://github.com/SachaCR/koa-template-samples/blob/master/lib/controllers/todos/createTodo.js)T3】*

在这里，我们看到控制器与数据库完全分离。因此，如果您传递一个真正的 dbCLient 对象，数据库将被调用。但是如果注入一个假的 dbClient，就可以完全控制和模拟数据库返回的值。

让我们看一个测试例子来更好地理解这种模式的优点:

```
// /tests/integrations/createTodo.test.js 

  it('Should return an error on database failure', async () => {
    const server = app({
      dbClient: {
        query: async () => {
          throw new Error('Database is down')
        },
      }, logger
    }).listen(config.app.port)

    const response = await request
      .post('http://localhost/todos')
      .send({ title: 'Lean Javascript' })
      .catch(err => err.response)

    server.close()

    assert.deepStrictEqual(response.body, {
      error: 'CREATE_TODO_ERROR',
      info: {
        done: false,
        priority: 0,
        title: 'Lean Javascript',
      },
      message: 'Something went wrong while inserting new todo: Database is down',
    }, 'Response body should be an error with error messages concatenated by verror module')
    assert.deepStrictEqual(response.status, 500, 'Status code should be 500')
  }) 
```

Enter fullscreen mode Exit fullscreen mode

*完整代码[此处](https://github.com/SachaCR/koa-template-samples/blob/master/tests/integrations/createTodo.test.js)T3】*

在这个测试中，我们希望验证我们的代码是否正确处理了数据库错误。为了模拟数据库错误，我们在应用程序中注入了一个假的 dbCLient。当您尝试执行 SQL 查询时，这个假的 dbCLient 将`throw new Error('Database is down')`。这使我们能够很容易地模拟数据库可能抛出任何错误。

请注意，创建应用程序的工厂函数非常有用。你可以为你必须做的每个测试创建一个新的应用，并根据你的测试场景注入真实或虚假的依赖。

总结一下:

*   创建一个工厂函数来创建您的 Koa 应用程序。
*   使用 koa-depsi 注入您对每个请求上下文的依赖。
*   从请求上下文中检索路由中的依赖关系。
*   将这些依赖关系传播到您的控制器。
*   根据测试场景，在测试中注入虚假或真实的依赖。

希望这篇文章对你们有些人有用。我鼓励你阅读这个例子的完整代码[这里](https://github.com/SachaCR/koa-template-samples)

如果你喜欢这个测试方法，或者你有改进的想法，请在评论中告诉我。:-)

英语不是我的母语。所以如果你发现一些语法错误，请告诉我。