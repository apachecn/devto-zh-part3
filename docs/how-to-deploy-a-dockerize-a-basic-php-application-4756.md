# 如何对一个基本的 PHP 应用进行 Dockerize？

> 原文：<https://dev.to/concatly/how-to-deploy-a-dockerize-a-basic-php-application-4756>

# Docker 是什么？

Docker 是一个工具，旨在通过使用容器来简化应用程序的创建、部署和运行。容器允许开发人员将应用程序与它需要的所有部分打包在一起，比如库和其他依赖项，然后作为一个包发送出去。

# 一个基本 PHP 应用程序的 Dockerizing 步骤

## 设置所需的文件

在您的工作目录中创建一个文件夹。

将下面给出的文件添加到目录中。

**index.php**

这是一个基本的 PHP 文件，只在屏幕上打印文本。

```
<?php

        echo "Hello - This is my first docker application";

?> 
```

**Dockerfile**

```
FROM php:7.2-apache
COPY index.php /var/www/html/index.php
EXPOSE 80
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"] 
```

1.  使用了基础映像 php:7.2-apache。基本图像来自 dockerhub。
2.  文件 index.php 被复制到映像中的/var/www/html/
3.  端口 80 对 apache 是公开的。
4.  Apache 在容器内部启动。

## Docker 命令

1.  使用 docker build 命令构建映像。我们用 phpdemo 的名字和 v1 的标签构建图像。

```
docker build -t phpdemo:v1 . 
```

1.  使用 docker run 命令运行容器中的映像。我们从图像 phpdemo:v1 运行一个容器。请注意-p 标记。它用于将主机端口 9090 发布到容器端口 80。简而言之，如果任何请求来自端口 9090，它会将该请求重定向到端口 80 中的容器。

```
docker run -d -p9090:80 phpdemo:v1 
```

1.  打开浏览器，进入[http://localhost:9090/index . php](http://localhost:9090/index.php)你应该会看到 PHP 文件的输出。

# 结论

在本文中，我们讨论了如何对一个基本的 PHP 应用程序进行 dockerize。你也可以在 Concatly 上阅读更多关于 [PHP 的文章。](http://concatly.com/topics/php/)