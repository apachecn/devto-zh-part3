# OpenBSD 6.4 上的 php72_fpm 默认不工作:如何修复

> 原文：<https://dev.to/nabbisen/fixing-php72fpm-on-openbsd-64-f0f>

**封面图片由 [pixel2013](https://pixabay.com/en/users/pixel2013-2364555) 原创，经过精心编辑。*

## 总结

在 OpenBSD 中，由`ports`的`pkg_add`安装 PHP 是[之后自动安装 PHP-fpm](https://github.com/openbsd/ports/commit/5b4e47f8261f71072cc15e7f1e23cb6fafb81f9e)。(很好看😃)

然而，在 PHP 7.2(和 7.1)的情况下，它不能作为现成的守护进程工作。
这个问题可以通过**手动添加其池定义**来解决。
这篇文章将展示如何做到这一点。

而且是[固定在`-current`版](https://github.com/openbsd/ports/commit/8c40c6d028d47ad4d1ecbb2e88c1a50d654230f0)里。

**注:以上链接是给 Github 提交的，但是【OpenBSD 实际使用的版本控制系统是 CVS 。*

### 环境

*   操作系统:OpenBSD 6.4 amd64
*   PHP 版本:7.2

<center>✿ ✿ ✿</center>

## 一个问题

OpenBSD 6.4 有 4 个版本的 PHP:

```
#  pkg_add php
quirks-3.16 signed on 2018-10-12T15:26:25Z
Ambiguous: choose package for php a       0: <None>  1: php-5.6.38p0
        2: php-7.0.32p1
        3: php-7.1.22
        4: php-7.2.10
Your choice: 
```

Enter fullscreen mode Exit fullscreen mode

[根据他们的支持条款](http://php.net/supported-versions.php)，PHP 5.6 几乎不再安全。
所以我选择 PHP 7。*，尤其是 7.2:

```
Your choice: 4
php-7.2.10: ok 
```

Enter fullscreen mode Exit fullscreen mode

因此，这些文件在`/etc` :
中创建

```
$  ls /etc/php*
/etc/php-7.2.ini    /etc/php-fpm.conf

/etc/php-7.2:

/etc/php-7.2.sample:
opcache.ini 
```

Enter fullscreen mode Exit fullscreen mode

可悲的是，`php-7.2.10`中的`php72_fpm`默认不起作用:

```
#  rcctl -d start php72_fpm
doing _rc_parse_conf ... php72_fpm_flags empty, using default ><
doing _rc_parse_conf /var/run/rc.d/php72_fpm
doing _rc_quirks
doing rc_check
php72_fpm
doing rc_start
doing _rc_wait start
doing rc_check
[10-Nov-2018 23:32:18] WARNING: Nothing matches the include pattern '/etc/php-fpm.d/*.conf' from /etc/php-fpm.conf at line 125.
[10-Nov-2018 23:32:18] ERROR: No pool defined. at least one pool section must be specified in config file
[10-Nov-2018 23:32:18] ERROR: failed to post process the configuration
[10-Nov-2018 23:32:18] ERROR: FPM initialization failed
doing _rc_rm_runfile
(failed) 
```

Enter fullscreen mode Exit fullscreen mode

### 原因

这是因为缺乏“池定义”。

描述在`/etc/php-fpm.ini` :
的底部

```
;;;;;;;;;;;;;;;;;;;;
; Pool Definitions ;
;;;;;;;;;;;;;;;;;;;;

; Multiple pools of child processes may be started with different listening
; ports and different management options.  The name of the pool will be
; used in logs and stats. There is no limitation on the number of pools which
; FPM can handle. Your system will tell you anyway :)

; Include one or more files. If glob(3) exists, it is used to include a bunch of
; files from a glob(3) pattern. This directive can be used everywhere in the
; file.
; Relative path can also be used. They will be prefixed by:
;  - the global prefix if it's been set (-p argument)
;  - /usr/local otherwise
include=/etc/php-fpm.d/*.conf 
```

Enter fullscreen mode Exit fullscreen mode

但是没有`/etc/php-fpm.d/*.conf`。

### 解

手动创建目录和文件:

```
#  mkdir /etc/php-fpm.d
#  touch /etc/php-fpm.d/default.conf 
```

Enter fullscreen mode Exit fullscreen mode

`/etc/php-fpm.d/default.conf`应该写什么？
我们可以通过安装(和卸载)获得它😜)PHP 7.0 ( `php-7.0.32p1`)包。
其`/etc/php-fpm.conf`包括所需的池定义。

定义的本质是这样的:

```
;;;;;;;;;;;;;;;;;;;;
; Pool Definitions ;
;;;;;;;;;;;;;;;;;;;;

[www]
user = www
group = www
listen = /var/www/run/php-fpm.sock
listen.owner = www
listen.group = www
listen.mode = 0660
pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
chroot = /var/www 
```

Enter fullscreen mode Exit fullscreen mode

另外[这里](https://gist.github.com/nabbisen/4f55fa3889010d1121ac2e3b0376fe6a)是作为主旨的完整版(423 行加注释)。

写入`/etc/php-fpm.d/default.conf`，准备完毕。
结果是:

```
#  rcctl start php72_fpm
(ok) 
```

Enter fullscreen mode Exit fullscreen mode

好😆

<center>✿ ✿ ✿</center>

为🕊服务愉快