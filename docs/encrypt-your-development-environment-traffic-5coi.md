# 加密您的开发环境流量

> 原文：<https://dev.to/mmollick/encrypt-your-development-environment-traffic-5coi>

让您的开发环境忠实于您的生产环境可以确保您的应用程序按预期工作。然而，我们的开发环境经常伴随着警告和缺点，导致我们否定特定的设置、特性或功能。这些经常被忽视的特性之一是通过 HTTPS 为您的应用程序提供服务的能力。

虽然对来自本地 Docker 容器的流量进行加密可能不是首要任务，但确保您的应用程序通过正确的协议加载其所有资产和数据才是首要任务。在`<link>`或`<script>`标签中硬编码的`HTTP://` URL 可能会破坏你的应用程序的风格，或者根本阻止脚本的加载。防止这种情况的最好方法是在开发期间通过 HTTPS 加载您的应用程序。

要在本地使用 HTTPS 加载您的应用程序，我们必须生成 SSL 证书。在生产环境中，您可能会使用证书颁发机构(CA)来生成可信的证书，如[让我们加密](https://letsencrypt.org/)或几十个付费 CA 之一。

虽然可以在您的生产环境中重用相同的证书，但是传递您的生产证书和证书的私钥会带来很大的安全风险。最好不要在本地开发机器上使用您的产品证书和私钥。

在本地启用 HTTPS 的最佳解决方案是使用自签名证书。您的浏览器本身不会信任这个自签名证书，很可能会告诉您“您的连接不是私有的”在你不控制的网站上遇到这种信息，你不应该忽视。但是，在这种情况下，您可以控制证书的来源和服务器本身，因此您可以安全地绕过这个屏幕。

虽然这些原则适用于任何环境或堆栈；本文解释了如何使用 Docker 和 Nginx 创建自签名证书。如果您对 Docker 的具体示例不感兴趣，请跳到文章末尾。

## Docker 示例

使用 Docker，我们有两个创建自签名证书的选项。第一种选择是在本地机器上生成证书，并在构建过程中将它们复制到容器中。第二种是在构建过程中创建证书。

### 从本地机器复制证书。

我不会详细讨论生成自签名证书的过程，因为已经详细解释过很多次了。在文章的最后，你会找到更多的链接来详细解释这个过程。

要创建新的自签名证书，您首先需要确保 OpenSSL 在您的机器上可用。如果您运行的是 Unix 机器(Linux 或 MacOS)，它可能已经安装了。要确保它可用，请运行以下命令:

```
$ openssl version 
```

根据您的平台，该命令应该输出类似“LibreSSL 2.6.5”或“OpenSSL 1 . 1 . 1 a 2018 年 11 月 20 日”的内容。如果您收到一条消息说“找不到命令”，您需要在继续之前安装 OpenSSL。一旦验证了 OpenSSL 可用，就可以在终端中运行以下命令，并按照交互式提示进行操作。

```
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout PROJECT_DIRECTORY/nginx.key -out PROJECT_DIRECTORY/nginx.crt 
```

> 将“PROJECT_DIRECTORY”替换为 docker 文件所在的目录。

一旦完成，你会留下一个`nginx.key`和`nginx.crt`文件。您可以修改 docker 文件，将这两个文件复制到您的容器中。这一步如下所示，有两行以`ADD PROJECT_DIRECTORY/...`和
开始

```
FROM nginx:latest

# Adds certificates from local machine to docker container
ADD PROJECT_DIRECTORY/nginx.key /etc/nginx/ssl/nginx.key
ADD PROJECT_DIRECTORY/nginx.crt /etc/nginx/ssl/nginx.crt

# Assumes servers/vhosts are defined in /etc/nginx/conf.d/
ADD PROJECT_DIRECTORY/vhost.conf /etc/nginx/conf.d/default.conf 
```

有了 Docker 容器中的证书，最后一步是更新 Nginx 服务器配置以使用证书。下面在我们的`vhost.conf`文件中使用`ssl_certificate`和`ssl_certificate_key`选项演示了这个步骤。

```
server {
    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;

    index index.php index.html;
    root /var/www/public;

    # Self-signed certificate setup
    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;

    # Example PHP application
    location / {
        try_files $uri /index.php?$args;
    }

    # Example PHP application upstream
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
} 
```

如果您正在独自开发项目，此选项非常有用。然而，如果你和其他几个开发者一起工作，他们也需要证书。每个开发人员都可以生成一个本地证书来使用，但是需要在设置过程中引入额外的步骤。更好的方法是将证书签入源代码控制，尽管还有另一个选项可以使这个过程更容易。

### 用 Docker Build 生成证书

我发现的最佳解决方案是在构建过程中用 Dockerfile 生成证书。这种方法允许每个开发人员拥有只有他们的机器才知道的证书，并且消除了在源代码控制中管理证书过期的负担。

如果开发人员遇到过期的证书，他们可以用一个命令重新构建 Docker 容器来获得新的证书。以前的方法需要团队中的某个人在本地生成一个新的证书，将其提交给版本控制，然后重新构建 docker 容器。

为此，我们将修改前面的 docker 文件，如下所示:

```
FROM nginx:latest

# Ensure OpenSSL is available and generate the certificate chain
RUN apt-get update && apt-get install -y openssl\
    && mkdir /etc/nginx/ssl\
    && openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt -subj "/C=US/ST=Ohio/L=Cleveland/O=X/CN=www.example.com"

# Assumes servers/vhosts are defined in /etc/nginx/conf.d/
ADD PROJECT_DIRECTORY/vhost.conf /etc/nginx/conf.d/default.conf 
```

我们现在安装 OpenSSL(如果还不可用)并在容器上生成证书，而不是用`ADD`命令复制静态证书。OpenSSL 通常要求我们在发布证书链之前回答几个问题。然而，`-subj`标志允许我们在运行命令时指定这些问题的答案。这样，我们现在可以创建“无人值守”的自签名证书。在这个场景中，我们将使用上一个例子中相同的 Nginx 配置。

## TL；速度三角形定位法(dead reckoning)

将自签名证书用于本地开发，并在开发环境设置期间自动生成它们。使用以下命令生成无人参与的证书(无提示)。

```
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt -subj "/C=US/ST=Ohio/L=Cleveland/O=X/CN=www.example.com" 
```