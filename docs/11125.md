# 2019 年最佳 60+ Laravel 面试问题

> 原文：<https://dev.to/maddymc/best-60-laravel-interview-questions-2019-2hi>

[https://www . best interview question . com/laravel-interview-questions](https://www.bestinterviewquestion.com/laravel-interview-questions)

Laravel 是基于 MVC 设计模式的免费开源“PHP 框架”。它是由泰勒·奥特威尔创造的。Laravel 提供了富有表现力和优雅的语法，有助于轻松快速地创建精彩的 web 应用程序。
许多公司寻找了解 Laravel 框架的开发人员。在这篇文章中，你可以读到几个关于 Laravel 框架的最佳面试问题。

前 30 名 Laravel 面试问答。

以下是最新的 Laravel 面试问题和答案列表。

1.  什么是 Laravel？

Laravel 是基于 MVC 设计模式的免费开源“PHP 框架”。它是由泰勒·奥特威尔创造的。Laravel 提供了富有表现力和优雅的语法，有助于轻松快速地创建精彩的 web 应用程序。

1.  使用 Laravel 框架有什么利弊？使用 Laravel 框架的优点

    Laravel framework 内置了轻量级刀片模板引擎，可以加快编译任务的速度，轻松创建包含动态内容的布局。困扰代码的可重用性。
    雄辩 ORM 用 PHP 主动记录实现
    内置命令行工具“Artisan”用于创建代码骨架、数据库结构和构建它们的迁移

使用 laravel 框架的缺点

```
Development process requires you to work with standards and should have real understanding of programming
Laravel is new framework and composer is not so strong in compare to npm (for node.js), ruby gems and python pip.
Development in laravel is not so fast in compare to ruby on rails.
Laravel is lightweight so it has less inbuilt support in compare to django and rails. But this problem can be solved by integrating third party tools, but for large and very custom websites it may be a tedious task 
```

1.  解释拉勒维尔的事件？

事件是由程序识别的可以由程序或代码处理的动作或事件。Laravel events 提供了一个简单的 observer 实现，允许您订阅和监听应用程序中发生的各种事件/操作。

所有事件类通常存储在 app/Events 目录中，而它们的侦听器存储在应用程序的 app/Listeners 中。

1.  解释 laravel 中的验证？

在编程中，验证是一种简便的方法，可以确保数据在进入数据库之前始终是干净的、预期的格式。Laravel 提供了几种不同的方法来验证应用程序输入的数据。默认情况下，Laravel 的基本控制器类使用 ValidatesRequests 特征，它提供了一种方便的方法来验证所有来自客户端的 HTTP 请求。您还可以通过创建表单请求来验证 laravel 中的数据。点击此处阅读更多关于 Laravel 中数据验证的信息。

1.  如何通过 composer 安装 laravel？

您可以运行以下命令通过 composer 安装 Laravel。

1.  列出 laravel 5.0 的一些特性？

    内置 CRSF(跨站点请求伪造)保护。
    内置分页
    反向路由
    查询构建器
    路由缓存
    数据库迁移
    IOC(逆控制)容器或服务容器。

2.  什么是 PHP artisan。列出一些 artisan 命令？PHP artisan 是 Laravel 附带的命令行界面/工具。它提供了许多有用的命令，可以帮助您轻松构建应用程序。以下是一些工匠命令的清单

    php 工匠列表
    php 工匠帮助
    php 工匠修补匠
    php 工匠制作
    php 工匠–versian
    PHP 工匠制作模型 model_name
    php 工匠制作控制器 controller_name

3.  列出一些 Laravel 5.4 提供的默认包？下面是 Laravel 5.4 提供的一些官方/默认软件包的列表

    出纳
    特使
    护照
    球探
    交际花

4.  Laravel 有哪些命名的路线？
    命名路由是 Laravel 框架的另一个惊人特性。命名路由允许在生成重定向或 Url 时更方便地引用路由。您可以通过将命名方法链接到路由定义来指定命名路由:

Route::get('user/profile '，function(){
//
})->name(' profile ')；

您可以为控制器操作指定路由名称:

Route::get('user/profile '，' user controller @ show profile ')-> name(' profile ')；

一旦为您的路由指定了名称，您就可以在通过全局路由功能生成 URL 或重定向时使用该路由的名称:

//生成 URL...
$ URL = route(' profile ')；

//生成重定向...
返回 redirect()- >路由(' profile ')；

1.  什么是数据库迁移。如何通过 artisan 创建迁移？

迁移就像是对数据库的版本控制，允许您的团队轻松地修改和共享应用程序的数据库模式。迁移通常与 Laravel 的 schema builder 配合使用，以轻松构建应用程序的数据库模式。

使用以下命令通过 artisan 创建迁移数据。

//创建迁移
PHP artisan make:Migration create _ users _ table

1.  什么是服务提供商？

服务提供商是所有 laravel 应用程序引导的中心位置。您的应用程序以及所有 Laravel 核心服务也是由服务提供商引导的。所有的服务提供者都扩展了 Illuminate \ Support \ service provider 类。大多数服务提供者包含一个注册和一个引导方法。在 register 方法中，您应该只将东西绑定到服务容器中。永远不要试图在 register 方法中注册任何事件侦听器、路由或任何其他功能。
您可以从这里了解更多关于服务提供商的信息

阅读最新的 Codeigniter 面试问题 2018。

1.  解释一下 Laravel 的服务容器？

Laravel 最强大的特性之一是它的服务容器。它是在 Laravel 中解析类依赖和执行依赖注入的强大工具。依赖注入是一个有趣的短语，本质上是指类依赖通过构造函数或者在某些情况下通过“setter”方法被“注入”到类中。

1.  什么是作曲家？

Composer 是 PHP 中管理依赖关系的工具。它允许您声明项目所依赖的库，并为您管理(安装/更新)它们。Laravel 利用 Composer 来管理其依赖关系。

1.  Laravel 中的依赖注入是什么？

在软件工程中，依赖注入是一种由一个对象提供另一个对象的依赖的技术。依赖是可以使用的对象(服务)。注入是将依赖关系传递给使用它的依赖对象(客户机)。服务成为客户端状态的一部分。[1]将服务传递给客户机，而不是让客户机构建或找到服务，是该模式的基本要求。
出处([https://en.wikipedia.org/wiki/Dependency_injection](https://en.wikipedia.org/wiki/Dependency_injection))。
可以通过构造函数、setter、属性注入来做依赖注入。

1.  什么是拉勒维尔合同？

Laravel 的契约只不过是定义 Laravel 框架提供的核心服务的一组接口。
阅读更多关于 laravel Contract 的信息

阅读最新的 Symfony 面试问题

1.  解释 Laravel 的立面？

Laravel Facades 为应用程序的服务容器中可用的类提供了一个静态的接口。Laravel 自带了许多 faces，这些 faces 提供了对 Laravel 的几乎所有特性的访问。Laravel facades 充当服务容器中底层类的“静态代理”,提供了简洁、有表现力的语法，同时保持了比传统静态类方法更好的可测试性和灵活性。Laravel 的所有外观都在 Illuminate\Support\Facades 名称空间中定义。您可以像这样轻松地访问外观:

使用照明\支持\立面\缓存；

Route::get('/cache '，function () {
返回缓存::get(' key ')；
})；

1.  什么是拉勒维尔口才？

Laravel 的雄辩的 ORM 是一个简单的活动记录实现与您的数据库。Laravel 提供了许多不同的方式来与你的数据库交互，雄辩是其中最值得注意的。每个数据库表都有一个相应的“模型”，用于与该表进行交互。模型允许您查询表中的数据，以及向表中插入新记录。

下面是使用 concertive 在数据库中查询和插入新记录的示例。

//从标记为' new '
$ products = Product::where(' tag '，' new ')的 products 表中查询或查找记录；
//插入新记录
$ Product =新产品；
$ product->title = " Iphone 7 "；
$ product->price = " $ 700 "；
$ product->tag = ' iphone '；
$ product->save()；

1.  如何在 Laravel 中启用查询日志？

使用 enableQueryLog 方法在 Laravel 中启用查询日志

DB::connection()-> enableQueryLog()；
使用 getQueryLog 方法可以得到已执行查询的数组:
$ queries = DB::getquery log()；

1.  什么是 Laravel 中的反向路由？

Laravel 反向路由基于路由声明生成 URL。反向路由使您的应用程序更加灵活。它定义了链路和 Laravel 路由之间的关系。当使用现有路由的名称创建链接时，Laravel 会自动创建适当的 Uri。这是一个反向路由的例子。

//路由声明
Route::get('login '，' users @ log in ')；

使用反向路由，我们可以创建一个到它的链接，并传入我们定义的任何参数。如果没有提供可选参数，将从生成的链接中删除。

它会自动生成一个像[http://xyz.com/login](http://xyz.com/login)在视图中的网址。

阅读 2018 年 40 个最佳 PHP 面试问题

1.  如何关闭 CRSF 保护特定路线在拉韦勒？

要关闭 Laravel 中的 CRSF 保护，请在“app/Http/Middleware/verifycsrftoken . PHP”中添加以下代码

//添加路由数组跳过 CSRF 检查
private $ except URLs =[' controller/route 1 '，' controller/route 2 ']；
//修改此函数
公共函数句柄($request，Closure $ next){
//添加此条件 for each($ this->except URLs as $ route){
if($ request->is($ route)){
return $ next($ request)；
}
}
返回 parent::handle($request，$ next)；
}

1.  拉勒维尔的特质是什么？PHP 特征只是一组您希望包含在另一个类中的方法。一个特征，像一个抽象类，不能被自己实例化。创建 Trait 是为了减少 PHP 中单一继承的限制，它允许开发人员在不同类层次结构中的几个独立类中自由地重用方法集。这里有一个特质的例子。

可分享的特质{

公共函数 share($item)
{
返回‘共享此项’；
}

}
然后，您可以将该特征包含在其他类中，如下所示:

班级帖子{

使用 Sharable

}

类别注释{

使用 Sharable

}
现在，如果您要从这些类中创建新的对象，您会发现它们都有 share()方法可用:
$ Post = new Post；
echo $ post->share(’)；//'共享此项目'

$comment =新评论；
echo $ comment->share(')；//'共享此项目'

1.  Laravel 支持缓存吗？

是的，Laravel 支持流行的缓存后端，如 Memcached 和 Redis。
默认情况下，Laravel 被配置为使用文件缓存驱动程序，它将序列化的缓存对象存储在文件系统中。对于大型项目，建议使用 Memcached 或 Redis。

1.  解释一下 Laravel 的中间件？

顾名思义，中间件充当请求和响应之间的中间人。这是一种过滤机制。例如，Laravel 包括一个验证应用程序的用户是否经过身份验证的中间件。如果用户通过身份验证，他将被重定向到主页，否则，他将被重定向到登录页面。

Laravel 中有两种类型的中间件。
全局中间件:将在应用程序的每个 HTTP 请求上运行。
路由中间件:将被分配到特定的路由。
阅读更多关于 Laravel middlewares 的信息

1.  卢蒙是什么？

Lumen 是建立在 Laravel 顶级组件之上的 PHP 微框架。它是由泰勒·奥特威尔创造的。它是构建基于 Laravel 的微服务和快速 REST API 的完美选择。这是目前最快的微框架之一。您可以通过运行以下命令使用 composer 安装 Lumen

composer create-project-prefere-dist laravel/lumen 博客

1.  解释 Laravel 中的捆绑包？

在 Laravel 中，包也称为包。包是扩展 Laravel 功能的主要方式。包可以是任何东西，从处理诸如 Carbon 之类的日期的好方法，到 Behat 之类的完整 BDD 测试框架。在 Laravel 中，您也可以创建自己的定制包。你可以从这里了解更多关于包裹的信息

1.  如何在 Laravel Modal 中使用自定义表格？

您可以在 Laravel 中使用自定义表格，方法是覆盖 concertive 的 protected＄table 属性。

下面是使用示例

类用户扩展了雄辩{
protected $ table = " my _ User _ table "；

}

1.  列出 Laravel 口才中可用的关系类型？

以下是 Laravel 雄辩 ORM 支持的关系类型。

```
One To One
One To Many
One To Many (Inverse)
Many To Many
Has Many Through
Polymorphic Relations
Many To Many Polymorphic Relations 
```

你可以从这里阅读更多关于拉勒维尔雄辩术的内容

1.  为什么迁移是必要的？

迁移是必要的，因为:

```
Without migrations, database consistency when sharing an app is almost impossible, especially as more and more people collaborate on the web app.
Your production database needs to be synced as well. 
```

1.  提供安装 Laravel 5.4 的系统要求？

为了安装 laravel，请确保您的服务器满足以下要求:

```
PHP >= 5.6.4
OpenSSL PHP Extension
PDO PHP Extension
Mbstring PHP Extension
Tokenizer PHP Extension
XML PHP Extension 
```

1.  列出 Laravel 中查询生成器提供的一些聚合方法？

    计数()
    最大值()
    最小值()
    平均值()
    总和()