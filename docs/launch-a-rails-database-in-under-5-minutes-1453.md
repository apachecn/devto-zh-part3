# 在 5 分钟内启动一个 Rails 数据库

> 原文：<https://dev.to/robghchen/launch-a-rails-database-in-under-5-minutes-1453>

### 循序渐进教程

[![](img/c1b6e751afe3934b023e12b71d62dcdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LOEAtdKe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADrtYN01WmB_xA819CyE6GQ.jpeg)

这将是严格的指导性的，按照这个一步一步的指南来快速构建你的后端。

1)在您的终端:`rails new APP-NAME --api`

*   或者，如果您已经安装了 Postgres 并且想要使用它，请添加此标志:`rails new APP-NAME --database=postgresql —-api`
*   如果您选择使用 Postgres，记得在启动服务器时打开 Postgres。

2)在您的终端:`cd APP-NAME && open Gemfile`，然后找到并取消注释`gem 'rack-cors'`，然后将这行代码添加到下一行:`gem 'active_model_serializers'`

3)在您的终端:`bundle && open config/initializers/cors.rb`，然后取消注释下面的代码，使用 *`*`* 表示*的起源*: