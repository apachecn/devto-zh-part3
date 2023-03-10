# 生产专用 PHP+Apache dock 映像

> 原文:# t0]https://dev . to/silarri/un-image docker-PHP-Apache-定制-生产-15e 2

创建与您相似的 PHP+Apache docker 图像。因此，如果您是此博客的读者，并且订阅了新文章，它将非常完美💖。

## 什么是完美的对接图像？

这首先是根据**你的需要和经验**建立起来的形象。您已确定了在您的开发过程中不断重复的常见配置模式**。作为一个好人，您希望将这些应用程序放在一个通用层中，以便于维护。**

根据我自己的经验和我大部分时间开发的应用程序类型，我认为 Apache PHP 映像是完美的:

*   [Composer](https://getcomposer.org/) 安装**默认**(现在不使用依赖管理器？页:1。
*   PHP 和 Apache 配置为“T0”生产。
*   默认情况下启用的循环 PHP 模块。
*   比 Apache 的默认错误页面安静一点。
*   最适合与 Symfony 一起运行的应用程序。

## 创造他完美的形象

首先，我们将从 php 的官方形象开始，因为我们想节省时间，而不是重新发明轮子。你知道吗？该映像甚至随附了一个集成的 Apache 服务器。**顶。**

🤓*为什么使用 Apache 而不是 nginx？*

因为这篇文章不是中立的，必须作出决定。就我个人而言，我发现两者之间的性能差异微不足道，对于大多数项目来说，Apache 是理想的选择。对于 PHP Docker 团队将直接执行的安全更新来说，这也是一种更安静的方式。但是，我有时会使用 nginx 来执行一些更大的项目。再一次，这真的仍然是**需要、经验和品味的问题**。

因此，我们将从以下行开始我们的码头风格:

```
# Dockerfile
FROM php:7.4-apache 
```

我们构建并运行所有这些以确保一切顺利:

```
docker build -t super/hero .
docker run -it -p 8080:80 super/hero 
```

访问 [http://127.0.0.1:8080](http://127.0.0.1:8080) 时，一定要翻到美丽的 403 页。这意味着一切都很顺利，还没有你的消息来源，所以 Apache 很好地回答你！

### 默认合成器

再简单不过了，我们会安装最新版本的 composer，并使该命令在容器中的任何位置都可以访问。

```
# Dockerfile
ENV COMPOSER_ALLOW_SUPERUSER=1
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version 
```

### 配置阿帕奇

默认情况下，正式映像配置为在/var/www/html 中具有 Web 服务器根目录。**太长了！**我们将忽略这一切，将我们未来的源代码直接存放在容器根目录的 **/app** 文件夹中。

```
# conf/vhost.conf
<VirtualHost *:80>
    ServerAdmin contact@monsupersite.fr

    DocumentRoot /app

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> 
```

```
# conf/apache.conf
<Directory /app/>
    Options -Indexes +FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

ServerTokens Prod
ServerSignature Off 
```

```
# Dockerfile
# ...
ADD conf/vhost.conf /etc/apache2/sites-available/000-default.conf
ADD conf/apache.conf /etc/apache2/conf-available/z-app.conf
RUN a2enconf z-app 
```

此处更改了 Apache 的基本 vhost 配置(000-default.conf)，该配置现在提供/app 文件夹的内容。您也可以将/app 资料夹设定为不允许 Apache 列出目录中含有`Options -Indexes`的档案。

就我而言，我不喜欢用一个来设置我的网站 htaccess(出于性能原因)。这就是为什么我放了指令`AllowOverride None`。因为影像是用于暂存/生产，所以也删除了与方针`ServerTokens Prod`和`ServerSignature Off`一起使用的基础设施的相关信息。

我已自愿将配置命名为“`z-app`”，以确保它最后加载，并且没有其他模块会导致指令过载。

### 配置 de PHP

我们将根据未来图像的重复需要配置 PHP。请不要在此处输入太大的值，如果您的特定容器需要更快的配置，则可以在此应用程序特有的 dockerfile 中复盖此基本配置。

```
; conf/php.ini
date.timezone = Europe/Paris

opcache.enable = 1
opcache.enable_cli = 1
opcache.memory_consumption = 128
opcache.revalidate_freq = 0
apc.enable_cli = On

upload_max_filesize = 16M
post_max_size = 16M

realpath_cache_size = 4096k
realpath_cache_ttl = 7200

display_errors = Off
display_startup_errors = Off 
```

```
# Dockerfile
# ...
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    libicu-dev
RUN docker-php-ext-install -j$(nproc) opcache pdo_mysql
ADD conf/php.ini /usr/local/etc/php/conf.d/app.ini 
```

根据需要自定义默认模块。对我来说，我的几乎所有项目都使用 PDO 和 MySQL。缺省情况下，OPCache 也是如此。

### 漂亮的缺省错误页

谁从来没见过 Apache 的 404 页面？就是失去‘t0’[【忍者奉献大数据亲婴儿脚】](https://www.mathieupassenaud.fr/un-developpeur-nest-pas-un-joueur/) 等信誉的保证，你站在顾客面前。因此，我们将修改现有的 conf，增加错误页面处理:

```
# conf/apache.conf
# ...
<Directory /errors/>
    Options -Indexes
    AllowOverride None
    Require all granted
</Directory>

Alias /_errors/ /errors/
ErrorDocument 404 /_errors/404.html
ErrorDocument 403 /_errors/403.html
ErrorDocument 500 /_errors/500.html 
```

```
# Dockerfile
# ...
ADD errors /errors 
```

为了避免与将来的应用程序冲突，我将错误页的内容放在单独的文件夹(/errors 位于容器的根目录)中，并使用/_errors 路径隔离特定于错误页的 HTTP 请求。

## 到底是什么结果？

Dockerfile 已经过优化，可以减小层的大小。

```
# Dockerfile
FROM php:7.2-apache

ENV COMPOSER_ALLOW_SUPERUSER=1

EXPOSE 80
WORKDIR /app

RUN apt-get update -qy && \
    apt-get install -y \
    git \
    libicu-dev \
    unzip \
    zip && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# PHP Extensions
RUN docker-php-ext-install -j$(nproc) opcache pdo_mysql
ADD conf/php.ini /usr/local/etc/php/conf.d/app.ini

# Apache
ADD errors /errors
RUN a2enmod rewrite remoteip
ADD conf/vhost.conf /etc/apache2/sites-available/000-default.conf
ADD conf/apache.conf /etc/apache2/conf-available/z-app.conf
RUN a2enconf z-app
ADD index.php /app/index.php 
```

```
# conf/apache.conf
<Directory /app/>
    Options -Indexes +FollowSymLinks
    AllowOverride None
    Require all granted

    SetEnvIf X_FORWARDED_PROTO https HTTPS=on
</Directory>

ServerTokens Prod
ServerSignature Off

<Directory /errors/>
    Options -Indexes
    AllowOverride None
    Require all granted
</Directory>

Alias /_errors/ /errors/
ErrorDocument 404 /_errors/404.html
ErrorDocument 403 /_errors/403.html
ErrorDocument 500 /_errors/500.html 
```

```
# conf/vhost.conf
<VirtualHost *:80>
    ServerAdmin guillaume@silarhi.fr

    DocumentRoot /app

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> 
```

```
; conf/php.ini
date.timezone = Europe/Paris

opcache.enable = 1
opcache.enable_cli = 1
opcache.memory_consumption = 128
opcache.revalidate_freq = 0
apc.enable_cli = On

upload_max_filesize = 16M
post_max_size = 16M

realpath_cache_size=4096k
realpath_cache_ttl=7200

display_errors = Off
display_startup_errors = Off 
```

好奇吗？我只为你准备了：

*   [以超菲律宾语为早上好](https://labs.silarhi.fr/php)的图像对接演示
*   [以 Symfony](https://labs.silarhi.fr) ( [及其相关博客文章](https://blog.silarhi.fr/deploiement-continu-symfony-docker-circleci/)为基础的更加具体的演示
*   [404 页的一个例子](https://labs.silarhi.fr/php/liencass%C3%A9)
*   [用 Traefik](https://github.com/silarhi/labs.silarhi.fr/tree/master/php) 处理这一切的源代码

## 要更进一步

*   [github](https://github.com/silarhi/docker-php)文章中使用的全部源代码
*   [使用 Traefik](https://blog.silarhi.fr/docker-compose-traefik-https/) 在生产服务器上设置坞站映像
*   [坞站枢纽中的图像](https://hub.docker.com/r/silarhi/php-apache)