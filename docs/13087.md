# 使用发布阶段在您的 Laravel 应用程序中自动发布

> 原文：<https://dev.to/autoidle/use-the-release-phase-to-automate-your-release-in-your-laravel-app-1a0j>

#### Heroku 上的 Laravel 提示 2

在 Heroku 上，[发布阶段](https://devcenter.heroku.com/articles/release-phase)允许你在部署新版本的应用程序之前运行某些任务。

发布阶段对于以下任务非常有用:

*   运行数据库迁移
*   清除缓存
*   将 CSS、JS 和其他资源从应用程序的 slug 发送到 CDN 或 S3 桶

在我们的 Laravel 应用程序中，我们将运行数据库迁移并刷新应用程序缓存。

将下面一行添加到您的*过程文件*中:

```
release: php artisan migrate --force && php artisan cache:clear 
```

Enter fullscreen mode Exit fullscreen mode

使用发布阶段的主要优点是:

> 如果释放命令以非零退出状态退出，或者如果它被 dyno 管理器关闭，则释放*失败*。在这种情况下，发布是*而不是*部署到应用的 dyno 形式。如果发布阶段失败，您将收到一封电子邮件通知。

### 想要更多这样的提示？

你应该在推特上关注我！如果你正在使用 Heroku，你应该看看[auto idle](https://autoidle.com)——这是一种自动化的方式，可以节省你升级和审核应用的费用。