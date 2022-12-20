# 阿波罗客户端和阿波罗服务器中的 CORS

> 原文：<https://dev.to/doylecodes/cors-in-apollo-client-apollo-server-3cbj>

## 这是后续

好吧，不久前我问了一个问题，花了几天时间试图解决这个问题。我希望这能给试图解决问题的人指明正确的方向。作为参考，这是原帖。

[![doylecodes image](img/002cdee0566e4615dfc5506844df12f9.png)](/doylecodes) [## GraphQL 服务器未设置 JWT Cookie

### Ryan Doyle 3 月 31 日 193 分钟阅读

#help #express #react #apollo](/doylecodes/graphql-server-not-setting-jwt-cookie-1gme)

## 我的问题

所以，我的问题是我设置了一个 Apollo 服务器，后端托管在/backend 目录下，前端托管在/frontend 的一个单独的目录下。我的前端运行在 localhost:3000 上，后端运行在 localhost:4000 上。当我试图通过请求传递 cookies 时，我遇到了 CORS 的各种问题，我不知道发生了什么。我也很难弄清楚如何通过请求传递某些东西(比如用户数据)。

我的问题是，我试图传递特定的 cors 选项，但我不知道 Apollo 服务器有默认的 cors 设置。这些设置与我所需要的有冲突

## 解

我的解决方案最终分为两部分:

1.  使用包`apollo-server-express`和 cors 中间件。
2.  禁用 Apollo 服务器 cors 以避免冲突。

```
const express = require('express');
const { Prisma } = require('prisma-binding');
const { ApolloServer } = require('apollo-server-express');
const { importSchema } = require('graphql-import');
const cookieParser = require('cookie-parser');
const jwt = require('jsonwebtoken');
const cors = require('cors');
require('dotenv').config();

const typeDefs = importSchema('./src/schema.graphql');
const Query = require('./src/resolvers/Query');
const Mutation = require('./src/resolvers/Mutation');

const db = new Prisma({
  typeDefs: './generated/prisma.graphql',
  endpoint: process.env.DB_ENDPOINT,
  secret: process.env.DB_SECRET
});

const server = new ApolloServer({
  typeDefs,
  resolvers: {
    Mutation,
    Query
  },
  context: req => ({ ...req, db }),
});

const app = express();

var corsOptions = {
  origin: process.env.FRONTEND_URL,
  credentials: true // <-- REQUIRED backend setting
};

app.use(cors(corsOptions));
app.use(cookieParser());
app.use((req, res, next) => { // checks for user in cookies and adds userId to the requests
  const { token } = req.cookies;
  if (token) {
    const { userId } = jwt.verify(token, process.env.USER_SECRET);
    req.userId = userId;
  }
  next();
})
app.use(async (req, res, next) => {
  if (!req.userId) return next();
  const user = await db.query.user(
    { where: { id: req.userId } },
    '{id, permissions, email, name}' //the graphql query to pass to the user query
  );
  next();
})

server.applyMiddleware({
  app,
  path: '/',
  cors: false, // disables the apollo-server-express cors to allow the cors middleware use
})

app.listen({ port: 4000 }, () =>
  console.log(`🚀 Server ready at http://localhost:4000${server.graphqlPath}`)
); 
```

Enter fullscreen mode Exit fullscreen mode

这是我的服务器/应用程序设置，现在一切都运行良好！以下是要点以及为什么我最终会这样实现它。

*   **使用`apollo-server-express`而不是`apollo-server` :** 这基本上允许你创建一个快速应用并将其传递给服务器，而不是使用 Apollo Server 自己创建的普通应用。它还允许您创建中间件，就像您创建基于通用 express 的应用程序和服务器一样！
*   **app . use(cors(cors options)):**对于更细粒度的 CORS 选项(我希望服务器只监听 localhost:3000 ),您可以使用 CORS 中间件和选项来监听您想要的源，以及通过请求传递凭证的选项。
*   **server.applyMiddleware:** 就是在这里，你向 Apollo 服务器传递 express 应用程序，以及其他重要的部分，**关闭默认的 cors 选项**。尽管您关闭了默认选项，但是您在传递给服务器的 express 应用程序中使用了 cors 中间件，所以所有的事情都在那里处理。