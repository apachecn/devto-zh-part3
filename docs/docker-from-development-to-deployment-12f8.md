# 从开发到部署

> 原文：<https://dev.to/danroc/docker-from-development-to-deployment-12f8>

嗨！

几天来我一直试图解决这个问题，所以我可能最终会寻求帮助:

在我的开发 docker-compose 配置中，我有 4 个容器:

*   **前端** : Vue app 开始使用端口 80 上的`npm run serve`
*   **back**:KOA 2 server for my API(Postgraphile for graph QL)
*   **数据库** : Postgres 容器
*   **前端平衡器** : Nginx 服务器，发送`/`到**前端**，发送`/graphql`到**后端**

它在本地运行良好(尽管我可能应该跳过 Nginx 进行开发...)

现在我想部署它，这就是我头疼的地方。我最初的理解是，我不应该在生产上用`npm run serve`做**前置**，而是用 Nginx 构建我的 Vue app，服务静态文件。
但这是否意味着我在生产中不需要我的**前端**容器，可以直接将构建好的文件挂载到我的 Nginx 容器中？

或者你会怎么做？

接下来是实际部署。我仍然不知道在哪里(DO，Heroku，AWS？？)以及如何部署(CD 工作流)。但是，一旦我解决了上面的步骤，这可能会成为以后的问题！

提前感谢！