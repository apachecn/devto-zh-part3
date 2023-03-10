# 实施图表服务

> 原文：<https://dev.to/sergiodxa/implementando-un-servidor-de-graphql-3gg4>

在前面的文章中，我们看到了一个[GraphQL](https://sergiodxa.com/essays/introduccion-a-graphql)的介绍，现在我们来看一下如何组装简单的 graph QL 服务器。在此示例中，我们将使用 JavaScript(与 Node.js 一起使用)，但重要的是要了解，我们可以使用任何后端技术来创建 GraphQL 服务器。

> public ado origination en[https://sdx . im/articles/implementando-un-servidor-de-graph QL](https://sdx.im/articles/implementando-un-servidor-de-graphql)

## 启动项目和依存关系

与任何 JavaScript 项目一样，我们将启动该项目并安装依赖项。

```
npm init --yes
# o con yarn
yarn init --yes 
```

Enter fullscreen mode Exit fullscreen mode

项目开始后，我们安装了这些设备。

```
npm i body-parser compression cors express graphql graphql-server-express graphql-subscriptions graphql-tools morgan pg sequelize subscriptions-transport-ws uuid
npm i -D nodemon
# o con yarn
yarn add body-parser compression cors express graphql graphql-server-express graphql-subscriptions graphql-tools morgan pg sequelize subscriptions-transport-ws uuid
yarn add -D nodemon 
```

Enter fullscreen mode Exit fullscreen mode

我们会列出所有的单位，并解释每个单位所做的事情:

*   `body-parser`=>express 中间件便于阅读邮件请求正文
*   `compression`=>express 中间件用 GZIP 压缩我们的响应
*   `cors` = >用于处理颜色的直通中间件
*   `express` = >书店创建 HTTP 服务器并处理路径
*   `graphql`=>JavaScript 中图形 SQL 的实现
*   `graphql-server-express` = >图表的快速联系
*   `graphql-subscriptions` = >书店为实时事物激活图形 SQL 订阅
*   `graphql-tools` = >帮助我们创建更容易的图 QL 服务器的工具
*   `morgan`=>express 中间件以在控制台上保存我们的请求日志
*   `pg`=>PostgreSQL 驱动程序用作数据库
*   `sequelize` = >形式的数据库
*   `subscriptions-transport-ws` = >为使我们的订阅通过 websockets 运行而提供的书店
*   `uuid` = >书店生成唯一 ID
*   `nodemon` = >将为我们运行我们正在开发的应用程序提供服务

如您所见，我们的应用程序将使用 express for http server，并将 PG 用作数据库。

## 基德达托斯

我们将创建数据库连接和我们的模型，我们的应用程序将是一切，所以我们将只有一个模型，所以我们将把一切都存放在一个文件中，我们称之为“`db.js`”。

```
// importamos sequelize
const Sequelize = require("sequelize");

// definimos en constantes nuestras variables de entorno con los datos de conexión de la base de datos
const DB_USER = process.env.DB_USER;
const DB_PASS = process.env.DB_PASS;
const DB_HOST = process.env.DB_HOST;
const DB_NAME = process.env.DB_NAME;
const DB_PORT = process.env.DB_PORT || 5432;

// creamos una nueva conexión de Sequelize
const sequelize = new Sequelize(DB_NAME, DB_USER, DB_PASS, {
  host: DB_HOST,
  dialect: "postgres",
  pool: {
    max: 5,
    min: 0,
    idle: 10000
  }
});

// definimos nuestro modelo Todo que va a tener 3 campos
// un campo ID que va a ser un UUID
// un campo content que va a ser un string
// un campo status que puede ser `active`, `completed` y `deleted`
const Todo = sequelize.define(
  "todo",
  {
    id: {
      type: Sequelize.UUID,
      primaryKey: true,
      unique: true
    },
    content: {
      type: Sequelize.STRING
    },
    status: {
      type: Sequelize.ENUM,
      values: ["active", "completed", "deleted"]
    }
  },
  {
    indexes: [
      {
        unique: true,
        fields: ["id"]
      }
    ]
  }
);

// exportamos nuestra conexión a la base de datos y nuestro modelo
module.exports = {
  db: sequelize,
  Todo
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们与数据库和模型的连接。他们还必须有 PG 数据库可供连接，可以在本地安装 PG(或使用 docker)，也可以使用外部服务，如“[elephantql](https://www.elephantsql.com/)”提供 PostgreSQL 数据库作为服务。

### 定义数据方案

一旦我们有了数据库，我们将定义我们的 GQL 方案。客户将如何与我们的 API 交互。为此，我们创建了一个包含以下内容的文件:

```
// exportamos un template literal con nuestro esquema, esto podría estar dividido en varias partes
// y podríamos luego combinarlos, por simplicidad vamos a usar solo un archivo con todo el esquema
module.exports = `
  # Una tarea pendiente
  type Todo {
    # El ID único de nuestro TODO
    id: String!
    # El contenido de nuestro TODO
    content: String!
    # El estado actual de nuestro TODO
    status: String!
  }

  # Nuestra query principal que define la forma de consumir datos
  type Query {
    # Obtener un único TODO mediante el ID
    todo(id: String!): Todo
    # Obtener una lista de todos los TODO
    allTodos: [Todo]
    # Obtener una lista de los TODO activos
    activeTodos: [Todo]
    # Obtener una lista de los TODO completados
    completedTodos: [Todo]
  }

  # Nuestras mutaciones que definen como interactuar con los datos
  type Mutation {
    # Crear un nuevo TODO pasando el contenido
    createTodo(content: String!): Todo
    # Borrar un TODO existente mediante el ID
    deleteTodo(id: String!): Todo
    # Marcar como completo un TODO existente mediante el ID
    completeTodo(id: String!): Todo
  }

  # Nuestras suscripciones que definen a que datos suscribirse
  type Subscription {
    # Suscribirse a los nuevos TODOs creados
    todoCreated(status: String!): Todo
    # Suscribirse a las actualizaciones de un TODO mediante el ID
    todoUpdated(id: String!): Todo
  }

  # Nuestro esquema principal que define la query, mutation y subscription
  type Schema {
    query: Query
    mutation: Mutation
    subscription: Subscription
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的 GQL 计划。正如我们所看到的，我们将使用我们的 API 中的欲望、突变和订阅来刷新每个:

*   **查询**:向我们 API 请求数据的方式。
*   **突变**:用于创建、修改或删除数据的交互方式与函数相似。
*   **订阅**:订阅 API 变更并在变更时实时得知的方式

## 定义解析器

现在我们来定义一下我们的求解器。基本上，这些功能可确保在客户执行查询时，要求提供查询所需的数据。他们还必须负责创建或修改我们的数据，并对我们的期望作出响应。

```
// importamos uuid para crear nuestros ID únicos
const uuid = require("uuid/v4");
// nos traemos nuestro modelo Todo
const { Todo } = require("./db");
// imporatmos el módulo pubsub usado para suscripciones (luego lo creamos)
const pubsub = require("./pubsub");

// este objeto contiene todos nuestros resolvers
const resolvers = {
  // acá definimos como resolver cada query de nuestro esquema
  Query: {
    // nuestra query de obtener todos los TODOs
    allTodos() {
      // devolvemos todos los TODOs usando nuestro modelo
      return Todo.findAll();
    },
    // nuestra query de obtener solo los TODOs activos
    activeTodos() {
      // buscamos los TODO donde el estado es `active`
      return Todo.findAll({ where: { status: "active" } });
    },
    // nuestra query para obtener solo los TODOs completados
    completedTodos() {
      // buscamos los TODO donde el estado es `completed`
      return Todo.findAll({ where: { status: "completed" } });
    },
    // nuestra query para obtener un único ID
    todo(_, { id }) {
      // el segundo parámetro que recibimos es un objeto con los parámetros
      // que pasamos a nuestra query, en este caso `id`
      // luego obtenemos un único TODO usando el ID que recibimos
      return Todo.findById(id);
    }
  },
  // acá definimos como resolver cada mutación de nuestro esquema
  Mutation: {
    // la mutación para crear un nuevo todo
    async createTodo(_, { content }) {
      // creamos un nuevo TODO usando `uudi` para generar el ID y definiendo status como `active`
      const todo = await Todo.create({ id: uuid(), content, status: "active" });
      // enviamos el TODO a nuestro PubSub en el canal `todoCreated`
      pubsub.publish("todoCreated", todo);
      // devolvemos el TODO que creamos
      return todo;
    },
    // la mutación para borrar un TODO
    async deleteTodo(_, { id }) {
      // actualizamos el estado a `deleted` en el TODO con el ID que recibimos
      await Todo.update({ status: "deleted" }, { where: { id } });
      // obtenemos el TODO que creamos (el ORM no nos devuelve el TODO al hacer update)
      const todo = await Todo.findById(id);
      // enviamos ese TODO a nuestro PubSub en el canal `todoUpdated`
      pubsub.publish("todoUpdated", todo);
      // devolvemos el TODO que actualizamos
      return todo;
    },
    // la mutación para completar un TODO
    async completeTodo(_, { id }) {
      // actualizamos el estado a `completed` en el TODO con el ID que recibimos
      await Todo.update({ status: "completed" }, { where: { id } });
      // obtenemos el TODO que creamos (el ORM no nos devuelve el TODO al hacer update)
      const todo = await Todo.findById(id);
      // enviamos ese TODO a nuestro PubSub en el canal `todoUpdated`
      pubsub.publish("todoUpdated", todo);
      // devolvemos el TODO que actualizamos
      return todo;
    }
  },
  // acá definimos como resolver cada suscripción de nuestro esquema
  Subscription: {
    // cuando se crea un TODO recibimos ese TODO y lo enviamos a los clientes
    todoCreated(todo) {
      return todo;
    },
    // cuando se actualiza un TODO recibimos ese TODO y lo enviamos a los clientes
    todoUpdated(todo) {
      return todo;
    }
  }
};

module.exports = resolvers; 
```

Enter fullscreen mode Exit fullscreen mode

这些是 gql API 的解析器。正如我们所看到的，这些功能都相当简单，我们还没有实现的一部分是“`./pubsub.js`”模块，该模块适用于我们的订阅，这就是我们将要创建的内容。

## 创建 PubSub

本模块是订购的重要组成部分。它允许我们拥有发送消息的渠道，这些渠道称为我们在 GQL 架构中定义的订阅。

在现实世界的项目中，我们应该使用 Redis 或“[rabbitq](https://www.rabbitmq.com/)”这样的工具，这样我们就可以水平扩展应用程序(添加更多服务器实例)，而不必担心如果客户端连接到实例 1，则不会知道实例 2 中发生的变化。

对于我们的例子，我们将使用`graphql-subscriptions`给我们提供一个内存中 PubSub 系统(仅用于一个实例)。

```
const { PubSub } = require("graphql-subscriptions");
module.exports = new PubSub(); 
```

Enter fullscreen mode Exit fullscreen mode

极其简单，我们从我们的模块中导入 PubSub，对其进行实例化和导出。然后，正如我们在解析器中看到的那样，我们使用`pubsub.publish`从突变中发送信息。

## 创建服务器

现在是时候将所有这些结合起来，为我们的 gql API 创建 HTTP 服务器了。

```
// importamos la función de crear un servidor del módulo nativo HTTP
const { createServer } = require("http");
// importamos express
const express = require("express");
// imporatmos los middlewares body-parser, cors, compression y morgan
const bodyParser = require("body-parser");
const cors = require("cors");
const compression = require("compression");
const morgan = require("morgan");
// imporatmos nuestro middleware para combinar express con GraphQL y GraphiQL para tener el IDE
const { graphqlExpress, graphiqlExpress } = require("graphql-server-express");
// importamos una de las herramientas que nos provee `graphql-tools`, ya vamos a ver que hace
const { makeExecutableSchema } = require("graphql-tools");
// importamos el manejador de suscripciones de `graphql-subscriptions`
const { SubscriptionManager } = require("graphql-subscriptions");
// importamos el servidor de suscripciones que funciona mediante WS
// también hay opciones con socket.io por ejemplo
const { SubscriptionServer } = require("subscriptions-transport-ws");

// imporatmos nuestro modelo
const { Todo } = require("./db");
// nuestro cliente de Pubsub
const pubsub = require("./pubsub");
// nuestro esquema
const typeDefs = require("./schema");
// nuestros resolvers
const resolvers = require("./resolvers");

// definimos en constantes nuestras variables de entorno
const PORT = process.env.PORT || 3000;
const HOST = process.env.HOST || "localhost";
const NODE_ENV = process.env.NODE_ENV || "development";

// creamos una función asíncrona autoejecutable para poder usar Async/Await
(async () => {
  try {
    // intentamos sincronizar nuestro modelo con la BD
    // si estamos en desarollo forzamos el sincronizado
    // borrando los datos viejos
    await Todo.sync({ force: NODE_ENV !== "production" });
  } catch (error) {
    // si ocurre un error mostramos el error y matamos el proceso
    console.log(error);
    process.exit(0);
  }

  // creamos una aplicación de express y un servidor HTTP apartir de esta
  const app = express();
  const server = createServer(app);

  // usamos 3 los middlewares que importamos
  app.use(cors());
  app.use(compression());
  app.use(morgan("common"));

  // combinamos nuestro esquema (`typeDefs`) y nuestros resolvers para crear un schema ejecutable
  const schema = makeExecutableSchema({ typeDefs, resolvers });

  // creamos nuestro administrador de suscripciones usando nuestro esquema ejecutable
  // y nuestro módulo de PubSub y definimos como manejar cada suscripción
  const subscriptionManager = new SubscriptionManager({
    schema,
    pubsub,
    setupFunctions: {
      // cuando alguien se suscribe a `todoUpdated` solo mandamos las del ID al que se suscribe
      todoUpdated(options, args) {
        return {
          todoUpdated: {
            filter: todo => todo.id === args.id
          }
        };
      },
      // cuando alguien se suscribe a `todoCreated` solo enviamos las del status
      // al que el cliente se suscribe
      todoCreated(options, args) {
        return {
          todoCreated: {
            filter: todo => todo.status === args.status
          }
        };
      }
    }
  });

  // definimos la URL `/graphql` que usa los middlewares `body-parser` y el `graphqlExpress`
  // usando el esquema ejecutable que creamos
  app.use("/graphql", bodyParser.json(), graphqlExpress({ schema }));

  // si no estamos en producción
  if (NODE_ENV !== "production") {
    // usamos el middleware `graphiqlExpress` para crear la URL `/ide` donde cargamos GraphiQL
    // este IDE va a consumir datos de la URL `/graphql` que creamos antes y `/subscriptions`
    app.use(
      "/ide",
      graphiqlExpress({
        endpointURL: "/graphql",
        subscriptionsEndpoint: `ws://${HOST}:${PORT}/subscriptions`
      })
    );
  }

  // iniciamos el servidor en el puerto y host que obtuvimos por variables de entorno
  server.listen(PORT, HOST, error => {
    // creamos el servidor de suscripciones usando el administrador de suscripciones
    // combinando el servidor HTTTP y definiendo la ruta `/subscriptions`
    new SubscriptionServer(
      { subscriptionManager },
      { server, path: "/subscriptions" }
    );
    // luego mostramos un simple log indicando la URL donde corre el servidor
    console.log("> Server running on http://%s:%d", HOST, PORT);
  });
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的服务器，我们可以看到它是一个很大的设置和初始化。好消息是，一旦我们装配了这一点，将更多功能添加到我们的 API 中，只需定义更多架构和解析器，并且准备好了，此文件就几乎永远不会被触摸。

## 脚本 del package.json

现在，我们将配置我们的“`package.json`”脚本，以运行我们的开发和生产应用程序。

```
{  ...  "scripts":  {  "dev":  "NODE_ENV=development nodemon server.js",  "start":  "node server.js"  }  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用以下开发命令启动该项目:

```
npm run dev
# o con yarn
yarn dev 
```

Enter fullscreen mode Exit fullscreen mode

并在生产中使用:

```
npm start
# o con yarn
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

## 环境变量

当我们尝试运行服务器时，它会给我们错误，因为我们没有设置环境变量。为此，我们可以在运行`nodemon`之前，在脚本`dev`中设置变量，可以创建包含环境变量的`.env`文件，使用`dotenv`模块，或者使用具有`env`属性的`now.json`文件，然后使用

既然我们接下来要部署一个【v1】现在就要使用 now-env，所以我们用:
安装它

```
npm i now-env
# o con yarn
yarn add now-env 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创造了我们的`now.json` :

```
{  "env":  {  "NODE_ENV":  "production",  "HOST":  "localhost",  "PORT":  3000,  "DB_USER":  "@db_user",  "DB_PASS":  "@db_pass",  "DB_HOST":  "@db_host",  "DB_NAME":  "@db_name",  "DB_PORT":  "@db_port"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们创建了一个文件`now-secrets.json`，我们将在存储库中忽略该文件，在其中放置数据库的秘密值，类似于:

```
{  "@db_user":  "user",  "@db_pass":  "pass",  "@db_host":  "host",  "@db_name":  "name",  "@db_port":  "port"  } 
```

Enter fullscreen mode Exit fullscreen mode

无论您是使用 elephantsql 或其他服务在本地安装数据库，这些都必须是正确的。最后，让我们转到我们的 server.js 代码并添加以下行:

```
require("now-env"); 
```

Enter fullscreen mode Exit fullscreen mode

在程式码的开头，我们已经设定了环境变数

## 跑申请

现在可以了，如果我们使用之前定义的脚本运行应用程序，一切都会顺利运行。

进入`localhost:3000/ide`我们将看到一个连接到我们 API 的 QL 图形，在那里我们可以通过进行请求、突变或订阅来测试我们的 gql API，我们还可以看到我们 API 的文档，这些文档是通过我们的架构中的注释自动生成的。

## 部署生产

最后，为了部署到生产环境中，我们必须使用`now secrets`为数据库设置我们的生产秘密，然后进行部署。定义这些秘密是一个简单的命令。

```
now secret add db_user my-db-user
now secret add db_pass my-db-pass
now secret add db_host my-db-host
now secret add db_name my-db-name
now secret add db_port my-db-port 
```

Enter fullscreen mode Exit fullscreen mode

您必须将访问数据放在生产数据库的位置。然后我们就开始部署。让我们先修改我们的`now.json`以添加我们的应用程序名称和要使用的别名。

```
{  "name":  "platzi-now-api",  "alias":  "platzi-now-api.now.sh",  "env":  {  "NODE_ENV":  "production",  "HOST":  "localhost",  "PORT":  3000,  "DB_USER":  "@db_user",  "DB_PASS":  "@db_pass",  "DB_HOST":  "@db_host",  "DB_NAME":  "@db_name",  "DB_PORT":  "@db_port"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们运行命令进行部署。

```
now 
```

Enter fullscreen mode Exit fullscreen mode

使用这个简单的命令，我们已经在生产环境中使用了一个 URL，并为其分配了一个别名，以便与世界共享。

```
now alias 
```

Enter fullscreen mode Exit fullscreen mode

而现在我们已经有了“`platzi-now-api.now.sh`”我们的应用程序将在其中运行，并且可以由 GrahpQL(如“[Apollo](https://dev.apollodata.com/))的客户通过简单的许愿、突变或订阅来消费。

## 结论

需要执行的步骤似乎很多，但如果您查看这些步骤相当简单，并且服务器安装完毕后添加功能只需添加更多架构及其各自的解析器，就可以了。

所以很酷的是**使用 GraphQL 前端可以很容易地创建新的功能**，只需做不同的要求，后端可以通过添加更多的架构来扩展 API，并思考如何扩展和优化我们的应用程序，使其不会快速崩溃和运行。