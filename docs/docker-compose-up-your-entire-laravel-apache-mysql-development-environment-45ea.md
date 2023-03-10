# 组成你的整个 Laravel + Apache + MySQL 开发环境。

> 原文：<https://dev.to/veevidify/docker-compose-up-your-entire-laravel-apache-mysql-development-environment-45ea>

## 目录

*   [内容](#contents)
*   [简介](#intro)
*   [设置](#the-setup)
*   【Laravel 的网络服务器映像
*   [T2`docker-compose.yml`](#-raw-dockercomposeyml-endraw-)
*   [启动并运行](#up-and-running)
*   [助手脚本(可选)](#helper-scripts-optional)
    *   [T2`container`](#-raw-container-endraw-)
    *   [T2`db`](#-raw-db-endraw-)
    *   [T2`composer`](#-raw-composer-endraw-)
    *   [T2`php-artisan`](#-raw-phpartisan-endraw-)
    *   [T2`phpunit`](#-raw-phpunit-endraw-)
*   [TL；博士](#tldr)

## [T1】简介](#intro)

当新开发人员加入到您的项目中来时，您可能不希望他们花几个小时在文档和 StackOverflow 之间跳来跳去，弄清楚如何让任何东西工作。要在本地运行一个版本的应用程序，他们可能需要完成很多事情:php、ini 配置、php 扩展、apache 配置、apache 站点支持的配置、设置 mysql，...这样的例子不胜枚举。除非您已经设置了`docker`环境，所以他们可以简单地:

```
$ git clone git@git.repo.url/laravel-project
$ cd laravel-project
$ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

并且能够从`composer`、`php artisan`开始，写一些代码。

## 调教

为了演示现有的 laravel 应用程序，我将使用一个克隆自[https://github.com/laravel/laravel.git](https://github.com/laravel/laravel.git)的空白 laravel 应用程序

```
$ git clone https://github.com/laravel/laravel.git
$ cd laravel
$ git checkout -b dev-env
$ cp .env.example .env 
```

Enter fullscreen mode Exit fullscreen mode

下面是我将如何构建我的 docker 环境文件:

```
 app
  |__bootstrap
  |__config
  |__database
  |__public
  |__resources
  |__routes
  |__run               (+)
     |__.gitkeep       (+)
  |__storage
  |__tests
  .dockerignore        (+)
  .editorconfig
  .env
  .env.example
  .gitattributes
  .gitignore
  artisan
  CHANGELOG.md
  composer.json
  docker-compose.yml   (+)
  Dockerfile           (+)
  package.json
  phpunit.xml
  readme.md
  server.php
  webpack.mix.js 
```

Enter fullscreen mode Exit fullscreen mode

这个想法是，我们将从主应用程序文件夹构建映像并运行`docker-compose`命令，而`run`文件夹包含开发所需的配置和本地数据库。使用`docker`卷，我们将能够在我们的主机中保存源代码、`vendor`依赖项和本地开发数据库，而所有运行时(`apache`、`php`)由容器保存和管理。

在这篇文章中，我将尽我所知解释该设置的每个部分是做什么的。TL；如果你只需要一个工作版本，DR 以及 github 链接可以在底部列出所有的变化。

## Laravel 的 web 服务器镜像

php dockerhub 中的 [`php-apache:7.2`](https://github.com/docker-library/php/blob/873725e57ec2fc5f2642dc0023676597bcc4bea9/7.2/stretch/apache/Dockerfile) 图片有现成的可配置的功能性 Apache webserver 运行`mod_php`，这是一个很好的起点。我们将需要一些扩展和一些访问控制配置，以使开发更容易(可选)。这里是`Dockerfile` :

```
FROM php:7.2-apache

RUN apt-get update

# 1\. development packages
RUN apt-get install -y \
    git \
    zip \
    curl \
    sudo \
    unzip \
    libicu-dev \
    libbz2-dev \
    libpng-dev \
    libjpeg-dev \
    libmcrypt-dev \
    libreadline-dev \
    libfreetype6-dev \
    g++

# 2\. apache configs + document root
ENV APACHE_DOCUMENT_ROOT=/var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# 3\. mod_rewrite for URL rewrite and mod_headers for .htaccess extra headers like Access-Control-Allow-Origin-
RUN a2enmod rewrite headers

# 4\. start with base php config, then add extensions
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

RUN docker-php-ext-install \
    bz2 \
    intl \
    iconv \
    bcmath \
    opcache \
    calendar \
    mbstring \
    pdo_mysql \
    zip

# 5\. composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 6\. we need a user with the same UID/GID with host user
# so when we execute CLI commands, all the host file's ownership remains intact
# otherwise command from inside container will create root-owned files and directories
ARG uid
RUN useradd -G www-data,root -u $uid -d /home/devuser devuser
RUN mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser 
```

Enter fullscreen mode Exit fullscreen mode

从 web 服务器本身开始，`php-apache` image 默认将文档根目录设置为`/var/www/html`。然而，由于拉韦尔`index.php`在`/var/www/html/public`里面，我们需要编辑 apache 配置以及站点可用。我们还将启用用于 url 匹配的`mod_rewrite`和用于配置 web 服务器头的`mod_headers`。

```
ENV APACHE_DOCUMENT_ROOT=/var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf 
```

Enter fullscreen mode Exit fullscreen mode

转到`php`配置，我们从使用已提供的`php.ini`开始，然后通过`docker-php-ext-install`添加几个扩展。执行这些任务的顺序并不重要(`php.ini`不会被覆盖)，因为加载每个扩展的配置保存在单独的文件中。

对于`composer`，我们在这里做的是从 [`composer:latest`](https://github.com/composer/docker/blob/ebbb9efd87b78cf9b837f97105bcb6c5f0ee44ef/1.8/Dockerfile) docker 映像中获取位于`/usr/bin/composer`的`composer`二进制文件。显然，你可以在标签中指定你想要的任何其他版本，而不是`latest`。这是 docker 多阶段构建功能的一部分。

```
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer 
```

Enter fullscreen mode Exit fullscreen mode

最后的步骤是可选的。因为我们要将应用程序源代码从主机装载到容器中进行开发，所以从容器 CLI 中运行的任何命令都不应该影响主机文件/文件夹的所有权。这对由`php artisan`生成的配置等很有帮助。这里我使用`ARG`让其他团队成员设置他们自己的`uid`来匹配他们的主机用户`uid`。

```
ARG uid
RUN useradd -G www-data,root -u $uid -d /home/devuser devuser
RUN mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser 
```

Enter fullscreen mode Exit fullscreen mode

## `docker-compose.yml`

web 服务器已设置。现在我们只需要使用一个`docker-compose`配置引入一个数据库容器

`docker-compose.yml`

```
version: '3.5'

services:
  laravel-app:
    build:
      context: '.'
      args:
        uid: ${UID}
    container_name: laravel-app
    environment:
      - APACHE_RUN_USER=#${UID}
      - APACHE_RUN_GROUP=#${UID}
    volumes:
      - .:/var/www/html
    ports:
      - 8000:80
    networks:
      backend:
        aliases:
          - laravel-app

  mysql-db:
    image: mysql:5.7
    container_name: mysql-db
    volumes:
      - ./run/var:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=securerootpassword
      - MYSQL_DATABASE=db
      - MYSQL_USER=dbuser
      - MYSQL_PASSWORD=secret
    networks:
      backend:
        aliases:
          - db

networks:
  backend:
    name: backend-network 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件事要处理。首先，对于 laravel 容器:

*   `build:context`是指我们刚刚写的`Dockerfile`，和`docker-compose.yml`放在同一个目录下。
*   `args`是为了我上面说的`uid`。我们将在 app `.env`文件中写入`UID`值，让`docker-compose`去取。

`.env`

```
...
MIX_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
MIX_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"

UID=1000 
```

Enter fullscreen mode Exit fullscreen mode

*   `APACHE_RUN_USER`和`APACHE_RUN_GROUP` ENV 变量带有`php-apache`。通过这样做，web 服务器生成的文件也将拥有一致的所有权。
*   `volumes`指令告诉`docker`将主机的 app 源代码挂载到`/var/www/html`中——与`apache`配置一致。这使得宿主文件的任何更改都可以反映在容器中。像`composer require`这样的命令会将`vendor`添加到主机中，所以我们不需要在每次关闭和重新启动容器时安装依赖项。
*   但是，如果您正在为 CI /远程虚拟机环境构建容器，则需要将源文件添加到容器预构建中。例如:

```
COPY . /var/www/html
RUN cd /var/www/html && composer install && php artisan key:generate 
```

Enter fullscreen mode Exit fullscreen mode

*   `ports`是可选的，如果您愿意在端口 80 下运行它，请省略。或者，可以使用类似于构建参数的`.env`进行配置:

```
ports:
  - ${HOST_PORT}:80 
```

Enter fullscreen mode Exit fullscreen mode

```
HOST_PORT=8080 
```

Enter fullscreen mode Exit fullscreen mode

*   `networks`带`aliases`也是可选的。默认情况下，`docker-compose`创建一个以父文件夹名为前缀的`default`网络来连接`docker-compose.yml`中指定的所有服务。然而，如果你有超过 1 个`docker-compose`的开发，像这样指定`networks`名称允许你从其他`docker-compose.yml`文件中加入它。`another-app`这里将能够到达`laravel-app`，反之亦然，使用指定的`aliases`。

`docker-compose.yml`

```
services:
   another-app:
    networks:
      backend:
        aliases:
          - another-app

networks:
  backend:
    external:
      name: backend-network 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看一下`mysql`:

*   [`mysql:5.7`](https://github.com/docker-library/mysql/blob/bb7ea52db4e12d3fb526450d22382d5cd8cd41ca/5.7/Dockerfile) 非常易于配置，开箱即用。所以我们不需要延长它。
*   只需选择 laravel 应用程序中的`.env`设置，即可为 db 用户设置用户名和密码:

```
environment:
   - MYSQL_ROOT_PASSWORD=securerootpassword
   - MYSQL_DATABASE=${DB_DATABASE}
   - MYSQL_USER=${DB_USERNAME}
   - MYSQL_PASSWORD=${DB_PASSWORD} 
```

Enter fullscreen mode Exit fullscreen mode

*   还要确保`.env DB_HOST`设置为 mysql-db 服务名，或者它的别名:`.env`

```
DB_HOST=mysql-db 
```

Enter fullscreen mode Exit fullscreen mode

*   理想情况下，您希望使用一系列迁移和种子将数据库更改保存在存储库中。但是，如果您想使用现有的 sql 转储启动 mysql 容器，只需挂载 SQL 文件:

```
volumes:
   - ./run/var:/var/lib/mysql
   - ./run/dump/init.sql:/docker-entrypoint-initdb.d/init.sql 
```

Enter fullscreen mode Exit fullscreen mode

*   使用`volumes`，我们将数据库本地保存在`run/var`下，因为`mysqld`写的任何数据都在容器的`/var/lib/mysql`内。我们只需要忽略`.gitignore`和`.dockerignore`中的本地数据库(用于构建上下文):

`.gitignore` :

```
/node_modules
/public/hot
/public/storage
/storage/*.key
/vendor
.env
.phpunit.result.cache
Homestead.json
Homestead.yaml
npm-debug.log
yarn-error.log

run/var 
```

Enter fullscreen mode Exit fullscreen mode

`.dockerignore` :

```
run/var 
```

Enter fullscreen mode Exit fullscreen mode

## 启动并运行

现在，让我们构建环境，并让它开始运行。我们还将安装 composer 依赖项以及一些 artisan 命令。

```
$ docker-compose build && docker-compose up -d && docker-compose logs -f
Creating network "backend-network" with the default driver
Creating mysql-db    ... done
Creating laravel-app ... done
Attaching to laravel-app, mysql-db
... 
```

Enter fullscreen mode Exit fullscreen mode

一旦所有的容器都启动并运行，我们可以通过`docker ps` :
来检查它们

```
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                  NAMES
c1ae3002d260        laravel_laravel-app   "docker-php-entrypoi…"   4 minutes ago       Up 4 minutes        0.0.0.0:8000->80/tcp   laravel-app
6f6546224051        mysql:5.7             "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes        3306/tcp               mysql-db 
```

Enter fullscreen mode Exit fullscreen mode

作曲家和工匠:

```
$ docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash" 
```

Enter fullscreen mode Exit fullscreen mode

```
devuser@c1ae3002d260:/var/www/html$ composer install
...
Generating optimized autoload files
> Illuminate\Foundation\ComposerScripts::postAutoloadDump
> @php artisan package:discover --ansi
Discovered Package: beyondcode/laravel-dump-server
Discovered Package: fideloper/proxy
Discovered Package: laravel/tinker
Discovered Package: nesbot/carbon
Discovered Package: nunomaduro/collision
Package manifest generated successfully. 
```

Enter fullscreen mode Exit fullscreen mode

```
devuser@c1ae3002d260:/var/www/html$ php artisan key:generate
Application key set successfully. 
```

Enter fullscreen mode Exit fullscreen mode

```
devuser@c1ae3002d260:/var/www/html$ php artisan migrate
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table 
```

Enter fullscreen mode Exit fullscreen mode

```
devuser@c1ae3002d260:/var/www/html$ php artisan make:auth
Authentication scaffolding generated successfully. 
```

Enter fullscreen mode Exit fullscreen mode

带主机文件:

```
127.0.0.1       laravel-app.local 
```

Enter fullscreen mode Exit fullscreen mode

我们都准备好了！
[![App Screenshot](img/e1c43210036bfd206d7adec1175642f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pX0Z2cjK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fn4w33g06gm61rwkkqoe.png) 
[![App Screenshot](img/b3b6f2146e89e985a7fae340e419e124.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Da0Uv21--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n7a4zocaqlw127rcmsoj.png)

## 助手脚本(可选)

有时，我希望能够快速运行 CLI 命令(`composer`、`artisan`等)。)而不必每次都键入`docker exec`。这里是我围绕`docker exec`编写的一些 bash 脚本:

### `container`

```
#!/bin/bash

docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash" 
```

Enter fullscreen mode Exit fullscreen mode

运行`./container`将带您进入用户`uid(1000)`(与主机用户相同)
下的`laravel-app`容器

```
$ ./container
devuser@8cf37a093502:/var/www/html$ 
```

Enter fullscreen mode Exit fullscreen mode

### `db`

```
#!/bin/bash

docker exec -it mysql-db bash -c "mysql -u dbuser -psecret db" 
```

Enter fullscreen mode Exit fullscreen mode

运行`./db`将使用 mysql 客户端连接到数据库容器的守护进程。

```
$ ./db
mysql> 
```

Enter fullscreen mode Exit fullscreen mode

### `composer`

```
#!/bin/bash

args="$@"
command="composer $args"
echo "$command"
docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash -c \"$command\"" 
```

Enter fullscreen mode Exit fullscreen mode

运行任意`composer`命令，例如:

```
$ ./composer dump-autoload
Generating optimized autoload files> Illuminate\Foundation\ComposerScripts::postAutoloadDump
> @php artisan package:discover --ansi
Discovered Package: beyondcode/laravel-dump-server
Discovered Package: fideloper/proxy
Discovered Package: laravel/tinker
Discovered Package: nesbot/carbon
Discovered Package: nunomaduro/collision
Package manifest generated successfully.
Generated optimized autoload files containing 3527 classes 
```

Enter fullscreen mode Exit fullscreen mode

### `php-artisan`

```
#!/bin/bash

args="$@"
command="php artisan $args"
echo "$command"
docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash -c \"$command\"" 
```

Enter fullscreen mode Exit fullscreen mode

运行`php artisan`命令，例如:

```
$ ./php-artisan make:controller BlogPostController --resource
php artisan make:controller BlogPostController --resource
Controller created successfully. 
```

Enter fullscreen mode Exit fullscreen mode

### `phpunit`

```
#!/bin/bash

args="$@"
command="vendor/bin/phpunit $args"
echo "$command"
docker exec -it laravel-app bash -c "sudo -u devuser /bin/bash -c \"$command\"" 
```

Enter fullscreen mode Exit fullscreen mode

运行`./vendor/bin/phpunit`执行测试，例如:

```
$ ./phpunit --group=failing
vendor/bin/phpunit --group=failing
PHPUnit 7.5.8 by Sebastian Bergmann and contributors.

Time: 34 ms, Memory: 6.00 MB

No tests executed! 
```

Enter fullscreen mode Exit fullscreen mode

## TL；速度三角形定位法(dead reckoning)

链接:

*   [储存库](https://github.com/veevidify/laravel-apache-docker)
*   [提交](https://github.com/veevidify/laravel-apache-docker/commit/48933bd10c6da6c1d3addb1b1dfc6d9b7ec4d49b)

`Dockerfile`由基本的 apache 文档根配置、mod_rewrite 和 mod_header、composer 和 sync 容器的 uid 与主机 uid 组成。

`docker-compose.yml`启动`php-apache`(挂载 app 文件)和`mysql`(挂载 db 文件)，使用`networks`互联。

使用环境:

```
$ docker-compose build && docker-compose up -d && docker-compose logs -f
$ ./composer install
$ ./php-artisan key:generate 
```

Enter fullscreen mode Exit fullscreen mode