# Heroku 上的 Laravel 会话配置

> 原文：<https://dev.to/autoidle/laravel-session-configuration-on-heroku-159k>

#### Heroku 上的 Laravel 提示 6

默认情况下，Laravel 会将会话保存到磁盘上的一个目录中，这并不理想，因为 Heroku 使用一个短暂的文件系统(在[提示#5](https://dev.to/dynotower/logging-with-laravel-on-heroku-46db-temp-slug-8321570) 中解释)。

您必须使用另一个会话驱动程序。Laravel 提供了几个现成的优秀驱动程序:

*   饼干
*   数据库ˌ资料库
*   雷迪斯

在这个例子中，我们使用 Redis(就个人而言，我们总是使用 Redis，缓存和队列也是如此)。

1.  您必须在 *config/session.php* 中将默认的会话驱动程序更改为 Redis: