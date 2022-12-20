# Laravel 的 CRUD 脚手架

> 原文：<https://dev.to/juhlinus/crud-scaffolding-in-laravel-2bai>

我最近开始在日本东京的一家名为 HeyaTalent 的公司工作。到目前为止，我很喜欢它，我在世界上最美丽的国家之一用我最喜欢的框架工作。

但是我注意到当我写代码的时候，我总是重复我自己，直到创建 CRUD 资源。

因此，我决定尝试创建我的第一个 Laravel 包！耶！

这是一个非常简单的软件包，但是可以节省你大量的时间。

只需从 packagist 获得带有 composer 的软件包。

`composer require heyatalent/laracrud`

该包利用包自动发现，所以你安装后都设置好了。

现在您只需要决定资源的名称，并像这样调用命令；

`php artisan make:crud Post`

这将生成一个控制器、模型、迁移(它将迁移哪个)、视图，并将您的足智多谋的控制器附加到`routes/web.php`文件中。

它还带有一些脚手架，这样你就可以马上开始使用它。

如果你有任何想法或可以想到任何改进，那么我很乐意听到你的意见！

在 Github repo 中提交问题或在 Twitter 上给我发消息 [@juhlinus](https://dev.to/juhlinus) 。

我希望你能像我一样从这个包中得到更多的使用！

下次见。