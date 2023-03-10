# 让您的 Laravel 开发体验棒极了的包。

> 原文：<https://dev.to/amit_merchant/packages-to-make-your-laravel-development-experience-awesome-345h>

在开发应用程序时(不一定使用 Laravel)，您会遇到需要实现某些功能的情况。在这种情况下，如果某个人已经将功能作为一个包实现了，并且被社区很好地接受，并且可以随时消费，那么您就不应该想要重新发明轮子。这是显而易见的。我过去也遵循同样的方法，今天在这篇文章中，我将列出一些我最喜欢的 Laravel 包，它们让我在 Laravel 中的开发体验变得非常美妙。

## [laravel-backup](https://github.com/spatie/laravel-backup)

这个 Laravel 包[创建了应用程序](https://docs.spatie.be/laravel-backup/v6/taking-backups/overview)的备份。备份是一个 zip 文件，包含您指定的目录中的所有文件以及数据库的转储。备份可以存储在[您在 Laravel 5](http://laravel.com/docs/filesystem) 中配置的任何文件系统上。

一旦安装，备份你的文件和数据库是非常容易的。你只需要发出这个 artisan 命令:

```
php artisan backup:run 
```

这个包需要 PHP 7 和 Laravel 5.7 或更高版本。

## [laravel-邮箱](https://github.com/beyondcode/laravel-mailbox)

该软件包在处理 Laravel 应用程序中的电子邮件时非常有用。它有一个流畅的 API，允许你定义自定义邮箱来接收电子邮件。

这里有一个关于如何做同样事情的例子:

```
Mailbox::from('{username}@gmail.com', function (InboundEmail $email, $username) {
    // Access email attributes and content
    $subject = $email->subject();

    $email->reply(new ReplyMailable);
}); 
```

您可以通过 composer 安装软件包:

```
composer require beyondcode/laravel-mailbox 
```

## [laravel-sitemap](https://github.com/spatie/laravel-sitemap)

这个包可以生成一个站点地图，而不需要你手动添加 URL。这是通过爬行你的整个网站来实现的。

```
use Spatie\Sitemap\SitemapGenerator;

SitemapGenerator::create('https://example.com')->writeToFile($path); 
```

你也可以手动创建你的站点地图:

```
use Carbon\Carbon;
use Spatie\Sitemap\Sitemap;
use Spatie\Sitemap\Tags\Url;

Sitemap::create()

    ->add(Url::create('/home')
        ->setLastModificationDate(Carbon::yesterday())
        ->setChangeFrequency(Url::CHANGE_FREQUENCY_YEARLY)
        ->setPriority(0.1))

   ->add(...)

   ->writeToFile($path); 
```

更高级的用法，查看文档[这里](https://github.com/spatie/laravel-sitemap/blob/master/README.md)。

您可以通过 composer 安装软件包:

```
composer require spatie/laravel-sitemap 
```

## [laravel-opcache](https://github.com/appstract/laravel-opcache)

[PHP op cache](http://php.net/manual/en/book.opcache.php)[能让你的 Laravel app 飞起来](https://medium.com/appstract/make-your-laravel-app-fly-with-php-opcache-9948db2a5f93)。这个由 Appstract 开发的包包含一些有用的 Artisan 命令来处理 PHP OPcache。该软件包需要 Laravel 5.5 或更高版本。对于较旧的 Laravel 版本(5.1 或更高版本)，可以使用这个包的 1.3.0 版本。

要使用这个包，您需要登录到您的服务器/虚拟机并运行其中一个命令。

清除 OPcache:

```
php artisan opcache:clear 
```

显示 OPcache 配置:

```
php artisan opcache:config 
```

显示 OPcache 状态:

```
php artisan opcache:status 
```

预编译您的应用程序代码:

```
php artisan opcache:optimize 
```

以编程方式使用软件包:

```
use Appstract\Opcache\OpcacheFacade as OPcache;

...

OPcache::clear(); 
```

您可以通过 Composer 安装软件包:

```
composer require appstract/laravel-opcache 
```

## [【幼虫-中介】](https://github.com/plank/laravel-mediable)

Laravel-Mediable 是一个软件包，可以轻松地将媒体文件上传和附加到 Laravel 5 的模型上。

它具有以下特点:

*   文件系统驱动的方法很容易配置，允许任意数量的具有不同可访问性的上传目录。
*   多对多多态关系允许将任意数量的媒体分配给任意数量的其他模型，而无需修改模式。
*   使用标签将媒体附加到模型，以设置和检索用于特定目的的媒体，如“缩略图”、“特色图像”、“图库”或“下载”。
*   轻松查询媒体并通过 MIME 类型、扩展名和/或聚合类型(例如 jpeg、png 或 gif 图像)限制上传。

使用这个包很简单。将一个文件上传到服务器，并将其放在文件系统磁盘上名为“uploads”的目录中。这将创建一个可用于引用该文件的媒体记录。

```
$media = MediaUploader::fromSource($request->file('thumb'))
    ->toDestination('uploads', 'blog/thumbnails')
    ->upload(); 
```

将媒体附加到另一个有一个或多个标签定义其关系的雄辩模型。

```
$post = Post::create($this->request->input());
$post->attachMedia($media, ['thumbnail']); 
```

通过标签从模型中检索媒体。

```
$post->getMedia('thumbnail')->first()->getUrl(); 
```

使用 Composer 安装软件包:

```
composer require plank/laravel-mediable 
```

## [菜单](https://github.com/spatie/laravel-menu)

这个包提供了一个流畅的界面，可以在你的 Laravel 应用程序中构建任意大小的菜单。

所有的类都提供了人类可读的、流畅的接口(没有数组配置)。此外，您可以选择更详细、更灵活的语法，或者覆盖大多数用例的方便方法。

```
Menu::new()
    ->add(Link::to('/', 'Home'))
    ->add(Link::to('/about', 'About'))
    ->add(Link::to('/contact', 'Contact'))
    ->add(Html::empty())
    ->render();

// Or just...
Menu::new()
    ->link('/', 'Home')
    ->link('/about', 'About')
    ->link('/contact', 'Contact')
    ->empty() 
```

上面将输出以下 HTML:

```
<ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about">About</a></li>
    <li><a href="/contact">Contact</a></li>
    <li></li>
</ul> 
```

您可以通过 composer 安装软件包:

```
$ composer require spatie/laravel-menu 
```

这个包的深入文档可以在这里找到:[https://docs.spatie.be/menu](https://docs.spatie.be/menu)。

## 结论

我真诚地感谢开发人员提供了这样的包，让我的生活变得更容易，我希望它也能对你有所帮助。每当我遇到这样的软件包时，我会不时地在本文中添加更多这样的软件包。下次见，敬请期待！

> 原文写于[https://www . Amit merchant . com/make-laravel-development-experience-awesome-with-this-packages/](https://www.amitmerchant.com/make-laravel-development-experience-awesome-with-this-packages/)