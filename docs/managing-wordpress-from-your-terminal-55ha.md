# 从你的终端管理 WordPress

> 原文：<https://dev.to/pretzelhands/managing-wordpress-from-your-terminal-55ha>

<small>卢卡·布拉沃在 Unsplash 上拍摄的照片</small>

> 这篇文章最初发表在[我的个人博客](https://pretzelhands.com/posts/managing-wordpress-from-your-terminal)

嗨，我是理查德。我沉迷于 WordPress。

...

哦，这不是互助小组吗？好吧。但是尽管如此，让我告诉你一个不起眼的命令行工具，叫做`wp`(或者 WP CLI)。这是迄今为止我最喜欢的管理 WordPress 安装的方式，它让我在大约 95%的时间里不用点击仪表盘。

### 安装

安装并不麻烦。如果你已经安装了[作曲家](https://getcomposer.org)，那简直易如反掌。你只需要一个包就可以了。

我通常在全球范围内安装`wp`，因为我管理许多 WordPress 站点:

```
composer global require wp-cli/wp-cli 
```

Enter fullscreen mode Exit fullscreen mode

但是当然你也可以只要求本地项目的`wp`:

```
composer require wp-cli/wp-cli --dev 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有安装 Composer，还有一些其他的安装方法。请随意挑选最适合您工作流程的产品。

✨祝贺你！你的生活变得更有效率了！

### 从命令行安装 WordPress

现在看看你能用`wp`做什么，让我们安装并配置 WordPress。一切都是在你舒适的壳里完成的。我们需要做的第一件事是下载 WordPress 的最新版本。

```
$ wp core download

Downloading WordPress x.y.z (en_US)...
md5 hash verified: 83bec78836aabac08f769d50f1bffe5d
Success: WordPress downloaded. 
```

Enter fullscreen mode Exit fullscreen mode

一点也不难，是吗？现在让我们用所有必要的信息来设置我们的`wp-config.php`。为此，您可以运行以下命令

```
$ wp config create --dbname=wpdemo --dbuser=root --dbpass=root

Success: Generated 'wp-config.php' file. 
```

Enter fullscreen mode Exit fullscreen mode

当然，您应该用自己的信息替换上面的数据库信息。下一步是实际运行 WordPress 安装程序。那看起来有点像这个

```
$ wp core install \
          --url="https://wpdemo.test" \
          --title="WordPress Demo Site" \
          --admin_user="pretzelhands" \
          --admin_password="awesome-p4ssw0rd" \
          --admin_email="hello@pretzelhands.com"

Success: WordPress installed successfully. 
```

Enter fullscreen mode Exit fullscreen mode

你就完了。你现在可以访问你的 WordPress 站点的 URL，看看你的精彩的新安装！

这需要记住很多参数。但是不要害怕，`wp`就在这里！直到你记住所有这些参数，你还可以运行`wp config create --prompt`和`wp core install --prompt`。这些命令将以交互方式要求您提供所有必需的参数。俏皮！

现在让我们来谈谈你可能想拥有的其他一些很棒的特性。

### 安装插件

检查！抓取一个插件的 slug，就可以运行以下命令:

```
$ wp plugin install advanced-custom-fields

Installing Advanced Custom Fields (x.y.z)
Downloading installation package from https://downloads.wordpress.org/plugin/advanced-custom-fields.x.y.z.zip...
Unpacking the package...
Installing the plugin...
Plugin installed successfully.
Success: Installed 1 of 1 plugins.

$ wp plugin activate advanced-custom-fields 
```

Enter fullscreen mode Exit fullscreen mode

您也可以传入一个`--activate`标志来立即激活插件。这看起来像

```
$ wp plugin install advanced-custom-fields --activate 
```

Enter fullscreen mode Exit fullscreen mode

如果你不知道插件的 slug，你可以通过进入[插件库](https://wordpress.org/plugins/)，搜索你的插件，然后检查最后一个斜杠后的内容。

例如，高级定制字段插件的 URL 看起来像这样:`https://wordpress.org/plugins/advanced-custom-fields`如果你检查最后一个斜杠后的所有内容，你可以看到 slug 是`advanced-custom-fields`。这就是你传给`wp`的东西。可能需要一些时间来记住每个插件都有哪些插件，但是你很快就会记住你最喜欢的插件！

### 安装语言

我来自一个讲德语的国家。如果我把一个带有英文仪表盘的 WordPress 网站交给一个本地客户，他们可能会奇怪地看着我。谢天谢地，用`wp`安装语言是一件轻而易举的事。

```
$ wp core language install de_DE --activate

Downloading translation from https://downloads.wordpress.org/translation/core/x.y.z/de_DE.zip...
Unpacking the update...
Installing the latest version...
Translation updated successfully.
Success: Language installed.
Success: Language activated. 
```

Enter fullscreen mode Exit fullscreen mode

你完了！去喝点茶或咖啡，享受你的成功吧。这里的`--activate`标志与插件的工作方式相同。

### 安装主题

现在我相信你已经找出了模式。但是这里是你如何安装一个主题并激活它的方法

```
$ wp theme install storefront --activate

Installing Storefront (x.y.z)
Downloading installation package from https://downloads.wordpress.org/theme/storefront.x.y.z.zip...
Unpacking the package...
Installing the theme...
Theme installed successfully.
Activating 'storefront'...
Success: Switched to 'Storefront' theme.
Success: Installed 1 of 1 themes. 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经安装并运行了 WooCommerce 的店面。是的，真的就是这么回事。

### 期末笔记和进一步阅读

上面给出的三个例子向您展示了`wp`最常见的用例，但是您可以用它管理几乎任何东西。这里有一个简短的摘录，你可以在不接触 WordPress 仪表盘的情况下做些什么

*   管理用户
*   创建、更新、删除您的帖子
*   创建、更新、删除和审核评论
*   在你的数据库中进行搜索替换
*   导出和导入 WordPress 数据
*   管理多站点网络
*   定制文章类型、分类、子主题等的支架代码
*   为你的 WordPress 安装运行一个定制的 PHP shell
*   为你的 Wordpress 安装运行一个本地开发服务器
*   ...(列表**还会持续一段时间)**

 **举例来说，如果你安装了 WooCommerce，你会得到另一套与管理网上商店相关的命令。

因此，如果你正在使用 WordPress，尤其是如果你正在使用 2-3 个以上的网站，我强烈建议你走出去，安装`wp`并获得更多的空闲时间。

如果你想了解更多，你可以[查阅文档](https://make.wordpress.org/cli/handbook/)，这是一个很好的参考。否则，通过运行`wp help`并浏览手册页可以找到很多信息。

尽情享受吧！~**