# 无头 GraphQL CMS

> 原文：<https://dev.to/stereobooster/headless-graphql-cms-51id>

## 我在找什么？

CMS -网站的管理部分，用于编辑内容(CRUD 操作)。这个任务解决了很多次，我不想再做一次，我想使用一些现有的东西，一些开箱即用的东西。

**Headless** CMS -意思是 CMS 不提供网站的前端部分，它只提供后端编辑。例如，Wordpress 在默认情况下是**而不是** headless，它也生成网站。

GraphQL 有很多方面，但我对它作为 API 规范感兴趣。可以从 GraphQL 规范中生成类型签名或者生成验证器(比如 io-ts ),这样就可以创建“端到端类型检查”体验。从这个角度来看，OpenAPI 作为规范也是可行的。

**开源** -我希望能够自己部署它，例如，在内部网应用中使用它。

**关系数据库**支持-我希望能够备份我的内容或在不同的应用程序中使用它。关系数据库是一个很好理解的抽象概念。它易于备份，复制，用 SQL 客户端检查，用[超集](https://github.com/apache/incubator-superset)可视化，为其建立 ER 图。例如， [ponzu](https://github.com/ponzu-cms/ponzu) (牛逼项目)但是它使用自己的内部数据库，这让我无法使用。

**S3** 资产(可选)——如果 CMS 可以上传资产到 S3 桶(或类似服务)就好了，这样我就不用担心存储文件、备份和迁移。

对我来说不是优先事项(但对其他人来说可能是优先事项):

**访问控制**——我不需要粒度权限。

**高负载**支持或速率限制- API 将用于生成静态网站，所以我不关心高负载。

**突变**——我不需要通过 API 编写或修改数据。

## 选项

**CMS 在构建时考虑到了这一使用案例**，并提供了开箱即用的所有功能，例如 strapi、prime 等。

**没有 GraphQL 的无头 CMS**+一些把 API 转换成 GraphQL 的代理，比如 Ghost + Gatsby 的 Ghost 插件。

**没有 GraphQL** + Postgraphile 或 Hasura 的无头 CMS。

**框架生成无头 CMS** + Postgraphile 或 Hasura，例如 Rails 框架。

## 我发现了什么

我做了一个小的研究，发现了一些选择，但将感谢额外的提示。我用 docker-compose 做实验。所有中间结果记录在[报告](https://github.com/stereobooster/postgresql-experiment)中。

### 斯特拉皮

网址:[https://strapi.io/](https://strapi.io/)
技术:ECMAScript、MongoDB 或 MySQL 或 MariaDB 或 PostgreSQL

一个真正有效的方法(我在开始其他项目时遇到了问题)。我只有一个问题:为什么它需要在每次模式或插件安装改变后重启，感觉他们在那里做了一些黑客攻击。

### 质数

网址:[https://docs.primecms.app/](https://docs.primecms.app/)
技术:TypeScript，PostgreSQL

看起来很酷，但我无法启动它。这个项目很年轻——只有一个核心维护者。作者在做很酷的事情，但是文档仍然滞后，初次用户体验可能会更好。

### 驾驶舱

网址:[https://getcockpit.com/](https://getcockpit.com/)
技术:PHP、ECMAScript、SQLite 或 MongoDB、Apache

它似乎没有得到积极的维护。我不知道如何安装 GraphQL 插件。

### WordPress 为无头 CMS

网站:[https://wordpress.org/](https://wordpress.org/)
技术:PHP，ECMAScript，MySQL

来自 postlight 的人发表了关于这个选项的文章。我不喜欢这个解决方案——WordPress 不是为这个用例构建的。有可能添加 2-3 个插件来使它工作，但它感觉黑客和 UX 是笨拙的。

### 幽灵一样的 CMS

网址:[https://ghost.org/](https://ghost.org/)
技术:ECMAScript，MySQL

不提供 GraphQL，但是可以使用 [Gatsby development server 作为 GraphQL 代理](https://github.com/TryGhost/gatsby-starter-ghost)。不支持内容的自定义类型。

### Directus 为 CMS

网站:[https://directus.io/](https://directus.io/)
技术:PHP，ECMAScript，Vue，MySQL

不提供 GraphQL API。

### 基于 Rails 的管理部分

技术:Ruby on Rails，关系数据库

有很多自动或半自动生成用于 Rails 的 CMS 的解决方案，例如:

*   [管理](https://github.com/thoughtbot/administrate)
*   [戈德明](https://github.com/varvet/godmin)
*   [activeadmin](https://github.com/activeadmin/activeadmin)
*   [rails_admin](https://github.com/sferik/rails_admin)
*   [栈桥](https://github.com/TrestleAdmin/trestle)

但是在 GraphQL API 生成方面就没那么好了(至少我不知道这个)。所以诀窍就是对 CMS 用 Rails，对 GraphQL API 用别的，比如 postgraphile。

### PostgreSQL(基于 PostgreSQL 的 API)

网址:[https://www.graphile.org/postgraphile/](https://www.graphile.org/postgraphile/)

> PostGraphile 自动检测表、列、索引、关系、视图、类型、函数、注释等——提供了一个对数据高度智能的 GraphQL 服务器，当您修改数据库时，它会自动更新自己而无需重新启动

可以与任何使用 PostgreSQL 的 CMS 一起使用。我也尝试过和 [react-admin](https://github.com/marmelab/react-admin) 一起使用，但是这个尝试失败了。

[尝试用 CockroachDB 代替 PostgreSQL，但是没有运气](https://github.com/stereobooster/cockroachdb-experiment)。

### Hasura(基于 PostgreSQL 的 API)

网址:[https://hasura.io/](https://hasura.io/)

> Postgres 上的即时实时图表

令人印象深刻。有内置 CMS(我猜挺原始的，不过这总比没有强)。

可以与任何使用 PostgreSQL 的 CMS 一起使用。

## 帮助

我错过了什么？有我不知道的支持 GraphQL 或 OpenAPI 的好的 CMSes 吗？有好的数据库 API 提供者吗？

> 米卡在 Unsplash 上拍摄的照片