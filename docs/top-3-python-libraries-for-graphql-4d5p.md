# graphql 的三大 python 库

> 原文：<https://dev.to/robmatyszewski/top-3-python-libraries-for-graphql-4d5p>

这篇文章总结了 GraphQL 的最佳 python 库。Python 在最近几年开始登上顶级编程语言的名单。GraphQL 是一种新兴但非常有前途的查询语言和执行引擎，可以绑定到任何后端服务。

Python 是数据科学、机器学习和人工智能系统中最受欢迎的语言之一。GraphQL 是由脸书引入的，作为 REST 的替代品，它在处理复杂系统方面的灵活性很受欢迎。

# 阿里阿德涅

Ariadne 是一个 Python 库，使用模式优先的方法实现 GraphQL 服务器。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[mirumee](https://github.com/mirumee)/[Ariadne](https://github.com/mirumee/ariadne)

### Ariadne 是一个 Python 库，使用模式优先的方法实现 GraphQL 服务器。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Ariadne](img/8094459e90195aa1e2bcc0dec364cd18.png)](https://ariadnegraphql.org)

[![Documentation](img/455ea83cc14b14762c96a33db17ab4dc.png)](https://ariadnegraphql.org)[![Build Status](img/5c768ac6c1cdc6d821a224d6d01a0a68.png)](https://travis-ci.org/mirumee/ariadne)[![Codecov](img/9b6f6b324deacced64870b1449cdb28a.png)](https://codecov.io/gh/mirumee/ariadne)

* * *

# 阿里阿德涅

Ariadne 是一个 Python 库，用于实现 [GraphQL](http://graphql.github.io/) 服务器。

*   **模式优先:** Ariadne 使 Python 开发者能够使用模式优先的方法来实现 API。这是 GraphQL 社区使用的领先方法，并得到了许多前端和后端开发工具、示例和学习资源的支持。Ariadne 使您和您团队的其他成员可以立即获得所有这些信息。
*   简单: Ariadne 提供小型、一致且易于记忆的 API，让开发人员专注于业务问题，而不是样板文件。
*   **开放:** Ariadne 被设计成模块化的，开放定制。如果你缺少什么或对什么不满意，扩展或轻松地与你自己的交换。

文档可在[这里](https://ariadnegraphql.org)获得。

## 特征

*   简单，快速学习，易于记忆的 API。
*   与 GraphQL.js 版本 14.4.0 兼容。
*   查询、突变和输入类型。
*   异步解析器和查询执行。
*   订阅。
*   自定义标量、枚举和模式指令。
*   联合和接口。
*   文件上传。
*   …

</article>

[View on GitHub](https://github.com/mirumee/ariadne)

Ariadne 是一个用于实现 GraphQL 服务器的 Python 库。

*   模式优先:Ariadne 使 Python 开发人员能够使用模式优先的方法来实现 API。这是 GraphQL 社区使用的领先方法，并得到了许多前端和后端开发工具、示例和学习资源的支持。Ariadne 使您和您团队的其他成员可以立即获得所有这些信息。
*   简单:Ariadne 提供小型、一致且易于记忆的 API，让开发人员专注于业务问题，而不是样板文件。
*   开放:Ariadne 被设计成模块化和开放的定制。如果你缺少什么或对什么不满意，扩展或轻松地与你自己的交换。文档可在此处获得。

特征

*   简单，快速学习，易于记忆的 API。
*   与 GraphQL.js 版本 14.0.2 兼容。
*   查询、突变和输入类型。
*   异步解析器和查询执行。
*   订阅。
*   自定义标量和枚举。
*   联合和接口。
*   使用 SDL 字符串定义模式。
*   正在从加载架构。graphql 文件。
*   用于在现有站点中实现 GraphQL 的 WSGI 中间件。
*   选择加入 camelCase 和 snake_case 之间的自动解析程序映射。
*   内置简单的同步开发服务器，用于快速的 GraphQL 实验和 GraphQL 游乐场。
*   支持 Apollo GraphQL 扩展 Visual Studio 代码。
*   通过 gql() helper 函数进行 GraphQL 语法验证。如果安装了 Apollo GraphQL 扩展，还提供彩色化。

#### 安装

```
pip install ariadne 
```

#### 快速入门

下面的示例创建了一个 API，定义人员类型和单个查询字段 people，返回两个人的列表。它还启动了一个本地开发服务器，GraphQL Playground 位于 [http://127.0.0.1:8000](http://127.0.0.1:8000) 地址上。首先安装 uvicorn，这是一个 ASGI 服务器，我们将使用它来为 API 提供服务:

首先安装 uvicorn，这是一个 ASGI 服务器，我们将使用它来服务 API:

```
pip install uvicorn 
```

然后为您的示例应用程序创建一个 example.py 文件:

```
from ariadne import ObjectType, QueryType, gql, make_executable_schema
from ariadne.asgi import GraphQL

# Define types using Schema Definition Language (https://graphql.org/learn/schema/)
# Wrapping string in gql function provides validation and better error traceback type_defs = gql("""
    type Query {
        people: [Person!]!
    }

    type Person {
        firstName: String
        lastName: String
        age: Int
        fullName: String
    }
""")

# Map resolver functions to Query fields using QueryType query = QueryType()

# Resolvers are simple python functions @query.field("people")
def resolve_people(*_):
    return [
        {"firstName": "John", "lastName": "Doe", "age": 21},
        {"firstName": "Bob", "lastName": "Boberson", "age": 24},
    ]

# Map resolver functions to custom type fields using ObjectType person = ObjectType("Person")

@person.field("fullName")
def resolve_person_fullname(person, *_):
    return "%s %s" % (person["firstName"], person["lastName"])

# Create executable GraphQL schema schema = make_executable_schema(type_defs, [query, person])

# Create an ASGI app using the schema, running in debug mode app = GraphQL(schema, debug=True) 
```

# 草莓

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [草莓-graphql ](https://github.com/strawberry-graphql) / [草莓](https://github.com/strawberry-graphql/strawberry)

### Python 的新 GraphQL 库🍓

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/13ebc1b51782792c01555b37d7d26ea6.png)](https://github.com/strawberry-graphql/strawberry/raw/master/.github/logo.png)

# 草莓图表 SQL

> 基于数据类的 Python GraphQL 库

[![CircleCI](img/5c9fb79a38554fd5ba5aec654880d030.png)](https://circleci.com/gh/strawberry-graphql/strawberry/tree/master)

## 装置

安装方式:

```
pip install strawberry-graphql
```

## 入门指南

用以下代码创建一个名为`app.py`的文件:

```
import strawberry
@strawberry.type
class User
    name: str
    age: int
@strawberry.type
class Query:
    @strawberry.field
    def user(self, info) -> User:
        return User(name="Patrick", age=100)

schema = strawberry.Schema(query=Query)
```

这将创建一个 GraphQL 模式，定义一个`User`类型和一个将返回硬编码用户的查询字段`user`。

要运行调试服务器，请运行以下命令:

```
strawberry server app
```

点击以下链接打开调试服务器:[http://0 . 0 . 0 . 0:8000/graph QL](http://0.0.0.0:8000/graphql)

这将打开 GraphiQL，您可以在其中测试 API。

### 类型检查

Strawberry 附带了一个 [mypy](http://www.mypy-lang.org/) 插件，可以对你的 GraphQL 进行静态类型检查…

</article>

[View on GitHub](https://github.com/strawberry-graphql/strawberry)

Strawberry 是 Python 3 的一个新的 GraphQL 库，灵感来自于 dataclasses。GitHub 上发布了草莓的初始版本。草莓是由@patrick91 创建的，他也是@pyconit 的组织者。它最初是在柏林 Python 比萨期间宣布的。

> ![unknown tweet media content](img/b14c4e54aad84af33274b94314a44ee3.png)![Patrick Arminio profile image](img/3d73267b975c04764c1776e7592900c5.png)帕特里克·阿米尼奥[@ Patrick 91](https://dev.to/patrick91)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)昨天我发了第一次更新[#草莓](https://twitter.com/hashtag/Strawberry)🍓在这里公开回购:[github.com/strawberry-gra…](https://t.co/sOivYrXiZb)，还有很多工作要做，但看起来还不错！
> 
> 快讯在这里如果你好奇:[mailchi.mp/9a55232fe5a4/s…](https://t.co/2UBx77wdR0)
> 
> 我会带贴纸在[@ DjangoConEurope](https://twitter.com/DjangoConEurope):)2019 年 04 月 14:52[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1115266087342563329)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1115266087342563329)5[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1115266087342563329)19

[![](img/e2aa9bd31daf7e3bef24dc9fc64810a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVBMudK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jrow1cc45mi8n650eiba.png)

[https://strawberry.rocks/](https://strawberry.rocks/)

#### 安装

```
pip install strawberry-graphql 
```

#### 入门

用下面的代码创建一个名为 app.py 的文件:

```
import strawberry

@strawberry.type
class User:
    name: str
    age: int

@strawberry.type
class Query:
    @strawberry.field
    def user(self, info) -> User:
        return User(name="Patrick", age=100)

schema = strawberry.Schema(query=Query) 
```

这将创建一个 GraphQL 模式，定义一个用户类型和一个将返回硬编码用户的查询字段 User。

要运行调试服务器，请运行以下命令:

```
strawberry run server app 
```

点击以下链接打开调试服务器:[http://0 . 0 . 0 . 0:8000/graph QL](http://0.0.0.0:8000/graphql)

这将打开一个 GraphQL 平台，您可以在这里测试 API。

# 石墨烯

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ graphql-python ](https://github.com/graphql-python) / [石墨烯](https://github.com/graphql-python/graphene)

### Python 的 GraphQL 框架

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Graphene Logo](img/fb4dfbee3a6596c07e1e6a8c7de247d3.png) ](https://camo.githubusercontent.com/04f2156e9ca5d92073997410a3ee3f379c584e20/687474703a2f2f6772617068656e652d707974686f6e2e6f72672f66617669636f6e2e706e67) [石墨烯](http://graphene-python.org)[![Build Status](img/d630c7edf4cb8b3ecff1d95dda7ccafd.png)](https://travis-ci.org/graphql-python/graphene)[![PyPI version](img/473c998940175e315e6eb63d32711399.png)](https://badge.fury.io/py/graphene)[![Coverage Status](img/2599e68ce818e955dc735dbbbbeca8e1.png)](https://coveralls.io/github/graphql-python/graphene?branch=master)

[<g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>加入 Slack 上的社区](https://join.slack.com/t/graphenetools/shared_invite/enQtOTE2MDQ1NTg4MDM1LTA4Nzk0MGU0NGEwNzUxZGNjNDQ4ZjAwNDJjMjY0OGE1ZDgxZTg4YjM2ZTc4MjE2ZTAzZjE2ZThhZTQzZTkyMmM)

我们正在寻找贡献者！请查看[路线图](https://github.com/graphql-python/graphene/blob/master/ROADMAP.md)看看你能如何帮助 <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji>

* * *

**下面的自述文件是石墨烯`dev`(预发布)版本的文档。要查看最新稳定石墨烯版本的文档，请访问 [v2 文档](https://docs.graphene-python.org/en/stable/)T4】**

* * *

## 介绍

Graphene 是一个自以为是的 Python 库，用于快速轻松地构建 GraphQL 模式/类型。

*   **简单易用:**石墨烯帮你毫不费力地在 Python 中使用 GraphQL。
*   **中继:**石墨烯内置了对中继的支持。
*   **数据不可知:** Graphene 支持任何类型的数据源:SQL (Django，SQLAlchemy)、NoSQL、自定义 Python 对象等。我们相信，通过提供完整的 API，您可以将 Graphene 插入数据所在的任何位置，并通过 GraphQL 提供您的数据。

## 集成

石墨烯与不同的框架有多种集成:

| 综合 | 包裹 |
| --- | --- |
| 姜戈 | [石墨烯-姜戈](https://github.com/graphql-python/graphene-django/) |
| sqllcemy(SQL 语法) | [石墨烯-sqlalchemy](https://github.com/graphql-python/graphene-sqlalchemy/) |
| 谷歌应用引擎 | [石墨烯-gae](https://github.com/graphql-python/graphene-gae/) |

此外，石墨烯完全兼容…

</article>

[View on GitHub](https://github.com/graphql-python/graphene)

Graphene 是一个 Python 库，用于快速轻松地构建 GraphQL 模式/类型。

*   简单易用:Graphene 帮助你在 Python 中毫不费力地使用 GraphQL。
*   继电器:石墨烯内置继电器支持。
*   数据不可知:石墨烯支持任何类型的数据源:SQL (Django，SQLAlchemy)，NoSQL，自定义 Python 对象等。我们相信，通过提供一个完整的 API，您可以将石墨烯插入您的数据所在的任何地方，并通过 GraphQL 提供您的数据。

#### 集成

石墨烯与不同的框架有多种集成:

*   姜戈-石墨烯-姜戈
*   石墨烯化学
*   谷歌应用引擎-石墨烯-gae
*   Peewee - In progress(跟踪问题)

此外，Graphene 与 GraphQL 规范完全兼容，可以与所有 GraphQL 客户端无缝协作，如 Relay、Apollo 和 gql。

#### 安装

要安装石墨烯，只需在 shell 中运行这个命令

```
pip install "graphene>=2.0" 
```

#### 例子

```
Here is one example for you to get started:

class Query(graphene.ObjectType):
    hello = graphene.String(description='A typical hello world')

    def resolve_hello(self, info):
        return 'World'

schema = graphene.Schema(query=Query) 
```

然后查询石墨烯。模式简单如:

```
query = '''
    query SayHello {
      hello
    }
'''
result = schema.execute(query) 
```