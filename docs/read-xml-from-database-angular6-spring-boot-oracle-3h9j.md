# 从数据库读取 XML(angular 6，spring boot，oracle)

> 原文：<https://dev.to/grmkris/read-xml-from-database-angular6-spring-boot-oracle-3h9j>

当用户在我的 angular 应用程序中按下按钮时，服务器应该返回给他一个 xml 文件，这个文件是我在服务器上生成的。

我有一个有效的 sql 查询，它生成一个包含 xml 的单元格。
我有工作的 spring boot 控制器，它从前端获取请求，然后调用 db 过程。它还从数据库中获取 xml。(我使用 varhcar2 作为返回数据类型)。我现在的问题是如何将 xml 作为文件返回给用户

提前表示感谢:)