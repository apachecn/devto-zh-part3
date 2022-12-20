# 欢迎来到 Nginx！(第一部分)

> 原文：<https://dev.to/rshiva/welcome-to-nginx-part-i-58mn>

# Nginx 是什么？

根据 Wiki 的说法，Nginx 是一个开源 web 服务器，也可以用作反向代理、负载平衡器、邮件代理和 HTTP 缓存。该软件由伊戈尔·塞索耶夫创作，并于 2004 年首次公开发布。Nginx 声称运行着互联网上 60%最繁忙的网站。
我们主要将 Nginx 用作 web 服务器(Rails 或 golang 应用程序的乘客)和负载平衡器。

# Ubuntu 中如何安装 Nginx？

安装 Nginx 有两种方法，一种是从软件包安装，另一种是从源代码安装(推荐)

1.  从软件包安装非常简单。这将安装最新的稳定版本

    ```
    $ sudo apt-get install nginx

    $ sudo nginx -v
      nginx version: nginx/1.10.2 
    ```

2.  从源代码安装 Nginx。在安装 Nginx 之前，我们需要安装一些依赖项

    ```
    $ sudo apt-get install libpcre3-dev build-essential libssl-dev 
    ```

    1.  这基本上是一个正则表达式库
    2.  libssl-dev ->这个包是用于安全连接的 ssl 和 TLS 的 OpenSSL 实现的一部分
    3.  [build-essential](https://packages.ubuntu.com/xenial/build-essential)->本包包含 GNU C++编译器、GNU C 编译器等库

你可以安装在任何你喜欢的位置，通常安装在/opt 或者/etc

```
 $ cd /etc/

    $ sudo wget http://nginx.org/download/nginx-1.10.2.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

解压缩 tar 文件

```
 $ sudo tar -zxvf nginx-1.10.2.tar.gz

    $ cd /etc/nginx-1.10.2 
```

Enter fullscreen mode Exit fullscreen mode

配置版本

```
 $ sudo ./configure --prefix=/etc/nginx --user=nginx --group=nginx --with-http_ssl_module

    Configuration summary
  + using system PCRE library
  + using system OpenSSL library
  + md5: using OpenSSL library
  + sha1: using OpenSSL library
  + using system zlib library

  nginx path prefix: "/etc/nginx"
  nginx binary file: "/etc/nginx/sbin/nginx"
  nginx modules path: "/etc/nginx/modules"
  nginx configuration prefix: "/etc/nginx/conf"
  nginx configuration file: "/etc/nginx/conf/nginx.conf"
  nginx pid file: "/etc/nginx/logs/nginx.pid"
  nginx error log file: "/etc/nginx/logs/error.log"
  nginx http access log file: "/etc/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp" 
```

Enter fullscreen mode Exit fullscreen mode

让我们用上面的配置来构建和安装

```
 $ sudo make 
    $ sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为 Nginx 创建一个单独的用户和组

```
 $ sudo adduser --system --no-create-home --disabled-login --disabled-password --group nginx 
```

Enter fullscreen mode Exit fullscreen mode

从源安装不包括用于在服务器重启期间启动和停止 Nginx 的 init 文件

```
 $ sudo vi /etc/init.d/nginx 
```