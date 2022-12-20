# Heroku 上的 Laravel 部署优化

> 原文：<https://dev.to/autoidle/laravel-deployment-optimization-on-heroku-1hkc>

#### Heroku 上的 Laravel 提示 4

<figure>[![](img/6ef762f80134ded2880c8f1c4fe9f309.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3V-K3lEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AAmZG-ptxLz776cOl) 

<figcaption>照片由[托德·夸肯布什](https://unsplash.com/@toddquackenbush?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

Laravel 有很棒的关于部署的文档。

最重要的部分是优化，包括 3 个部分:

1.  自动装载机优化
2.  优化配置加载
3.  优化路线装载

在 Heroku 上，我们也需要这 3 个优化。

### 自动加载器优化

我们必须用下面的命令优化 Composer 的类自动加载器:

```
composer install --optimize-autoloader --no-dev 
```

Enter fullscreen mode Exit fullscreen mode

Heroku 已经用这个命令运行了 composer install([Doc](https://devcenter.heroku.com/articles/php-support#installation-of-dependencies))。谢谢 Heroku👍

### ️Optimizing 配置加载和路线加载

我们应该跑了

```
php artisan config:cache

php artisan route:cache 
```

Enter fullscreen mode Exit fullscreen mode

用于优化配置加载和路由。

我们将在启动时调用这两个 artisan 命令，因此我们在 composer.json 中创建新的脚本条目，例如 warmup:

```
"scripts": {
 "warmup": [
 "php artisan config:cache",
 "php artisan route:cache"
 ]
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以将该脚本的调用与 Procfile 中现有的命令结合起来，例如:

```
web: composer warmup && $(composer config bin-dir)/heroku-php-apache2 web/ 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，我们 Heroku 上的 Laravel app 优化了！🚀

### 想要更多这样的提示？

你应该在推特上关注我！如果你正在使用 Heroku，你应该看看[auto idle](https://autoidle.com)——这是一种自动化的方式，可以节省你升级和审核应用的费用。