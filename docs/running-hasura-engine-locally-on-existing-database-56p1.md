# 在现有数据库上本地运行 Hasura 引擎

> 原文：<https://dev.to/hasurahq/running-hasura-engine-locally-on-existing-database-56p1>

在这个视频中，我们将从在 Heroku 中运行 Hasura 引擎转移到使用 Docker 在本地运行它，同时仍然连接到部署到 Heroku 的 Postgres 数据库。我们将看到，我们在前面步骤中定义的所有内容都保持不变，并将演示引擎如何在现有数据库以及新数据库上运行。从这一步你可以理解，只要你能在 Docker 容器中运行引擎，基本上你就能在任何支持 Docker 的云中的任何环境中运行它。