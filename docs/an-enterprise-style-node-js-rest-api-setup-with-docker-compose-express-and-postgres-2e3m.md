# 带有 Docker Compose、Express 和 Postgres 的企业风格 Node.js REST API 设置

> 原文：<https://dev.to/hugo__df/an-enterprise-style-node-js-rest-api-setup-with-docker-compose-express-and-postgres-2e3m>

企业风格 Node.js 应用的原因和方式。使用像 Express.js、Postgres 和 Docker Compose 这样久经考验的技术在本地运行，这种设置易于测试和扩展。

直接潜入 GitHub 上的代码:[github.com/HugoDF/express-postgres-starter](https://github.com/HugoDF/express-postgres-starter)。

## 引导整个应用程序堆栈的单一命令

在新机器上运行要开发的应用程序只有一个先决条件: [Docker for Desktop](https://www.docker.com/products/docker-desktop) 安装并运行。

在项目的根目录下运行`docker-compose up`,以开发模式打开 Postgres 和 Express application server。

基于`docker-compose.yaml`，应用服务器被绑定到`localhost:3000`。主机上绑定的端口(即。开发机器，而不是 Docker 容器)可以通过改变`services.app.ports`的`3000:3000`中的前 3000 来重新映射。那一行中的第二个`3000`是`app`容器应该监听的端口(即我们配置 Express 应用程序监听的内容)。Express 应用程序被配置为监听环境中定义的任何`PORT`，在本例中，我们看到的是`services.app.environment`中的`PORT: 3000`。

Postgres 在主机(开发机器，而不是 Docker 容器)端口`35432`上公开。连接字符串为`postgres://user:pass@localhost:35432/db`(用户名、密码和数据库名在`docker-compose.yaml`的`services.postgres.environment`块中定义)。在内部，它可以在`postgres:5432` ( `<name-of-the-service>:<port>`)访问，因此我们将`services.app.environment.DATABASE_URL`设置为`postgres://user:pass@postgres:5432/db`。

`app`服务的启动命令是`npm start`，如 Dockerfile 中所定义的，但是`docker-compose.yml`用`npm run dev`覆盖了它`CMD`，后者使用 [nodemon](https://www.npmjs.com/package/nodemon) 运行应用程序(文件更改时自动重启)。

另一个有趣的地方是`services.app.volumes`条目。`- .:/app/`将本地目录同步到`/app`，后者是 Dockerfile 中定义的`WORKDIR`。`- /app/node_modules`确保本地`node_modules`目录(Docker 之外)不会同步到容器。它是`.:/app/`卷挂载的一个例外。

app 的`docker-compose.yaml`、`.dockerignore`和 Dockerfile 如下:

```
# docker-compose.yml
version: "3"
services:
  app:
    build: .
    depends_on:
      - postgres
    environment:
      DATABASE_URL: postgres://user:pass@postgres:5432/db
      NODE_ENV: development
      PORT: 3000
    ports:
      - "3000:3000"
    command: npm run dev
    volumes:
      - .:/app/
      - /app/node_modules

  postgres:
    image: postgres:10.4
    ports:
      - "35432:5432"
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: db 
```

Dockerfile:

```
FROM node:10

WORKDIR /app

COPY ./package.json .
COPY ./package-lock.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD npm start 
```

如上所述，`CMD`被`docker-compose`到`npm run dev`覆盖。我们将在下一节中查看不同的脚本。

### 访问应用程序

该应用程序可从 http://localhost:3000 获得。

```
curl http://localhost:3000
OK 
```

### 直接连接到 Postgres

您可以使用 psql 客户端连接到 Postgres:

```
psql postgres://user:pass@localhost:35432/db 
```

## 应用生命周期和脚本

以下是`package.json` :
中的相关字段

```
{  "name":  "express-postgres-starter",  "scripts":  {  "test":  "xo",  "lint":  "xo",  "format":  "xo --fix",  "start":  "node ./bin/start.js",  "dev":  "nodemon ./bin/start.js",  "migrate":  "node ./bin/migrate.js",  "migrate:create":  "migrate create --migrations-dir='./src/migrations'"  },  "dependencies":  {  "bcrypt":  "^3.0.6",  "client-sessions":  "^0.8.0",  "express":  "^4.16.4",  "helmet":  "^3.16.0",  "morgan":  "^1.9.1",  "pg":  "^7.9.0",  "sql-template-strings":  "^2.2.2",  "uuid":  "^3.3.2"  },  "devDependencies":  {  "nodemon":  "^1.18.11",  "xo":  "^0.24.0"  },  "xo":  {  "prettier":  true,  "space":  true  },  } 
```

### npm 启动 vs npm 开发

`npm start`运行`node ./bin/start.js`。

`start.js`脚本只包含粘合代码，从环境中读取`PORT`并用那个值调用`server.start`。

```
const Server = require('../server');

Server.start(process.env.PORT); 
```

`npm run dev`运行相同的脚本，但是使用了`nodemon ./bin/start.js`，这意味着如果 JavaScript 发生任何变化，它将重新启动。

### 棉绒设置

这个项目使用的是 [xo，即“JavaScript 快乐风格 linter”](https://github.com/xojs/xo)。它是用漂亮的空格而不是制表符来设置的。

`npm run format`将运行`xo --fix`，后者依靠更漂亮来格式化所有代码。

`npm run lint`将只运行`xo`，这是一次 lint 运行，不会覆盖任何代码。

理想情况下，还可以使用 [husky](https://github.com/typicode/husky) 和/或 [lint-staged](https://github.com/okonet/lint-staged) 在提交或推送时运行 linter/formatter。

### 数据库设置&管理

由于我们使用 Postgres 和节点 Postgres ( [pg on npm](https://www.npmjs.com/package/pg) )驱动程序(而不是 Sequelize 之类的 ORM)，我们需要建立一个系统来使我们的关系数据库的模式有序。

为此，我们使用了[节点迁移](https://github.com/tj/node-migrate)，`src/persistence/postgres-state-storage.js`带有自定义“状态存储”模块的“节点抽象迁移框架”，你可以在 GitHub 上看到 [postgres-state-storage，它是从节点迁移文档中摘录并稍作修改的。](https://github.com/HugoDF/express-postgres-starter/blob/master/src/persistence/postgres-state-storage.js)

我们还使用了一个自定义的`./bin/migrate.js` ( [参见 GitHub](https://github.com/HugoDF/express-postgres-starter/blob/master/bin/migrate.js) 上的 migrate.js)，它可以用`up`或`down`作为参数来调用。

这些都是使用 npm 脚本在`package.json`中粘合在一起的:

*   将运行迁移。
*   将回滚迁移。
*   `npm run migrate:create <migration-name>`将在`src/migrations`文件夹中创建一个新的迁移文件。

在 docker-compose 中运行迁移。它将在`app`容器中运行一个 bash 实例。

```
docker-compose run app bash 
```

其次是:

```
npm run migrate up 
```

## 快速 API 设置

Express API 位于`src/api`中。

在`src/api/index.js`、[中定义了资源的应用路径，参见 GitHub](https://github.com/HugoDF/express-postgres-starter/blob/master/src/api/index.js) 上的 src/index.js。

### 应用入口点

应用入口点是`server.js`。它处理全球性问题。

`server.js`公开一个具有`start`和`stop`功能的模块。

它用发送一个`200`状态码的`/`和`/health`定义了一个应用程序。

这包括[摩根](https://github.com/expressjs/morgan)请求记录器。[头盔](https://github.com/helmetjs/helmet)，为应用安全设置 sane 默认值)。内置在 Express ( `express.json`)中的 JSON 主体解析中间件，以及 Mozilla 的[客户端会话](https://github.com/mozilla/node-client-sessions)，用于将加密的客户端会话存储在 cookies 中。

它还装载了我们将在`src/api`文件夹中定义的 API 路由。

`server.js`看起来如下:

```
const express = require('express');

const morgan = require('morgan');
const clientSession = require('client-sessions');
const helmet = require('helmet');

const {SESSION_SECRET} = require('./config');

const app = express();
const api = require('./src/api');

app.get('/', (req, res) => res.sendStatus(200))
app.get('/health', (req, res) => res.sendStatus(200))

app.use(morgan('short'));
app.use(express.json());
app.use(
  clientSession({
    cookieName: 'session',
    secret: SESSION_SECRET,
    duration: 24 * 60 * 60 * 1000
  })
);
app.use(helmet());

app.use(api);

let server
module.exports = {
  start(port) {
    server = app.listen(port, () => {
      console.log(`App started on port ${port}`);
    });
    return app
  },
  stop() {
    server.close()
  }
} 
```

### API 架构:展现域数据分层

这个应用程序松散地遵循了[表示域数据分层](https://www.martinfowler.com/bliki/PresentationDomainDataLayering.html):

*   演示文稿在`./src/api`文件夹中处理
*   域在`./src/modules`文件夹中处理。它目前不存在，因为我们只有通用的用户和会话资源。
*   数据在`./src/persistence`文件夹中处理

## 架构示例:用户+会话管理

会话管理是通过自定义会话表、`/api/session`端点来完成的(参见[)。/src/api/session.js](//./src/api/session.js) 和利用[客户端会话](https://github.com/mozilla/node-client-sessions)。

### 呈现:一个带有 Express 的 HTTP 服务

“用户创建”动作是 HTTP 表示层的一个很好的例子。

#### 请求有效载荷验证

`./src/api/user.js`的这一部分是 HTTP 主体内容验证，这是人们可能表示表示层要做的事情之一([参见 GitHub src/API/user . js # L8-L13](https://github.com/HugoDF/express-postgres-starter/blob/master/src/api/user.js#L8-L13)上上下文中的代码)

```
const {email, password} = req.body;
if (!email || !password) {
  return res
    .status(400)
    .json({message: 'email and password must be provided'});
} 
```

#### 基于域函数输出的响应

根据域或数据层是否返回用户，展现模块会以 400(不能再次创建用户)或 200(已创建用户)进行响应([参见 GitHub src/API/user . js # L16-L20](https://github.com/HugoDF/express-postgres-starter/blob/master/src/api/user.js#L16-L20)上上下文中的代码)。

```
 if (!user) {
   return res.status(400).json({message: 'User already exists'});
 }

return res.status(200).json(user); 
```

### 域:编排

在上面的“用户创建”端点的例子中，域逻辑的唯一部分是对`User.create`的调用(这就是为什么它内嵌在处理程序中而不是一个单独的模块中):

```
const user = await User.create(email, password); 
```

#### 重构紧密耦合的表示和领域

另一个值得研究的特性是“会话创建”。

下面是端点的主体(省略了错误处理)，它从请求体中获取`email`和`password`，试图找到匹配的用户，如果用户不存在或密码不匹配，则创建一个会话，如果用户存在且密码正确，则创建 201。

```
const {email, password} = req.body;
const user = await User.find(email);
if (!user || !(await bcrypt.compare(password, user.password))) {
  return res.status(403).json({});
}

const sessionId = await Session.create(user.id);
req.session.id = sessionId;
res.status(201).json(); 
```

重写以下表示/域/数据分层的一种方法是:

```
// probably should go into ./src/modules/user.js
async function findUser(email, password) {
  const user = await User.find(email)
    if (!user || !(await bcrypt.compare(password, user.password))) {
    return null
  }
  return user
}

// in ./src/modules/session.js
function createSession(userId) {
    return Session.create(user.id);
}

// in the HTTP handler
const {email, password} = req.body;
const user = await findUser(email, password);
if (!user) {
  return res.status(403).json({});
}
req.session.id = await createSession(user.id);
res.status(201).json(); 
```

请注意，表示层不再了解数据层，它只与领域层对话。

### 数据:带`sql-template-strings`的原始 Postgres

自己编写 Postgres 查询的一个巨大缺点是允许 SQL 注入。为了减轻这种情况，我们应该使用 Postgres 预准备语句。

预准备语句的问题是它们需要一点脑力来解析，并且很容易引入一个错误(你有多少个`?`，etc 中的值是什么顺序):

```
await db.query(
  'INSERT INTO users (id, email, password) VALUES (?, ?, ?) RETURNING id, email;',
  [uuid(), email, hashedPassword]
); 
```

为了获得插值的工效学(更容易阅读)和预处理语句的好处(更小的攻击面)，我们使用了[SQL-template-strings 包](https://github.com/felixfbecker/node-sql-template-strings)。这使得上面写为:

```
const sql = require('sql-template-strings');
await db.query(sql`
  INSERT INTO users (id, email, password)
  VALUES (${uuid()}, ${email}, ${hashedPassword})
    RETURNING id, email;
`); 
```

#### 将域与数据分开

让我们来看看其中一个会话方法是如何实现的:

```
module.exports = {
  async create(userId) {
    const id = uuid();
    await db.query(sql`
    INSERT INTO sessions (id, user_id)
      VALUES (${id}, ${userId});
    `);
    return id;
  },
}; 
```

有一个论点是，uuid 的生成是一个数据库问题，因为它也是在模式级别强制执行的([参见在`src/migrations/1550969025172-authentication.js#L13-L16`](https://github.com/HugoDF/express-postgres-starter/blob/master/src/migrations/1550969025172-authentication.js#L13-L16) 的 GitHub 上创建 sessions 表的迁移)。

也可以在`src/modules/session.js` :
中实现

```
const uuid = require('uuid/v4');

const session = require('../persistence/session');

async function createSession(userId) {
  const sessionId = uuid();
  await session.create(sessionId, userId);
  return sessionId
} 
```

在`./src/persistence/session.js` :
中具有匹配的更新的`data`实现

```
module.exports = {
  async create(sessionId, userId) {
    await db.query(sql`
    INSERT INTO sessions (id, user_id)
      VALUES (${sessionId}, ${userId});
    `);
  },
}; 
```

这是企业风格 REST API 的基础，在表示/域/数据分层之后有 Node.js 和 Postgres。

杰夫·尼森