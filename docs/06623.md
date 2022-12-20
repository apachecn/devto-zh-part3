# Laravel —包含您自己的助手函数

> 原文：<https://dev.to/dennis_smink/laravel-include-your-own-helper-functions-47jo>

### Laravel —包含您自己的助手函数

有时你可能想创建一个随处可用的函数，这就是本教程派上用场的地方👏

[![](img/84e3c7607611a9489c80886e4d494e6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ru_6Ue5U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ani4h_wQgCo3YukuH1PzNqg.jpeg)

我到处都可以看到教程，它们解释说你可以通过添加一个自动加载文件到你的 composer 文件中来很容易地实现这一点。出于某种原因，我认为它很难看，可以得到不可读的，因为你的 helpers.php 可能会在这种情况下增长。

我已经使用一个方法很长时间了，在这里你可以声明几个包含方法的文件，这样更干净，更易读。

让我们开始吧..🔥

首先，创建一个 HelperServiceProvider.php 提供者:

```
php artisan make:provider HelperServiceProvider 
```

一旦你这样做了，你会看到一个名为 HelperServiceProvider.php 的新文件

您可以安全地删除完整的 boot()方法，因为我们不打算使用它。

在寄存器函数中输入这段代码:

```
public function register()
{
    foreach (glob(app_path('Helpers') . '/*.php') as $file) {
        require_once $file;
    }
} 
```

这是在 app/Helpers 中的所有文件中循环，你可能已经猜到了:你现在可以在那个目录中输入几个 PHP 文件(你可能需要创建这个目录),这些文件将被加载到你的应用程序中。这些助手函数在代码的每个部分(视图、模型、控制器等等)都是可用的

我们仍然需要加载这个提供者，打开 config/app.php 并在 AppServiceProvider 之上添加 HelperServiceProvider】

```
...
App\Providers\HelperServiceProvider::class,
App\Providers\AppServiceProvider::class,
App\Providers\AuthServiceProvider::class,
App\Providers\BroadcastServiceProvider::class,
... 
```

现在让我们试着创建一个简单的函数，在 app/Helpers 文件夹中创建一个名为 Carbon.php 的新文件，内容如下:

```
<?php

/**
 * Carbon helper
 *
 * @param $time
 * @param $tz
 *
 * @return Carbon\Carbon
 */
function carbon($time = null, $tz = null)
{
    return new \Carbon\Carbon($time, $tz);
} 
```

您不需要输入任何命名空间。如果你愿意，可以通过调用 if 语句 function_exists 来检查函数是否存在，这可能是个好主意。

现在，您可以在应用程序的任何地方使用辅助工具 carbon()。现在假设你需要另一个返回特定格式的函数(只是为了本教程的用例)，你可以在同一个文件(Carbon.php):

```
<?php

/**
 * Carbon helper
 *
 * @param $time
 * @param $tz
 *
 * @return Carbon\Carbon
 */
function carbon($time = null, $tz = null)
{
    return new \Carbon\Carbon($time, $tz);
}
function carbonFormatted($time = null, $tz = null)
{
    return carbon($time, $tz)->format('Y-m-d')
} 
```

就是这样！您现在可以开始用您自己的 PHP 文件填充 app/Helpers 目录，这些文件包含您经常使用的快速助手💪