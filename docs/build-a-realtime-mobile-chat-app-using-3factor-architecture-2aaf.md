# 使用 3 因素架构构建实时移动聊天应用程序

> 原文：<https://dev.to/hasurahq/build-a-realtime-mobile-chat-app-using-3factor-architecture-2aaf>

[![](img/e401d4c4f1be3f1f2ff85e11e668813e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lPqp11CJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/christian-wiediger-606395-unsplash.jpg)

*本教程由* [*乌马尔·阿卜杜拉希*](https://twitter.com/codebyomar) *撰写，并作为 [Hasura 技术作者计划](https://blog.hasura.io/the-hasura-technical-writer-program/)的一部分发布，该计划旨在支持为开源 Hasura GraphQL 引擎编写指南和教程的作者。*

## 什么是 3 因子架构？

[3 因素架构](https://3factor.app/)设计模式利用实时通信、事件和无服务器计算，让您快速高效地构建全栈应用。架构模式由三个因素组成，很明显:)，它们是实时 GraphQL、可靠事件(例如 Hasura 事件触发器)和异步无服务。

构建可伸缩和高特性速度的应用程序听起来不错，但是当直接处理服务器和紧密耦合的组件时，实现可伸缩性和高特性速度可能相当具有挑战性。有了 3 因素架构，GraphQL 和 serverless 允许您通过分别在前端改善开发人员体验和抽象开发操作来非常快速地发布新特性。无服务器也意味着自动扩展。

## 先决条件

在我们继续之前。我们将使用 [Hasura GraphQL 引擎](https://hasura.io/)、Hasura 事件触发器和 Google cloud 函数来满足 3factor 架构的三个因素。需要预先了解 GraphQL、Go 和 React native。因为我们的云函数会用 Go 编写，而我们的移动应用程序会用 React native 和 Expo 编写。

## 造型

Hasura GraphQL 引擎会根据 Postgres 中的表和视图自动生成 GraphQL 模式和解析器。因此，我们必须为一个最小的聊天应用程序建模必要的表和视图。

### 用户

我们需要一个用户表来存储我们的用户。

```
user (
  id SERIAL PRIMARY KEY
  username TEXT UNIQUE
  push_token TEXT
  last_seen timestamp with time zone
  last_typed timestamp with time zone
  date_created timestamp with time zone DEFAULT now() NOT NULL
) 
```

要创建一个用户，我们简单的聊天应用程序只需要一个用户名，没有密码和电子邮件地址。last_seen 和 last_typed 字段将用于实现诸如键入指示符和用户在线状态之类的功能。

### 房间

为了能够对讨论进行分类，我们需要在一个房间表中存储相似主题的消息。

```
room (
    id SERIAL NOT NULL PRIMARY KEY
    name TEXT NOT NULL
    room_type TEXT NOT NULL
) 
```

默认情况下，所有房间都是公共的，即任何用户都可以加入，在本例中，我们不需要像 is_private 这样的字段来标识私人房间。

### 消息

用户发送的消息将存储在消息表中。

```
message (
id SERIAL NOT NULL PRIMARY KEY
"text" TEXT NOT NULL
username INT FOREIGN KEY REFERENCES user(username) NOT NULL
room_id INT FOREIGN KEY REFERENCES room(id) NOT NULL
"timestamp" timestamp with time zone DEFAULT now() NOT NULL
) 
```

room_id 和 username 将分别用于标识哪个用户发送了该消息以及它属于哪个房间。

### 用户 _ 房间

我们正在创建一个 user_rooms 表，以便能够获得用户所属房间的列表。

```
user_rooms (
    id SERIAL NOT NULL PRIMARY KEY
    user_id INT FOREIGN KEY REFERENCES user(username) NOT NULL
    room_id INT FOREIGN KEY REFERENCES room(id) NOT NULL
) 
```

每一行代表一个用户和一个加入的房间。通过使用 delete_user_rooms 变异，我们可以很容易地将用户从房间中删除。

**注意:**要获取所有在线用户，我们需要一个 online_users 视图。将提供一个 SQL 脚本来创建所有必要的表和视图。

## 设置 Hasura 和 Postgres

运行 Hasura GraphQL 引擎需要访问 Postgres 数据库，幸运的是，使用 [Heroku free tier](https://docs.hasura.io/1.0/graphql/manual/getting-started/heroku-simple.html) 您可以在几分钟内用 Postgres 数据库设置 Hasura GraphQL 引擎。或者，您可以使用 Docker 运行 Hasura GraphQL 引擎和 Postgres。

### 在 Heroku 部署 Hasura 和 Postgres

下面是如何在 Heroku 上设置 Hasura GraphQL 引擎和 Postgres 数据库；

1.  点击[此链接](https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku)部署到 Heroku。如果您没有 Heroku 帐户，您需要创建一个。注册后，你会被自动重定向到 Heroku 的应用创建页面。
2.  选择应用名称和地区。默认情况下会提供一个免费的 Heroku Postgres 插件。然后单击部署。
3.  部署完成后，单击查看。或者前往**https://<YOUR _ HEROKU _ APP _ NAME>. HEROKU APP . com**，应该会看到哈苏拉控制台。

### 在 Postgres 上应用模式

在 Hasura 控制台上，您会注意到没有 GraphQL 查询，这是因为我们的 Postgres 数据库中没有表或视图。我们在上面创建的模型已经被组合成下面的单个 SQL 脚本；