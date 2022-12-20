# 用 AdonisJS 构建 API(第 3 部分)

> 原文：<https://dev.to/nilomiranda/building-an-api-with-adonisjs-part-3-5cn1>

大家好！系列第三部终于来了！👏👏👏👏

如果你是新手，这是一个系列，将涵盖我们使用 [AdonisJS](https://adonisjs.com) 构建 API 所需的所有步骤。这是该系列的第三部分，以下是之前帖子的链接:

*   [第一部分](https://dev.to/nilomiranda/creating-an-api-using-adonisjs-part-1-2mk0)
*   [第二部分](https://dev.to/nilomiranda/building-an-api-with-adonisjs-part-2-3p94)

在这一部分，我保证会更短，我们将讨论如何为用户实现创建新事件的功能，设置特定的日期、位置和时间。

因此，我们将学习如何创建一个新的模型，因为之前的模型在我们搭建应用程序时就已经创建好了，如何创建一个新的迁移来正确设置表中需要的列，以及如何在模型之间建立关系。

所以让我们把手弄脏吧...

## 创建事件表

这个 API 将允许用户安排事件，设置地点、时间、日期和事件的标题(名称)

因此，我们需要设置 4 列:

*   标题(字符串)
*   位置(字符串)
*   日期(日期)
*   时间(时间戳)

因为该表将与用户有关系，因为用户可能有任意数量的事件，所以我们还需要一个用户 ID 列。该列将引用主列 User。

在 Adonis 中，为了创建一个新的模型，我们将执行以下操作:

```
adonis make:model Event -c -m 
```

Enter fullscreen mode Exit fullscreen mode

我在这里做的是告诉阿多尼斯制作一个新模型，名为`Event`，我传递两个标志:`-c`和`-m`。这两个标志将告诉 adonis 也创建控制器(`-c`)和迁移(`-m`)。

现在让我们开始构建我们的表。转到迁移文件`database/migrations/1551814240312_event_schema.js`

在您的类`EventSchema`中，对于`up()`方法，执行以下操作:

```
class EventSchema extends Schema {
  up () {
    this.create('events', (table) => {
      table.increments()
      table
        .integer('user_id')
        .unsigned()
        .references('id')
        .inTable('users')
        .onUpdate('CASCADE')
        .onDelete('SET NULL')
      table.string('title').notNullable()
      table.string('location').notNullable()
      table.datetime('date').notNullable()
      table.time('time').notNullable()
      table.timestamps()
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看我们在做什么:

```
table
    .integer('user_id')
    .unsigned()
    .references('id')
    .inTable('users')
    .onUpdate('CASCADE')
    .onDelete('SET NULL') 
```

Enter fullscreen mode Exit fullscreen mode

上面这段代码负责创建用户的 ID 列和对用户表的引用。

首先，我们将数据类型设置为 integer，在参数中用`table.integer('user_id')`将列名设置为`user_id`。

使用`.unsigned()`我们将列设置为只接受正值(-1，-2，-3 是无效数字)。

然后，我们告诉该列引用用户表中带有`. inTable('users ')的用户 id 列`.references('id)`。

如果我们碰巧更改了事件的所有者 ID，那么所有的更改都会反映到事件表中，所以列`user_id`中的用户 ID 也会更改(`.onUpdate('CASCADE')`)。

如果用户的帐户最终被删除，被删除用户拥有的事件的事件列`user_id`将全部被设置为空`.onDelete('SET NULL')`。

``javascript
table.string('title').notNullable()
table.string('location').notNullable()
table.datetime('date').notNullable()
table.time('time').notNullable()`` 

 ``现在我们设置其他列:

*   标题列，用`table.string('title')`作为字符串
*   位置列，也作为一个带有`table.string('location')`的字符串
*   日期列，用`table.datetime('date')`表示日期时间
*   和时间列，作为带有“table . TIME(‘TIME’)的时间

注意，对于每一列中的所有这些列，我还设置了`.notNullable()`,因为用户每次创建新事件时都必须设置这些值。

完成所有这些工作后，我们可以运行我们的迁移:

```
adonis migration:run 
```

Enter fullscreen mode Exit fullscreen mode

为了完成事件表和用户表之间关系的设置，我们有两个选项:

*   我们在用户模型中设置关系
*   我们在事件的模型中设置关系

在这个例子中，我们将在用户的模型中设置关系。我们不需要在两个模型中设置关系，因为 Adonis 的文档本身声明:

> 不需要在两个模型上定义关系。只需要在单个模型上设置为单向。

所以我们去`App/Models/User.js`添加方法`events()`。

```
events () {
    return this.hasMany('App/Models/Event')
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要做的！现在我们将能够开始创建我们的控制器来创建和列出新的事件。

## 创建并保存新事件

首先，让我们创建我们的`store()`方法，使用户能够创建和保存一个新事件。

在`App/Controllers/Http/EventController.js`我们将做:

```
async store ({ request, response, auth }) {
    try {
      const { title, location, date, time } = request.all() // info for the event
      const userID = auth.user.id // retrieving user id current logged

      const newEvent = await Event.create({ user_id: userID, title, location, date, time })

      return newEvent
    } catch (err) {
      return response
        .status(err.status)
        .send({ message: {
          error: 'Something went wrong while creating new event'
        } })
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

真的很简单。我们用`request.all()`检索来自请求的数据

我们还需要检索登录用户的 ID，但是我们将这些数据保存在 auth 对象中，由上下文提供。

```
const userID = auth.user.id 
```

Enter fullscreen mode Exit fullscreen mode

要使用这个控制器，我们只需在`Route.group()` :
内创建一条新的路线

```
Route.post('events/new', 'EventController.store') 
```

Enter fullscreen mode Exit fullscreen mode

为了测试事件的创建，我们向这个路由发送一个请求，按照下面的结构发送一个 JSON 数据:

```
{  "title":  "First event",  "location":  "Sao Paulo",  "date":  "2019-03-16",  "time":  "14:39:00"  } 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，请求将返回您创建的事件:

```
{  "user_id":  10,  "title":  "First event",  "location":  "Sao Paulo",  "date":  "2019-03-16",  "time":  "14:39:00",  "created_at":  "2019-03-16 14:40:43",  "updated_at":  "2019-03-16 14:40:43",  "id":  6  } 
```

Enter fullscreen mode Exit fullscreen mode

## 清单事件

我们将有两种方法在这个 API 中列出事件，列出所有事件或按日期。

让我们从列出所有事件开始。我们将创建一个方法`index()` :

```
async index ({ response, auth }) {
    try {
      const userID = auth.user.id // logged user ID

      const events = await Event.query()
        .where({
          user_id: userID
        }).fetch()

      return events
    } catch (err) {
      return response.status(err.status)
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将列出通过登录用户的 ID 搜索的所有事件，就像我们在使用`auth.user.id`获取该信息之前所做的那样。

我们在这里查询数据的方式将与我们之前所做的略有不同，因为在这种情况下我们不会使用任何静态方法。

```
const events = await Event.query()
        .where({
          user_id: userID
        }).fetch() 
```

Enter fullscreen mode Exit fullscreen mode

我们用`.query()`打开查询，然后设置 where 语句，将一个对象作为参数传递给过滤器来搜索数据:

```
.where({
    user_id: userID
}) 
```

Enter fullscreen mode Exit fullscreen mode

与特殊的静态方法不同，我们需要链接方法`.fetch()`来正确地检索数据。

这更容易测试，我们只需要在`start/routes.js` :
中为 GET 请求设置一个路由

```
Route.get('events/list', 'EventController.index') 
```

Enter fullscreen mode Exit fullscreen mode

这个请求不需要任何参数。如果成功完成，你将得到一个数组中所有事件的列表:

```
[  {  "id":  6,  "user_id":  10,  "title":  "First event",  "location":  "Sao Paulo",  "date":  "2019-03-16T03:00:00.000Z",  "time":  "14:39:00",  "created_at":  "2019-03-16 14:40:43",  "updated_at":  "2019-03-16 14:40:43"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将按日期列出事件，为此，我们将创建一个名为`show()`的方法。

```
async show ({ request, response, auth }) {
    try {
      const { date } = request.only(['date']) // desired date
      const userID = auth.user.id // logged user's ID

      const event = await Event.query()
        .where({
          user_id: userID,
          date
        }).fetch()

      if (event.rows.length === 0) {
        return response
          .status(404)
          .send({ message: {
            error: 'No event found'
          } })
      }

      return event
    } catch (err) {
      if (err.name === 'ModelNotFoundException') {
        return response
          .status(err.status)
          .send({ message: {
            error: 'No event found'
          } })
      }
      return response.status(err.status)
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做的是，检索请求中发送的数据和登录用户的 ID。然后，我们再次使用用户的 ID 和他在请求中提供的日期手动查询事件。

现在我们需要检查在给定的日期内是否有事件发生。

如果没有事件，下面这段代码运行并返回一条消息:

```
if (event.rows.length === 0) {
    return response
        .status(404)
        .send({ message: {
            error: 'No event found'
        } })
} 
```

Enter fullscreen mode Exit fullscreen mode

如果有事件，我们简单地返回它。

访问控制器时，不要忘记创建一个路由来调用该控制器:

```
Route.get('events/list/date', 'EventController.show') 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们创建了一个在 2019 年 3 月 16 日发生的事件。如果我们在请求中发送了以下 JSON:

```
{  "date":  "2019-03-16"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们收到的回报:

```
[  {  "id":  6,  "user_id":  10,  "title":  "First event",  "location":  "Sao Paulo",  "date":  "2019-03-16T03:00:00.000Z",  "time":  "14:39:00",  "created_at":  "2019-03-16 14:40:43",  "updated_at":  "2019-03-16 14:40:43"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我们寻找 3 月 26 日的事件:

```
{  "date":  "2019-03-26"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将收到以下内容:

```
{  "message":  {  "error":  "No event found"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 删除事件

唯一缺少的功能是删除事件的能力。这很简单。像往常一样，我们将获得登录用户的 ID 和事件 ID。然后我们在数据库中寻找事件。为了确保用户只删除他自己的事件，我们将检查登录用户的 ID 是否与被删除的事件相同，然后继续删除该事件。

让我们给我们的`destroy()`方法添加一些代码:

```
async destroy ({ params, response, auth }) {
    try {
      const eventID = params.id // event's id to be deleted
      const userID = auth.user.id // logged user's ID

      // looking for the event
      const event = await Event.query()
        .where({
          id: eventID,
          user_id: userID
        }).fetch()

      /**
       * As the fetched data comes within a serializer
       * we need to convert it to JSON so we are able 
       * to work with the data retrieved
       * 
       * Also, the data will be inside an array, as we
       * may have multiple results, we need to retrieve
       * the first value of the array
       */
      const jsonEvent = event.toJSON()[0]

      // checking if event belongs to user
      if (jsonEvent['user_id'] !== userID) {
        return response
          .status(401)
          .send({ message: {
            error: 'You are not allowed to delete this event'
          } })
      }

      // deleting event
      await Event.query()
        .where({
          id: eventID,
          user_id: userID
        }).delete() 
```

Enter fullscreen mode Exit fullscreen mode

顺便提一下:当我们使用查询构建器时，我们需要‘手动’删除它，也使用查询构建器，但是在另一个例子中，如果您使用模型提供的静态方法提取数据，您只需要使用静态方法`.delete()`。

让我们测试一下我们的`destroy()`方法。在您的`start/routes.js`文件中添加以下`delete`请求:

```
Route.delete('events/:id/delete', 'EventController.destroy') 
```

Enter fullscreen mode Exit fullscreen mode

因为我们只是通过 Url 发送我们需要的所有数据，所以我们不需要在请求体中发送任何数据。

就这一次了，伙计们！

今天我们学习了如何创建一个新的模型，以及一个控制器和一个迁移文件，还学习了如何设置不同表之间的关系``