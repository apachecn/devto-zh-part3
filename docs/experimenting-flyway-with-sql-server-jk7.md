# 使用 SQL Server 试验 Flyway

> 原文：<https://dev.to/praneetnadkar/experimenting-flyway-with-sql-server-jk7>

最近，在我和同事的茶话会上，Flyway 出现了。我很想尝试一下。根据他们的官方网站，Flyway 是你的数据库的版本控制，有很多很棒的特性。

# 为什么要飞来飞去？

实验之前最重要的问题之一是为什么？嗯，我的观点是，对于数据库来说，版本控制非常重要。在许多情况下，技术人员开发自己的内部版本控制或工具来管理模式和脚本。底线是版本控制确实很重要。

# 如何？

嗯，要尝试 Flyway，我可以总结为 5 个简单快捷的步骤:

1.  从[这里](https://flywaydb.org/download/#downloads)下载 Flyway zip 文件夹并解压到你的机器上。
2.  复制这个文件夹路径，并将其添加到您的环境变量中。如果您不确定如何添加环境变量的路径，请在此处查看[和](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/)
3.  在解压缩的文件夹中，会有一个 conf 文件夹。在它下面，用数据库连接更新 flyway.conf。
4.  将数据库迁移脚本放在 sql 文件夹下。该文件夹将出现在提取的飞行路径文件夹下。注意:请参考此[链接](https://flywaydb.org/documentation/migrations#naming)了解飞行路线的命名约定。
5.  打开命令提示符，运行`flyway migrate`。这将运行您的迁移。如果你得到错误`ERROR: Found non-empty schema(s) [dbo] without schema history table! Use baseline() or set baselineOnMigrate to true to initialize the schema history table.`在这种情况下，运行这个:`flyway migrate -baselineOnMigrate=true`。这个命令将在您的数据库中为 Flyway 创建一个基线模式版本表和数据

就是这样！

这将运行脚本并更新数据库中的版本。

Flyway 支持许多数据库，值得一试。

还有更多的东西可以用它来做实验。