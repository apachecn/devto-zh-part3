# 如何用 Docker 运行 WordPress

> 原文：<https://dev.to/lampewebdev/how-to-get-wordpress-running-with-docker-4mg6>

### 🚀先决条件

你需要有🐳 [Docker](https://www.docker.com/get-started) 安装完毕。你如何安装 docker 很大程度上取决于你的操作系统。

### 🤔选择我们的形象

WordPress Docker 图片可以在[这里](https://hub.docker.com/_/wordpress)找到。

我们将使用这个作为我们的基础图像，并扩展它。

### 🚀用 docker-compose 建立我们的形象

让我们首先创建一个新文件夹。对于本教程，
我将在`d:/dev/`中创建一个名为`wordpress`的文件夹。
在那里我们创建一个名为`docker-compose.yml`
的文件，将它复制并粘贴到文件:

```
version: '3.1'

services:

  wordpress:
    image: wordpress
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1' 
```

🤔这个`docker-compose.yml`文件是干什么的？

它将创建两个服务。服务是我们预装了 PHP 和 WordPress 的 docker 镜像。WordPress 需要 PHP 才能运行。我们告诉 docker 它应该在端口 8080 上运行，在`environment`中我们告诉 WordPress 它应该使用什么 db 凭证。

我们的第二个服务是一个`mysql`服务器，我们称之为`db`。WordPress 需要一个数据库来存储数据。在`environment`中我们告诉数据库凭证应该是。这必须与`wordpress`服务`environment`中的相同，因为 WordPress 需要这些凭证来登录并为 WordPress 的运行创建正确的数据。最后一个`environment`是为了更好的衡量。它将创建一个随机的根用户密码。

现在我们只需要在我们的`wordpress`文件夹中运行 docker-compose up。

```
docker-compose up 
```

Docker 将下载图像，然后尝试启动它们。这可能需要几分钟时间。所以要有耐心。

现在你只需要浏览到`http://localhost:8080`，你应该会看到 WordPress 提示来设置一个新的实例。

### 🚀安装 docker 文件夹

我们当前的设置有问题。当你想安装一个 WordPress 插件时，WordPress 会要求你输入一个 FTP 登录。我们真的不想那样。

另一个问题是你如何编辑 WordPress 的配置或者主题或者插件？

我们需要挂载来更改`wp-config.php`文件。
那么我们如何访问它呢？

一种方法是 ssh 进入容器，但这不是我们在这里要做的。我们将把 WordPress 文件夹安装到我们的主机系统中。

首先，我们需要在我们的`wordpress`文件夹中创建一个`html`文件夹。其次，我们需要更新我们的`docker-compse.yml`文件。
现在应该是这样的:

```
 wordpress:
    image: wordpress
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes: 
      - "D:/dev/wordpress/html:/var/www/html" 
```

我们刚刚在 wordpress 服务中添加了两行代码。
第一行是`volumes`。卷是处理 docker 映像中的文件的一种简单方法，就像它是系统上的本地文件一样。

```
 - "D:/dev/wordpress/html:/var/www/html" 
```

这一行内容如下:docker 容器中的`/var/www/html`文件夹中的内容。在`D:/dev/wordpress/html`中显示并编辑它。

再次强调`D:/dev/wordpress/html`是对我有用的东西。你可以把它放在任何你想放的地方。

现在，只需在终端中按下`ctrl+c`即可停止 docker-compose 过程。
并再次运行。

```
docker-compose up 
```

当你现在导航到`d:/dev/wordpress/html`时，你应该会看到 WordPress 文件。只需打开`wp-config.php`并在`define( 'DB_NAME', 'exampledb');`和
前添加下面一行

```
/* Make WordPress install plugins directly */
define('FS_METHOD', 'direct'); 
```

同样用这种方法，现在编辑 WordPress 主题和插件很容易。它们位于 wp-content 文件夹中。这不是很好吗？

### 🚀安装一些 WordPress 插件需要的 php 扩展

一些 WordPress 插件需要 PHP 扩展。基本映像已经安装了一些，但也许你需要更多。为此，我们需要创建自己的码头工人形象。这里我们将安装`memcached` PHP 扩展。

在`wordpress`文件夹中创建一个名为`Dockerfile`的新文件。把这个加进去保存:

```
FROM wordpress
RUN apt-get update
RUN apt-get install -y libz-dev libmemcached-dev && \
    pecl install memcached && \
    docker-php-ext-enable memcached 
```

在`FROM`行中，我们告诉 docker 将我们的图像基于我们之前使用的`wordpress`图像。第一个`RUN`简单的说我们想要更新我们正在使用的 Linux 发行版的包。在此之前，每个扩展都应该是相同的。
第二个`RUN`命令是针对`memcached`的。它安装所需的 Linux 包，然后启用 PHP 扩展。如果你需要另一个扩展，一个简单的 google 搜索就可以了，可能人们已经在某个时候用 docker 安装了这个扩展，并且已经知道需要什么样的 Linux 包。

好了，我们只需要用:
构建新的包

```
docker build -t wordpress_with_extra_extension . 
```

Yeahyyyy，你刚刚建立了一个新的码头工人形象！酷吧？
现在我们只需要告诉我们的`docker-compose.yml`文件使用来自
的
图像

```
 ...
  wordpress:
    image: wordpress
  ... 
```

至

```
 ...
  wordpress:
    image: wordpress_with_extra_extension
  ... 
```

就是这样！现在只要停下来跑一跑`docker-compose up`就万事大吉了！

**感谢阅读！**

说你好！[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)