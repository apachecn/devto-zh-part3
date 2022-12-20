# PostgreSQL 索引人生救星

> 原文：<https://dev.to/manishhub9/postgresql-index-a-life-savior-2oe8>

[![](img/7e954fc1ac02626469ee57584cc365a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Qea3SIt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/epes6ehk5aa7bv24xqyq.jpg)

我在我的 Django 项目中使用了过去 1 年的 postgres。在我的项目开始时，我的数据库反应良好，但一段时间后，当我的项目得到一些用户，它需要越来越多的时间。然后我开始做研究来修正我的反应时间。然后我发现了 postgres 索引，我们使用 postgres 索引来最小化查询成本。在 postgres 中，成本与页数相关。如果它们的页面数量很大，那么查询成本也很高。为了说明这一点，我将举一个我在 slideshare 上发布的简单例子。

[https://www . slide share . net/manishyadav 143341/postgre-SQL-index](https://www.slideshare.net/manishyadav143341/postgre-sql-index)