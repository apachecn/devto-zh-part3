# 在 Heroku 上运行 Laravel 计划作业

> 原文：<https://dev.to/autoidle/run-laravel-scheduled-jobs-on-heroku-2ah6>

#### Heroku 上的 Laravel 提示 3

[![](img/ea67aa151c3302d84ef608142f4ca1cc.png)](https://cdn-images-1.medium.com/max/1024/0*FkjnO3CTtwSUseXT) 

<figcaption>照片由[索尼娅·兰福德](https://unsplash.com/@sonjalangford?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

Heroku 有免费的 Heroku 调度程序，但是这有两个缺点:

*   它仅每 10 分钟、每小时或每天运行一次
*   你必须在 UI 中添加你的命令

Laravel 附带了一个惊人的任务调度器。

要在 Heroku 上运行 Laravel 计划作业，您必须添加一个新命令，并将其作为一个进程运行。

创建命令*app/Console/Commands/scheduler Damon . PHP*:

您可以使用以下内容生成该文件:

```
php artisan make:command SchedulerDaemon 
```