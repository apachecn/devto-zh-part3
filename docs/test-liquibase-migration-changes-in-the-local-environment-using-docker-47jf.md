# 使用 Docker 在本地环境中测试 Liquibase 迁移变化

> 原文：<https://dev.to/rohithmenon89/test-liquibase-migration-changes-in-the-local-environment-using-docker-47jf>

我发表这篇文章是因为最近发现我们在一些 liquibase 迁移问题的预发布环境中遇到了问题。

然后我在想，这可能是因为没有在我们的本地环境中正确地进行测试，或者不知道如何进行测试。

不幸的是，有些命令使用了 java-maven，但是我确信这些语句有替代方法。

也许下面的内容可以帮助到某人:)

# 先决条件:

*   码头工人

# 要做的步骤:

*   Docker-compose 文件内容可以从这里复制-[https://hub.docker.com/_/postgres](https://hub.docker.com/_/postgres)
*   `docker-compose up` -运行一个容器，里面运行 Postgress DB
*   `docker ps` -列出集装箱的详细信息
*   从预发布或测试环境中导出架构。
*   `docker exec -it {replace_with_container_id} bash` -您可以登录到容器并导入导出的模式。
*   从您的本地，打开`changelog.xml`并注释掉除您之外的更改。
*   将 pom.xml 中的 liquibase 配置更改为本地 postgres 配置。
*   运行命令`mvn liquibase:update`查看您的更改是否被应用。

# 给 postgres 新手的小贴士:

一旦您使用待办事项中提到的步骤 5 登录到 docker 容器，

*   执行命令`psql -U postgres`--“postgres”是 docker-compose 内容中使用的用户名。

*   \ c {数据库名称} -连接到数据库

*   \dt -显示数据库中表

*   \ d {表名} -描述表

谢谢你的阅读，如果我能帮助别人，我会很高兴。