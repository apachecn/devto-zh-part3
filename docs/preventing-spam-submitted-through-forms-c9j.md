# ★防止通过表单提交的垃圾邮件

> 原文：<https://dev.to/freekmurze/preventing-spam-submitted-through-forms-c9j>

当向公共网站添加表单时，存在垃圾邮件机器人试图用假值提交表单的风险。我们最近发布了一个名为 [laravel-honeypot](https://github.com/spatie/laravel-honeypot) 的新包，可以检测这些垃圾请求。

## 蜜罐如何工作

大多数垃圾邮件机器人都很笨。您可以通过在表单中添加一个在提交时不应包含值的不可见字段来阻止大多数错误。这样的领域被称为蜜罐。这些垃圾邮件机器人将填充所有领域，包括蜜罐。当一个提交带有一个填充的蜜罐字段时，我们的包将丢弃这个请求。

## 使用包

使用它很容易。首先，您必须将`@honeypot` blade 指令添加到您想要保护的任何表单中。

```
<form method="POST" action="{{ action(App\Http\Controllers\ContactFormSubmissionController::class, 'create') }}")>
    @honeypot
    <input name="myField" type="text">
</form> 
```

`@honeypot`将增加两个字段:`my_name`和`my_time`(可以在配置文件中更改名称)。

接下来，您必须在处理表单提交的路由中使用`Spatie\Honeypot\ProtectAgainstSpam`中间件。这个中间件将拦截任何为名为`my_name`的键提交非空值的请求。

大多数人需要一点时间来填写表格。Blade 指令添加的另一个字段`my_time`用于检测表单提交的速度是否超过一秒。

```
use App\Http\Controllers\ContactFormSubmissionController;
use Spatie\Honeypot\ProtectAgainstSpam;

Route::post([ContactFormSubmissionController::class, 'create'])->middleware(ProtectAgainstSpam::class); 
```

如果您的应用程序有许多由不同控制器处理的表单，您可以选择将其注册为全局中间件。

```
// inside app\Http\Kernel.php

protected $middleware = [
   // ...
   \Spatie\Honeypot\ProtectAgainstSpam::class,
]; 
```

## 在关闭

蜜罐是抵御垃圾邮件的第一道防线。在我的项目中，它可以防止大多数垃圾邮件的提交。虽然蜜罐很容易骗过大多数机器人，但也有更聪明的机器人，过一会儿就能发现蜜罐领域。在这种情况下，谷歌 Recaptcha T1 或使用像 T2 akis met T3 这样的服务可能是很好的第二道防线。

如果你喜欢 [laravel-honeypot](https://github.com/spatie/laravel-honeypot) ，一定要看看[团队](https://spatie.be/open-source)Spatie 之前发布的其他包。