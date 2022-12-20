# 配置 Xdebug + PHP 7 + Nginx +任何 Linux 发行版

> 原文：<https://dev.to/thamaraiselvam/configure-xdebug-php-7-nginx-any-linux-distribution-3ic0>

Xdebug 将 PHP 应用和网站的调试深化到一个你无法从使用代码级
的手动过程中获得的水平

```
var_dump() 
```

Enter fullscreen mode Exit fullscreen mode

### 获取您的 php.ini

接下来，将 php.ini 信息输出到一个文件或可以获取信息的地方。我喜欢把我的保存到一个名为 **php-info.txt**
的文件中

```
sudo php -i > ~/php-info.txt 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 Xdebug 向导

在 Xdebug 向导中将文本文件信息发送到向导中。然后按照向导提供的说明进行操作。

### 安装 php-xdebug

这将把带有 xdebug 的 php 连接到编辑器，你也可以配置编辑器和远程配置。

#### 基于 Debian 的发行版(Debian、Ubuntu、Linux Mint)

```
sudo apt install php-xdebug 
```

Enter fullscreen mode Exit fullscreen mode

#### Arch Linux

```
sudo pacman -Sy xdebug 
```

Enter fullscreen mode Exit fullscreen mode

### Edit xdebug.ini

现在编辑**xdebug . ini**T2】

```
sudo nano /etc/php/7.0/mods-available/xdebug.ini 
```

Enter fullscreen mode Exit fullscreen mode

然后粘贴以下配置

```
zend_extension=/usr/lib/php/20151012/xdebug.so
xdebug.remote_autostart = 1
xdebug.remote_enable = 1
xdebug.remote_handler = dbgp
xdebug.remote_host = 127.0.0.1
xdebug.remote_log = /tmp/xdebug_remote.log
xdebug.remote_mode = req
xdebug.remote_port = 9005 #this can be modified 
```

Enter fullscreen mode Exit fullscreen mode

### 重启服务

```
sudo systemctl restart php7.0-fpm
sudo systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

### 配置编辑器

在我的例子中，我使用 Sublime Text 3，所以使用包控制安装 xdebug 包

重启崇高文字。

***如此而已。您已成功配置 Xdebug！！！*T3】**

如果您有任何问题，请在下面评论

本文最初发表于 [Hashnode](https://thamaraiselvam.com/configure-xdebug-php-7-nginx-any-linux-distribution-cjutjwkqh001ssns1f53yt2ez)