# Laravel Modding:用单数表名生成模型

> 原文：<https://dev.to/slyfirefox/laravel-modding-generating-models-with-singular-table-names-11h9>

[![](img/f24a1a25d5072875f8a38e937c3b8054.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--arTnMCIY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxTQXZ2JN4Hd6Edoq) 

<figcaption>照片由[吉姆·昆泽尔](https://unsplash.com/@jimquenzer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

我见过的开发人员最常见的习惯之一是坚持他们在学校或大学早期学到的惯例。一个很好的例子是数据库表的单数名词(想想“团队”而不是“团队”)。这是经常教授的东西，但在现实中，你在申请时选择什么并没有什么不同。然而，有时它确实会给 Laravel 带来问题。Artisan 的 make model 命令(在进行迁移时)和模型本身默认期望表名是复数。

重要的是表名保持一致，如果不仅仅是让事物遵循一个固定的模式看起来更好，这也意味着开发人员在构建手动查询时不必记住哪些表用单数命名，哪些表用复数命名。最终，当您开始使用单个表名(或者从遗留应用程序中迁移数据库)时，这将更难维护，但是您总是忘记确保所有的模型和迁移都匹配，如果像许多人一样，您使用 make model 命令来生成新模型等。

#### 如何对抗复数表名

用一个简单的命令替换 Laravel 框架中的命令，实际上很容易解决这个问题。我们可以通过从 artisan 控制台快速生成一个新命令来做到这一点:

```
php artisan make:command ModelMakeCommand 
```

这样我们就可以编辑文件 app/Console/Commands/modelmakecommand . PHP。首先，我们最好清除所有内容，因为我们不需要句柄或构造方法。我们也不需要默认命令的签名和描述属性。

接下来要做的事情是改变我们新的 ModelMakeCommand 的继承。我们需要扩展 Illuminate \ Foundation \ Console \ ModelMakeCommand，而不是扩展 Illuminate\Console\Command。为此，我个人喜欢在 use 语句中设置和别名，所以我们称它为 BaseCommand。我们的新命令应该如下所示。