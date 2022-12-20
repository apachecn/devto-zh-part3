# 你不需要那个模块包

> 原文：<https://dev.to/matthewbdaly/you-dont-need-that-module-package-5gg6>

最近我看到一些 Laravel 包发布在 Reddit 这样的地方，它们提供了让你的项目更加模块化的方法，让你把它们的类从通常的结构中分离出来，放在一个叫做`packages/`或`modules/`的独立文件夹中。但是，这些包完全是多余的，只需很少的工作就可以用 Composer 完成同样的事情。此外，它的大部分并不特定于 Laravel，也可以应用于任何其他使用 Composer 的框架。

据我所知，主要有两种方法——将它保存在一个项目中，并将模块转移到单独的 Composer 包中。

# 单项工程

假设我们有一个全新的 Laravel 项目，名称空间保留为默认名称空间`App`。这是`composer.json`文件的`autoload`部分的样子:

```
"autoload": {
        "psr-4": {
            "App\\": "app/"
        },
        "classmap": [
            "database/seeds",
            "database/factories"
        ]
    }, 
```

Enter fullscreen mode Exit fullscreen mode

Composer 允许多种方式自动加载类，并且您可以根据需要添加额外的名称空间。最好的方法可能是使用 PSR-4 自动加载，如下例所示:

```
"autoload": {
        "psr-4": {
            "App\\": "app/",
            "Packages\\": "packages"
        },
        "classmap": [
            "database/seeds",
            "database/factories"
        ]
    }, 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您将模型`Post.php`放在文件夹`packages/Blog/Models/`中，那么它将映射到名称空间`Packages\Blog\Models\Post`，如果您在文件中将名称空间设置为这个，并运行`composer dump-autoload`，那么您应该能够毫无困难地从该名称空间导入它。与`App\`名称空间一样，因为它使用 PSR-4，所以您只需指定顶级名称空间，而下面的文件夹和文件必须镜像该名称空间，例如，`Packages\Foo\Bar`映射到`packages/Foo/Bar.php`。如果出于某种原因，PSR-4 自动加载没有很好地映射到您想要做的事情，那么您可以使用其他方法-请参考 Composer 文档的[相关部分，了解其他可用的方法。](https://getcomposer.org/doc/04-schema.md#autoload)

控制器是最难的部分，因为默认情况下，Laravel 的路由假设控制器都在`App\Http\Controllers`名称空间下工作，所以您可以缩短使用的名称空间。我知道有两种方法可以解决这个问题。一种是在引用每个控制器时指定完整的名称空间:

```
Route::get('/', '\App\Modules\Http\Controllers\FooController@index'); 
```

Enter fullscreen mode Exit fullscreen mode

另一个选项是更新`RouteServiceProvider.php`的名称空间属性。默认为:

```
protected $namespace = 'App\Http\Controllers'; 
```

Enter fullscreen mode Exit fullscreen mode

如果您想在一个更方便的名称空间下放置所有的控制器，那么您可以替换它，它将成为应用于您的路由文件的默认名称空间。

其他应用程序组件，如迁移、路由和视图，可以很容易地从服务提供商处加载。只需为您的模块创建一个服务提供者，在`config/app.php`中注册它，并设置`boot()`方法从适当的位置加载您想要的任何组件，如下例所示:

```
 $this->loadMigrationsFrom( __DIR__.'/../database/migrations');
        $this->loadRoutesFrom( __DIR__.'/../routes.php');
        $this->loadViewsFrom( __DIR__.'/../views', 'comments'); 
```

Enter fullscreen mode Exit fullscreen mode

# 单独包装

在项目的初始阶段，当您可能需要频繁切换以编辑项目的不同部分时，上述方法特别有效。然而，稍后，一旦项目的许多部分稳定下来，将模块放入单独的存储库中，并使用 Composer 将它们作为依赖项放入，利用它对私有存储库的支持，这可能更有意义。我也经常从一开始就采用这种方法，没有任何问题。

这种方法有许多优点。如果需要的话，在其他项目中重用项目的某些部分会更容易。此外，如果您将您的测试放在包含它们测试的组件的包中，这意味着您可以在每次更改时运行每个模块的测试，而不是为整个项目运行一个单一的测试套件，并且将主项目的测试套件限制为那些验证整个项目的集成和验收测试，以及对保留在主存储库中的代码的任何单元测试，从而导致更快的测试运行。

不要误解我的意思，让你的代码更加模块化绝对是一件好事，我完全赞同。然而，只需要一点点 Composer 的知识就可以在没有任何第三方包的情况下实现这一点，这很好，因为你不再依赖于一个随时可能落后于形势或被放弃的包。