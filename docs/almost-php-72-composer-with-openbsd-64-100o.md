# 差不多 PHP 7.2 Composer 带 OpenBSD 6.4

> 原文：<https://dev.to/nabbisen/almost-php-72-composer-with-openbsd-64-100o>

[PHP](http://php.net/) [`composer`](https://getcomposer.org/) 是一个流行的依赖/包管理器。
[OpenBSD 6.4](http://openbsd.org) 为[提供了其 1.6.3 版本](http://ports.su/www/composer)的包。
如何安装:

```
#  pkg_add composer 
```

Enter fullscreen mode Exit fullscreen mode

然而，它是基于 PHP 7.0 的，对 PHP 7.0 的支持已经结束了。
[这似乎部分是因为:](http://openports.se/www/composer)

> 还不是 7.2，因为有些东西仍然在使用 mcrypt

当然，[情况一直在被伟大的开发者](https://github.com/openbsd/ports/tree/master/www/composer)改善。

* * *

因为安全问题、PHP 扩展、依赖性或其他原因，可能有些情况下需要(或只是想要)使用 7.0 以上的 PHP。日本电子商务/购物车系统 EC-CUBE 对我来说就是这样。

我用`composer.phar` :
找到了路

```
#  php-7.2 /usr/local/libexec/composer.phar [subcommands] ... 
```

Enter fullscreen mode Exit fullscreen mode

例如，在我的例子中安装 EC-CUBE 可以使用这个命令:

```
$  php-7.2 /usr/local/libexec/composer.phar create-project ec-cube/ec-cube ./ec-cube "4.0.x-dev" --keep-vcs 
```

Enter fullscreen mode Exit fullscreen mode

**注意:`composer`建议在用户模式下运行。*

* * *

此外，在执行过程中有时需要到`php`的路径。
解决方案是创建一个符号链接:

```
#  ln -s /usr/local/bin/php-7.2 /usr/local/bin/php 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以使用`php`命令:

```
$  php /usr/local/libexec/composer.phar [subcommands] ... 
```

Enter fullscreen mode Exit fullscreen mode

根据需要使用后移除链接:

```
#  rm /usr/local/bin/php 
```

Enter fullscreen mode Exit fullscreen mode

* * *

此外，像这样的`composer` 的[平台配置有时可能会有帮助:](https://getcomposer.org/doc/06-config.md#platform) 

```
$  composer config platform.php 7.2.10
$  # or
$  composer config --global platform.php 7.2.10 
```

Enter fullscreen mode Exit fullscreen mode

* * *

快乐编码。