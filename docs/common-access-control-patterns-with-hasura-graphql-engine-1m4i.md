# Hasura GraphQL 引擎的常见访问控制模式

> 原文：<https://dev.to/hasurahq/common-access-control-patterns-with-hasura-graphql-engine-1m4i>

## 简介

在这篇文章中，我们将会看到一些访问控制模式，这些模式可以和 [Hasura](https://github.com/hasura/graphql-engine) 一起使用来精确地允许/限制数据。这是一篇来自 [Hasura Streams](https://twitch.tv/hasurahq) 的摘要博文，视频已经上传到 [Youtube](https://www.youtube.com/hasurahq) 。如果您喜欢观看，本视频中也有类似的例子:

[![video link](img/fbb3126d6b61bc811eeae99e82ac58fa.png)](https://www.youtube.com/watch?v=wrBOKlVG69A)

## Hasura 图形 QL 引擎

> Hasura GraphQL 引擎是一个瘦 GraphQL 服务器，它位于任何 Postgres 数据库上，允许您使用实时 GraphQL 和访问控制来 CRUD 数据

这篇文章假设你对 Hasura 和关系数据模型有基本的了解。如果你以前从未使用过 Hasura，请查看本指南。

Hasura 支持基于角色的访问控制，可以与大多数授权提供者集成。Hasura 中的访问控制规则是会话变量的函数。会话变量是 x-hasura-*变量，如 x-hasura-role、x-hasura-user-id，可以从 GraphQL 请求的请求头中解码。您可以使用任意数量的会话变量来使规则更加细化。

[![auth flow](img/aa0687ca719ae2486e62ff6fc05b1876.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PRBCH2Kf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/05/auth-high-level-overview1.png)

让我们看看如何将访问控制规则设置为这些会话变量的函数。Hasura 从 Postgres 模式推断出 GraphQL 模式，这意味着在表、列及其关系上设置访问控制规则对应于在 GraphQL 模式的字段上设置访问控制规则。

## 权限

### 用户界面

Hasura 控制台有一个简洁的用户界面来设置权限。它还有一个过滤器生成器，可以让构建过滤器和检查变得非常有趣。

[![perm-ui](img/d18c91e3115a35d7aca84a0c1182b748.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Iwr-sOer--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/05/insert_perm_example.png)

### 角色

对于向它发出的每个 GraphQL 请求，Hasura 都会检查会话变量`x-hasura-role`中客户端用户的角色。看看这个角色，Hasura 寻找这个角色的权限，并相应地允许或限制 CRUD。

> Hasura 中的角色与 Postgres 角色和用户无关。这些角色是在 Hasura 层实现的

您可以在 Hasura 控制台中定义这些角色，并且对定义多个角色没有限制。

### 插入权限

在表中插入行的权限由三部分组成:

*   **检查约束**:这是一个布尔值，由会话变量、被插入行的字段值和所有逻辑运算符构建而成。例如，要插入到用户表中，您需要设置一个类似于`{ "id": { "_eq": "x-hasura-user-id" } }`的条件。该条件规定，每当向用户表中插入一行时，只有当该行的`id`等于会话变量中的`x-hasura-user-id`值时，才能插入该行。
*   **列**:您可以限制只插入特定的列(其余的为空、默认或从`column presets`中推断出来)
*   **列预置**:列预置是在插入字段时，您可以设置分配给字段的值。例如，当一个条目被插入到用户表中时，我们可以从`x-hasura-user-id`会话变量中获取`id`。这有助于避免在客户端解析会话信息。

### 选择权限

*   **Filter** :这是一个由会话变量和所选字段值构建的布尔值。例如，在文章表中，您希望允许所有用户查询所有已发布的文章，但只能查询他们自己未发布的文章。要实现这一点，您需要添加一个过滤器，如下所示:

```
{  "or":  [  {  "author_id":  {  "_eq":  "x-hasura-user-id"  }  },  {  "is_published":  true  }  ]  } 
```

*   **列**:有时您希望一些角色不能访问表中的特定列。在这种情况下，您可以显式选择允许和限制哪些列。

### 更新权限

*   **Filter** :这是一个布尔值，由会话变量和正在更新的字段值构建而成。例如，在一个`articles`表格中，您希望用户只修改他们自己的文章。为此，您的更新过滤器应该是这样的:

```
{  "author_id":  {  "_eq":  "x-hasura-user-id"  }  } 
```

*   **列**:可以限制哪些列可以更新。当您不希望更新`created_at`字段，但是您可能希望更新 articles 表中的`title`字段时，这很方便。

*   **列预置**:和 insert 一样，如果你想自动更新某些字段的值，而不在请求中明确提及。

### 删除权限

*   **Filter** :和 update 一样，delete permisson 上的 Filter 是一个布尔条件，需要满足该条件才能删除行。

## 例子

有了以上的想法，让我们看看一些具体的案例，以及它们是如何被建模的。首先，让我们为类似于 [HackerNews](https://news.ycombinator.com) 的应用程序使用一个基础模式。这是 postgres 模式:

[![schemaspy-schema](img/912278d3c9a2fb1ecc97a55ad65fbe8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SDLEdNPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/05/acl-schemaspy.png)

让我向您展示一个样本 GraphQL 查询，以获取所有文章及其作者、评论、向上投票和向下投票。

```
query  {  articles  {  id  title  content  content_type  author  {  id  name  }  comments  {  body  id  author  {  id  name  }  }  article_upvotes_aggregate  {  aggregate  {  count  }  }  article_downvotes_aggregate  {  aggregate  {  count  }  }  }  } 
```

现在，让我们尝试用上面的模式针对一些特定的访问控制用例。

### 强制用户以自己的身份插入文章

说我是`id` = `123`的用户。这意味着我的会话信息中的`x-hasura-user-id`的值将是`123`。现在，当我在`articles`表中插入一篇文章时，您会希望`author_id`是`123`，这意味着您会希望我以作者的身份插入文章。要实现这一点，您可以采取两种方法:

*   **通过检查约束**:在`articles`表的插入权限中，可以像`{ "author_id": { "_eq": "x-hasura-user-id" } }`一样设置检查约束。这将确保只有当插入有效负载中的`author_id`与会话变量中的`x-hasura-user-id`匹配时，文章才会被插入。

*   **通过列预置**:在`articles`表的插入权限中，可以禁止插入`author_id`列，设置列预置，使`author_id`自动取自`x-hasura-user-id`会话变量。通过这种方式，`author_id`将被适当地插入，而不需要客户明确地提及它。

您可以使用完全相同的方法来限制用户更新不是由他们发布的`articles`。

### 多重角色

假设你有两个角色:`user`和`moderator`。一个`moderator`应该被允许无条件地更新每个帖子的`title`，而一个用户应该被允许只为自己的帖子更新`title`。所以更新权限为:

*   `moderator`:应允许更新的`title`和`content`，无需任何过滤器。
*   `user`:应该允许用过滤器`{ "author_id": { "_eq": "x-hasura-user-id" } }`更新`title`和`content`。

此外，`moderator`应该被允许删除每一篇文章，而用户应该被允许只删除自己的文章。所以删除权限为:

*   `moderator`:应允许删除，无需检查。
*   `user`:应该允许用过滤器`{ "author_id": { "_eq": "x-hasura-user-id" } }`删除

### 通过视图进行访问控制

在一个典型的新闻讨论应用中，一篇文章的反对票或赞成票必须是匿名的。这意味着用户不能从`article_downvotes`和`article_upvotes`表中获得所有信息。为了实现匿名，您可以创建一个只有`article_id`和投票数的视图。对于`article_downvotes`，视图看起来像这样:

```
CREATE VIEW article_downvote_count as
SELECT article_id,
  count(*) AS downvotes
FROM article_downvotes
GROUP BY article_id; 
```

现在，一旦创建了这个视图，您就可以在这个视图上设置一个 select 权限，这样就可以在没有任何检查的情况下选择它。您也可以为`article_upvotes`表实现类似的限制。

### 基于因果报应的否决

在像 [HackerNews](https://news.ycombinator.com) 这样的网站，只有当你达到特定的因果报应(比如 500)时，你才能获得否决特权。因此，在像`article_downvotes`这样的表中，在`articles`和`users`之间存在多对多关系，您的插入权限应该是:

```
{  "and":  [  {  "user"  :  {  "id":  {  "_eq":  "x-hasura-user-id"  }  }  },  {  "user":  {  "karma":  {  "_gte":  500  }  }  }  ]  } 
```

上述插入权限确保:

*   用户以自己的身份插入条目(`author_id`等于`x-hasura-user-id`)
*   只有当插入用户的因果报应大于或等于 500 时，插入才会成功。

### 强制字段只有特定的值

您可以使用 Hasura 权限模拟类似 enum 的行为。例如，在`articles`表中，`content_type`字段应该是`"url"`或`"text"`。任何其他值都是无效的。因此，对`articles`表的插入权限的检查条件如下:

```
{  "_and":  [  {  "user"  :  {  "id":  {  "_eq":  "x-hasura-user-id"  }  }  },  {  "content_type":  {  "_in":  ["text",  "url"]  }  }  ]  } 
```

该权限确保:

*   用户只能以自己的身份插入。(`author_id`等于`x-hasura-user-id`)
*   在插入的文章中，`content_type`字段必须是`"text"`或`"url"`。

* * *

同样，您可以为不同的角色添加不同的复杂访问控制规则，并保护您的数据。如果您有任何问题，请在评论中告诉我们，或者加入我们的不和谐频道，我们在那里非常活跃。另外，如果你需要更多的例子，请在评论中告诉我们。

## 引用

*   [文档](https://docs.hasura.io/1.0/graphql/manual/auth/index.html)
*   [图形 SQL 引擎](https://github.com/hasura/graphql-engine/)
*   [视频](https://www.youtube.com/watch?v=wrBOKlVG69A)