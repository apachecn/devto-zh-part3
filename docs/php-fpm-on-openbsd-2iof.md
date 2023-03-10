# PHP-OpenBSD 6.4 上的 FPM 7.2

> 原文：<https://dev.to/nabbisen/php-fpm-on-openbsd-2iof>

### 总结

[PHP-FPM](https://php-fpm.org/) ，PHP FastCGI 进程管理器，是现今 OpenBSD 包中 [PHP](https://www.php.net/) 包的一部分。
所以安装 PHP ( `php-7.?`由于版本原因)会自动附带`php7?_fpm`💃我会在这篇文章中告诉你如何设置它😃

##### 环境

*   操作系统:OpenBSD 6.4 amd64
*   PHP: 7.2

<center>✿ ✿ ✿</center>

### 安装

第一步是安装 PHP 包:

```
#  pkg_add php
Ambiguous: choose package for php a       0: <None>  1: php-5.6.38p0
        2: php-7.0.32p1
        3: php-7.1.22
        4: php-7.2.10
Your choice: 
```

Enter fullscreen mode Exit fullscreen mode

我选择“4”是因为 PHP 版本的每个支持术语[。](https://www.php.net/supported-versions.php)

然后这些目录/文件被制作:

```
$  ls /etc/php*
/etc/php-7.2.ini          /etc/php-fpm.conf

/etc/php-7.2:
server
/etc/php-7.2.sample:
gd.ini       opcache.ini 
```

Enter fullscreen mode Exit fullscreen mode

嗯，`/etc/php-7.2.sample`中的`.ini`文件是 PHP 扩展。
根据需要，将每个副本复制到`/etc/php-7.2/`来激活扩展:

```
#  ln -sf /etc/php-7.2.sample/%target-ini-files% /etc/php-7.2/ 
```

Enter fullscreen mode Exit fullscreen mode

根据需要编辑`/etc/php-7.2.ini`。
比如:

```
; Maximum allowed size for uploaded files.
; http://php.net/upload-max-filesize
;upload_max_filesize = 2M upload_max_filesize = 5M 
```

Enter fullscreen mode Exit fullscreen mode

此外，本手册也作为`/usr/local/share/doc/pkg-readmes/php-7.2`安装，其中声明:

> 主要的 OpenBSD php 包包括 php-fpm，FastCGI 进程管理器。管理 FastCGI 进程池:启动/重启它们，并根据配置维护最小和最大数量的备用进程。您可以使用 rcctl(8)在引导时启用 php-fpm，并在运行时启动它:2
> 
> rcctl 使能 php72_fpm
> rcctl 启动 php72_fpm

好的。我们准备好了。
让我们启动守护进程:

```
#  rcctl enable php72_fpm
#  rcctl start php72_fpm 
```

Enter fullscreen mode Exit fullscreen mode

**故障排除:如果`php72_fpm`(或`php71_fpm`)[无法启动，可能是已知的 bug](https://obsd.solutions/en/blog/2018/11/24/fixing-php72_fpm-on-openbsd-64-doesnt-work-by-default/index.html) 。*

### 用法

接下来，我们要准备一个 web 服务器。
所以让我们编辑 [`/etc/httpd.conf`](https://man.openbsd.org/httpd.conf.5) 以在[服务器](https://man.openbsd.org/httpd.conf.5#SERVERS)部分添加`fastcgi socket`如下:

```
types {
    include "/usr/share/misc/mime.types"
}

ext_addr="egress"

server "default" {
    listen on $ext_addr port 80

    root "/htdocs"
    directory index index.php

    location "*.php*" {
        fastcgi socket "/run/php-fpm.sock"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意 [`chroot`](https://man.openbsd.org/httpd.conf.5#chroot) 在这个上下文中起作用。
因此，`/etc/httpd.conf`中的`fastcgi socket "/run/php-fpm.sock"`其实就是`fastcgi socket "/var/www/run/php-fpm.sock"`的意思。
这和`root "/htdocs"`的意思`"/var/www/htdocs"`是一样的。

### 测试

让我们把`/var/www/htdocs/index.php`做成这样来测试:

```
#  echo "<?php phpinfo(); ?>" > /var/www/htdocs/index.php
#  # delete the file above afterwards as needed 
```

Enter fullscreen mode Exit fullscreen mode

然后浏览你的主机就会看到整个 phpinfo！

<center>✿ ✿ ✿</center>

快乐服务💃