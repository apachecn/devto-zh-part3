# Python - Strawberry 的 GraphQL 库

> 原文：<https://dev.to/robmatyszewski/graphql-library-for-python-strawberry-1jmc>

我最近偶然发现了一个有趣的 GraphQL 库。Strawberry 是 Python 3 的一个新的 GraphQL 库，灵感来自于 dataclasses。GitHub 上发布了草莓的初始版本。要跟踪这个项目，你可以在草莓网站或 github 上注册获取更新。以下链接:

[![](img/e2aa9bd31daf7e3bef24dc9fc64810a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVBMudK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jrow1cc45mi8n650eiba.png)

[https://strawberry.rocks/](https://strawberry.rocks/)

草莓是由@patrick91 创作的，他也是@pyconit 的组织者。它最初是在柏林 Python 比萨期间宣布的。

> ![unknown tweet media content](img/b14c4e54aad84af33274b94314a44ee3.png)![Patrick Arminio profile image](img/3d73267b975c04764c1776e7592900c5.png)帕特里克·阿米尼奥[@ Patrick 91](https://dev.to/patrick91)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)昨天我发了第一次更新[#草莓](https://twitter.com/hashtag/Strawberry)🍓在这里公开回购:[github.com/strawberry-gra…](https://t.co/sOivYrXiZb)，还有很多工作要做，但看起来还不错！
> 
> 快讯在这里如果你好奇:[mailchi.mp/9a55232fe5a4/s…](https://t.co/2UBx77wdR0)
> 
> 我会带贴纸在[@ DjangoConEurope](https://twitter.com/DjangoConEurope):)2019 年 04 月 14:52[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1115266087342563329)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1115266087342563329)5[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1115266087342563329)19

# PyPi 发布和演示

PyPI 上的 Alpha 版本:[https://pypi.org/project/strawberry-graphql](https://pypi.org/project/strawberry-graphql)
使用 Starlette 在 Heroku 上演示:[https://demo.strawberry.rocks/graphql](https://demo.strawberry.rocks/graphql)

# 安装

```
pip install strawberry-graphql 
```

入门指南

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