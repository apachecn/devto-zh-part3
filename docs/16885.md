# 在 laravel 开发的网站中添加多语言功能。

> 原文：<https://dev.to/fadilxcoder/adding-multi-language-functionality-in-a-website-developed-in-laravel-4ech>

大家好，
今天我将和大家分享如何给一个用 laravel 开发的网站添加多语言，我的版本是 **5.6** ，但它也可以在 **5.7** 上运行。

## 如此...开始了。

首先，我假设你已经有了一个 laravel 的工作版本。导航到该特定文件夹，并打开您的终端或命令提示符。

运行以下命令以创建所需的中间件。

```
$ php artisan make:middleware Localization 
```

Enter fullscreen mode Exit fullscreen mode

那就打开它。文件应该在“***project _ name/app/Http/Middleware/localization . PHP***中”

用下面的代码更新当前的代码:

```
public function handle($request, Closure $next)
{
   if(\Session::has('locale'))
   {
       \App::setlocale(\Session::get('locale'));
   }
   return $next($request);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 假设你想让你的网站使用 ***英语*** 和 ***法语*** 。

1.  转到 ***项目名称/资源/lang/en/***

2.  创建一个文件名为***【messages.php】***

3.  在“ ***项目名称/资源/lang/*** 中创建文件夹“***【fr】***”

4.  创建一个文件名为“***【messages.php】***”中的“ ***【项目名称/资源/lang/fr/***

打开两个文件夹中的“***【messages.php】***”，插入以下代码。

#### *”在“ **en** 文件夹中。*

```
 return [
    'welcome'       => 'Welcome to our application'
]; 
```

Enter fullscreen mode Exit fullscreen mode

#### *【在’**fr**文件夹中。*

```
 return [
    'welcome'       => 'Bienvenue sur notre application'
]; 
```

Enter fullscreen mode Exit fullscreen mode

## 最重要...

在“*protected $ middleware groups*section”中的“***project _ name/app/Http/kernel . PHP***中添加以下代码。

```
\App\Http\Middleware\Localization::class, 
```

Enter fullscreen mode Exit fullscreen mode

所以你的代码应该是这样的:

```
protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
            \App\Http\Middleware\Localization::class,
        ],

        'api' => [
            'throttle:60,1',
            'bindings',
        ],
    ]; 
```

Enter fullscreen mode Exit fullscreen mode

现在所有这些代码都在 route、***【project _ name/routes/web . PHP】***等定义好的 routes 之上。

```
Route::get('locale/{locale}', function ($locale){
    Session::put('locale', $locale);
    return redirect()->back();
}); 
```

Enter fullscreen mode Exit fullscreen mode

所以为了知道正在使用的语言，你可以简单地使用:

```
 <html lang="{{ app()->getLocale() }}"> 
```

Enter fullscreen mode Exit fullscreen mode

## 改变语言 en/fr

要更改语言，只需在 html 中创建一个锚标记，如下所示:

```
 <li><a href="{{ url('locale/en') }}" ><i class="fa fa-language"></i> EN</a></li>

<li><a href="{{ url('locale/fr') }}" ><i class="fa fa-language"></i> FR</a></li> 
```

Enter fullscreen mode Exit fullscreen mode

要调用所需的文本:

```
 <h1>{{ __('messages.welcome') }}</h1> 
```

Enter fullscreen mode Exit fullscreen mode**