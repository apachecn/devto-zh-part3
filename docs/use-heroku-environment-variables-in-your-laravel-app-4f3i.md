# 在您的 Laravel 应用程序中使用 Heroku 环境变量

> 原文：<https://dev.to/autoidle/use-heroku-environment-variables-in-your-laravel-app-4f3i>

#### Heroku 上的 Laravel 提示 1

[![](img/2e717c7f42c43d0f9ac542f942f4744d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YssbDTQo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3z-4f6PlhJ2nv_gbyUZhzg.png)

在 Heroku 上，您可以获得 PostgreSQL、Redis 的环境变量，例如

您不应该在 Heroku 上为数据库(例如 DB_HOST、DB_PORT)和 Redis(例如 REDIS_HOST)手动添加环境变量。

因为如果你升级/降级这些 Heroku 附加组件，你必须改变这些。

而且更重要的是，Heroku 可以随时改变这些环境变量。

更好的解决方案是，在 *config/database.php* 的开头添加以下几行