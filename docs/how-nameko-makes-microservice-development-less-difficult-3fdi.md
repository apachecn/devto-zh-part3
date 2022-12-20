# Nameko 如何降低微服务开发难度？

> 原文：<https://dev.to/rev0kz/how-nameko-makes-microservice-development-less-difficult-3fdi>

在我继续介绍 python 框架 Nameko 如何进行微服务开发之前，请记住微服务只是通过构建彼此独立的服务或组件来开发软件的另一种方式。

假设我们已经决定用以下组件构建一个公共汽车售票系统:

*   注册
*   登录(重置)
*   电子邮件
*   演员表
*   预订
*   通知
*   确认

在单一模式中，软件工程师团队将使用服务器端语言(如 Ruby、Golang、Node.js 或 Python)为所有组件开发以下功能。

这些组件都将驻留在由负载平衡器(如 Nginx)支持的 web 服务器的每个实例上，以防止 DDOS 攻击。

但是，在微服务模式中，结构有点不同。这些组件可以用不同的服务器端语言或框架来开发。

Nameko 作为一个微服务框架，使得软件开发者在依赖注入的帮助下轻松实现服务成为可能。

Nameko 的依赖注入允许软件开发人员将数据库、缓存系统等外部实体的代码与服务代码分开。

例如，`register`服务和`login`服务可以从`mongodb`或`postgres`数据库中调用`insert()`函数或`query()`函数。

还要记住，`register`服务和`billing`服务可能都有一个单独的`postgres`数据库来存储用户的详细信息。

这就是 nameko 如何使微服务开发对软件工程师来说非常灵活，而不需要后端代码的集群服务。