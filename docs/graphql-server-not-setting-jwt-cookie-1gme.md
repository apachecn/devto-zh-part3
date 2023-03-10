# GraphQL 服务器未设置 JWT Cookie

> 原文：<https://dev.to/doylecodes/graphql-server-not-setting-jwt-cookie-1gme>

## 解！

下面是这个原帖
中问题的解决方案

[![doylecodes image](img/002cdee0566e4615dfc5506844df12f9.png)](/doylecodes) [## 阿波罗客户端和阿波罗服务器中的 CORS

### 瑞安道尔 1919 年 5 月 1 日 3 分钟阅读

#apollo #tutorial #react #express](/doylecodes/cors-in-apollo-client-apollo-server-3cbj)

## 原帖

聪明人好！我希望有人能帮我解决一件困扰我很久的事情。

我参加过一个 WesBos 课程(Advanced React ),非常棒，它使用保存为 cookies 的 JWT 进行身份验证。我一直在做自己的项目，并决定做同样的事情。最初，我使用的是 Apollo Server 2，我不知道为什么我的 cookies 没有保存，所以我想，“嘿，我是用 GraphQL Yoga 服务器做的，所以我就这样做吧。”在用 apollo-server-express 服务器做了一些尝试之后)。

长话短说，我还是卡住了。我想我可以问一下 StackOverflow，但是那边不太好！她是我的一切...

### index.js

```
const { Prisma } = require('prisma-binding');
const express = require('express');
const { ApolloServer } = require('apollo-server-express');
const { GraphQLServer } = require('graphql-yoga');
const { importSchema } = require('graphql-import');
const cookieParser = require('cookie-parser');
const jwt = require('jsonwebtoken');
require('dotenv').config();

const typeDefs = importSchema('./src/schema.graphql');
const Query = require('./src/resolvers/Query');
const Mutation = require('./src/resolvers/Mutation');

const db = new Prisma({
  typeDefs: './generated/prisma.graphql',
  endpoint: process.env.DB_ENDPOINT,
  secret: process.env.DB_SECRET
});

const server = new GraphQLServer({
  typeDefs,
  resolvers: {
    Mutation,
    Query
  },
  context: req => ({ ...req, db })
});

server.express.use(cookieParser());
server.express.use((req, res, next) => {
  const { token } = req.cookies;
  if (token) {
    const { userId } = jwt.verify(token, process.env.APP_SECRET);
    // add the user to future requests
    req.userId = userId;
  }
  next();
});

server.start({ //start the GraphQL server
  cors: { // only allow from frontend server (frontend_url)
    credentials: true,
    port: 4000,
    origin: ['http://localhost:3000'],
  },
}, postStart => { //callback once connection is created
  console.log(`🚀 Server now running on http://localhost:${postStart.port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 我的登录突变

```
async signin(parent, { email, password }, ctx, info) {
    const user = await ctx.db.query.user({ where: { email: email } });
    if (!user) {
      throw new Error(`No such user found for the email ${email}`);
    }
    const valid = await bcrypt.compare(password, user.password);
    if (!valid) {
      throw new Error(`password is not valid!`);
    }
    const token = jwt.sign({ userId: user.id }, process.env.USER_SECRET);

    ctx.response.cookie('token', token, {
      httpOnly: true,
      maxAge: 1000 * 60 * 60 * 24 * 31,
    });
    console.log(ctx.response);
    return user;
  }, 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以看到，我在我的变异中设置了一个 console.log 来记录响应。我可以看到有一个实际的响应，它说:

```
 body:
      { operationName: 'LOGIN_MUTATION',
        variables: [Object],
        query:
         'mutation LOGIN_MUTATION($email: String!, $password: String!) {\n  signin(email: $email, password: $password) {\n    id\n    name\n    email\n    __typename\n  }\n}\n' },
     _body: true,
     length: undefined },
  locals: [Object: null prototype] {},

  [Symbol(outHeadersKey)]:
   [Object: null prototype] {
     'x-powered-by': [ 'X-Powered-By', 'Express' ],
     'access-control-allow-origin': [ 'Access-Control-Allow-Origin', 'http://localhost:3000' ],
     vary: [ 'Vary', 'Origin' ],
     'access-control-allow-credentials': [ 'Access-Control-Allow-Credentials', 'true' ],
     'set-cookie':
      [ 'Set-Cookie',   'token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJjanRreG5kZG9henlpMGIwMG8yamwwZzg1IiwiaWF0IjoxNTU0MDc0MDU1fQ.h617eZ1LV3yUqn01jpMaTZDTUlYQmRMxwZc1VKbSHns; Max-Age=2678400; Path=/; Expires=Wed, 01 May 2019 23:14:15 GMT; HttpOnly' ] } } 
```

Enter fullscreen mode Exit fullscreen mode

所以我在最后看到了这个 Set-Cookie，但是不管我怎么尝试，没有 Cookie 被保存:(

我甚至试着复制我在课程中所做的事情(尽管软件包略有不同)以避免运气不佳。

聪明的人们，我错过了什么？我感觉我想在知道发生了什么之后写一篇关于这个的帖子...

**更新:**

*   我知道我的 env 变量工作正常，我在应用程序中有其他工作正常的变化。如果我从我的。环境文件。
*   我认为这与 CORS 有关，因为服务器和客户端在不同的本地主机上。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [瑞安姆道尔](https://github.com/ryanmdoyle) / [热鱼学家](https://github.com/ryanmdoyle/thermoFisherSci)

### 零件库存描述跟踪者/编辑者/导出者。允许以多种语言输入和退出零件描述，以导出为 HTML，用于 web 应用程序。

<article class="markdown-body entry-content container-lg" itemprop="text">

# ThermoFisher 科学部分描述

零件库存描述跟踪者/编辑者/导出者。允许以多种语言输入和退出零件描述，以导出为 HTML，用于 web 应用程序。

您可以通过以下方式启动应用程序:

1.  从前端和后端文件夹安装 npm。
2.  您需要创建一个 prisma 帐户，并从后端部署到 Prisma。
3.  使用以下命令在后端创建一个. env 文件:

*   DB_ENDPOINT(设置为您的 prisma 端点)
*   DB_SECRET(随机生成一个秘密供 prisma 使用)
*   USER_SECRET(供应用程序使用的随机密码)
*   COOKIE_SECRET(用于 COOKIE 生成的随机内容)
*   FRONTEND_URL=http:本地主机:3000
*   APP_SECRET(供应用程序使用的随机内容)

4.  制造。前端环境

*   节点环境=开发

5.  npm 从前端(localhost: 3000)和后端(localhost:4000)运行 dev
6.  参见 locahost 的应用程序:3000

</article>

[View on GitHub](https://github.com/ryanmdoyle/thermoFisherSci)