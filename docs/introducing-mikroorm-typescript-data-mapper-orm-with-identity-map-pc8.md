# 引入 MikroORM，带有标识映射的类型脚本数据映射 ORM

> 原文：<https://dev.to/b4nan/introducing-mikroorm-typescript-data-mapper-orm-with-identity-map-pc8>

[![](img/2b4b9234e0719d333d2d2b79276e3f8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eSjqSIVt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4877k4Hq9dPdtmvg9hnGFA.jpeg)

## 动机

在我大学的早期，我记得我是多么迅速地爱上了面向对象编程和[对象关系映射](http://hibernate.org/orm/what-is-an-orm/)和[领域驱动设计](https://stackoverflow.com/questions/1222392/can-someone-explain-domain-driven-design-ddd-in-plain-english-please/1222488#1222488)的概念。那时，我主要是一名 PHP 程序员(*，而我们在学校做了很多 Java/Hibernate*，所以对我来说一个自然的选择是开始使用[教条](https://www.doctrine-project.org/)。

几年前我从 PHP 转到 Node.js ( *后来又转到 TypeScript* )的时候，真的很迷茫。JavaScript 世界里怎么没有类似 Hibernate 或者 Doctrine 的东西？大约一年前，我终于遇到了 [TypeORM](https://typeorm.io/) ，当我读到自述文件中的这一行时，我想我找到了我一直在寻找的东西:

> TypeORM 受其他 ORM 的影响很大，比如 [Hibernate](http://hibernate.org/orm/) 、[主义](http://www.doctrine-project.org/)和[实体框架](https://www.asp.net/entity-framework)。

[![](img/909796a8215136545355721f9d9692bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ai6fTJAt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AgWvTBke0c8BFLGR8u_5zSg.jpeg)

我立即开始玩它，但我很快就失望了。没有标识映射来跟踪所有加载的实体。没有处理事务隔离的工作单元。没有统一的 API 来引用非常奇怪的支持[只访问标识符而不填充实体](https://typeorm.io/#/relations-faq/how-to-use-relation-id-without-joining-relation)，MongoDB 驱动程序(*，我的目标是使用*)是实验性的，我有很多问题来设置它。挣扎了几天，我就远离了。

到那个时候，我开始考虑自己写点什么。 [**麦克风**](https://github.com/B4nan/mikro-orm) 就是这样开始的！

[![](img/faf2ffb04253ae4568b5728413cbac7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o0QWz3Ql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Af8phoYPnVRkwuV1wynXz_A.png)

> MikroORM 是基于数据映射器、工作单元和身份映射模式的 Node.js 的类型脚本 ORM。

目前它支持 **MongoDB** 、 **MySQL、PostgreSQL** 和 **SQLite** 数据库，但现在可以通过[定制驱动程序支持更多。它有一流的类型脚本支持，同时保持与普通 JavaScript 的向后兼容。](https://b4nan.github.io/mikro-orm/custom-driver/)

## 安装

首先通过`yarn`或`npm`安装模块，不要忘记安装数据库驱动程序。接下来你需要通过`experimentalDecorators`标志启用对`tsconfig.json`中[装饰者](https://www.typescriptlang.org/docs/handbook/decorators.html)

的支持。然后调用`MikroORM.init`作为应用程序引导的一部分。

最后一步是为每个请求提供分叉的`EntityManager`，这样它将拥有自己唯一的[身份映射](https://b4nan.github.io/mikro-orm/identity-map/)。为此，您可以使用`EntityManager.fork()`方法。另一种更友好的方式是为每个请求创建新的[请求上下文](https://b4nan.github.io/mikro-orm/identity-map/#request-context)，这将在后台使用一些[黑魔法](https://github.com/nodejs/node/blob/master/doc/api/async_hooks.md)来为你选择正确的`EntityManager`。