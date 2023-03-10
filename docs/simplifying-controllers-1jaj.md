# ★简化控制器

> 原文：<https://dev.to/freekmurze/simplifying-controllers-1jaj>

在这篇博文中，我想强调两个技巧，让 Laravel 中的控制器感觉更轻松。

## 控制器不需要扩展什么

在撰写本文时，这是您运行`php artisan make:controller` :
时得到的结果

```
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class MyController extends Controller
{
    //
} 
```

这个控制器扩展了一个基本控制器`App\Http\Controllers\Controller`。这是基本控制器的内容。

```
namespace App\Http\Controllers;

use Illuminate\Foundation\Bus\DispatchesJobs;
use Illuminate\Routing\Controller as BaseController;
use Illuminate\Foundation\Validation\ValidatesRequests;
use Illuminate\Foundation\Auth\Access\AuthorizesRequests;

class Controller extends BaseController
{
    use AuthorizesRequests, DispatchesJobs, ValidatesRequests;
} 
```

那个控制器又延伸了一个:`Illuminate\Routing\Controller`。为了使这篇文章简短，我不打算把那篇文章的内容贴在这里。只知道它包含一些注册中间件的函数和一些在一个控制器上调用其他方法的函数。

似乎有一种普遍的观点认为，为了让控制器工作，它应该扩展`Illuminate`-控制器。但是你知道吗？控制器不需要扩展任何东西。这是一个完美有效的控制器的例子。

```
namespace App\Http\Controllers;

class MyController
{
   // just add some functions
} 
```

在我们在 [Spatie](https://spatie.be) 的所有客户端项目中，默认情况下控制器不扩展任何东西。如果一个控制器需要验证或授权一个请求，我们在那个控制器上使用`AuthorizesRequests`或`ValidatesRequests`特征。

## 没有默认名称空间的控制器更好

在普通的 Laravel 应用程序中，控制器位于`App\Http\Controllers`名称空间中。这是由那个`namespace`调用在`RouteServiceProvider`中默认设置的。

```
namespace App\Providers;

use Illuminate\Support\Facades\Route;
use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;

class RouteServiceProvider extends ServiceProvider
{
    /**
     * This namespace is applied to your controller routes.
     *
     * In addition, it is set as the URL generator's root namespace.
     *
     * @var string
     */
    protected $namespace = 'App\Http\Controllers';

    // ..

    protected function mapWebRoutes()
    {
        Route::middleware('web')
             ->namespace($this->namespace)
             ->group(base_path('routes/web.php'));
    }
} 
```

这使我们能够在 routes 文件中使用一个字符串，例如将一个 URL 路由到一个控制器，如`App\Http\Controllers\MyController` :

```
// in the web routes file

Route::get('my-url', 'MyController@index'); 
```

当然，默认名称空间为我们在 routes 文件中节省了一些击键次数，但这是它给我们的唯一优势。

让我们来看看不设置默认名称空间有什么好处。如果您删除了`RouteServiceProvider`中的`protected $namespace`和`->namespace`调用，那么您必须在 routes 文件中指定完全限定的名称空间。

```
// in the web routes file

use App\Http\Controllers\MyController;

Route::get('my-url', [MyController::class, 'index']); 
```

如果您在控制器中只使用一种方法，您可以选择使用`__invoke()`。通过使用该方法，在 routes 文件中不再需要元组表示法。

```
// in the web routes file

use App\Http\Controllers\MyController;

// isn't this beautiful?
Route::get('my-url', MyController::class); 
```

用控制器的全限定名称空间替换基于短字符串的引用有几个好处。如果您使用像 PhpStorm 这样的 IDE，它现在知道正在使用哪个类。当你开始输入一个类时，它会尝试自动完成。也可以通过`MyController::class`点击。不错！如果重构控制器或其名称空间，PhpStorm 将自动更新 routes 文件。这些都是重大胜利！

另外，如果您现在想为您的控制器使用另一个名称空间，您可以简单地这样做:

```
// in the web routes file

// alternative namespace
use App\Front\Http\AlternativeNamespace\MyController;

Route::get('my-url', MyController::class); 
```

## 在关闭

Laravel 试图通过在默认情况下向脚手架控制器提供一些特征，并设置一个默认的名称空间来使新来者变得容易。但是如果你有一点经验，我强烈建议不要让你的控制器默认扩展任何东西，也不要使用默认的名称空间。

也许 Laravel 的下一个版本应该将一个类(不扩展任何东西)作为控制器，并且不使用默认的名称空间。你同意吗？让我在下面的评论中知道为什么或为什么不。