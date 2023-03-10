# hastatic:Docker 的小型静态内容 web 服务器

> 原文：<https://dev.to/abhin4v/hastatic-a-tiny-static-content-web-server-for-docker-5139>

我为 Docker 编写了这个非常小的静态内容 web 服务器。只需使用它作为基础图像来添加您的网站内容，您就可以获得一个包含服务器的 Docker 网站图像！它也充满了各种功能。

看看这个:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿宾 4v ](https://github.com/abhin4v) / [哈斯塔蒂克](https://github.com/abhin4v/hastatic)

### hastatic 是 Docker 的一个小型静态内容 web 服务器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 哈斯塔蒂克

[![Docker Build Status](img/74f3a9dd2e4c43f09fbf647e21ebb925.png)](https://hub.docker.com/r/abhin4v/hastatic/)[![Docker Pulls](img/856410adc0dde0dde74e60b7153fe67f.png)](https://hub.docker.com/r/abhin4v/hastatic/)[![MicroBadger Size](img/8a9dc25c1fc392f2a712fba2035c7e18.png)](https://hub.docker.com/r/abhin4v/hastatic/)

hastatic 是一个非常小的 web 服务器，用于从 Docker 容器中提供静态文件。

## 特征

*   一个轻量级的网络服务器，只有 5 MB 大小。
*   [为码头工人](https://hub.docker.com/r/abhin4v/hastatic/)建造。
*   支持 HTTPS。
*   支持自定义 404 文件。
*   支持以“/”结尾的 URL 的自定义索引文件。
*   注意不要提供隐藏文件。
*   自动添加缓存头。
*   自动添加安全标头。
*   缓存文件描述符和信息以获得更好的性能。

## 使用

为你的网站创建一个 docker 文件，从`abhin4v/hastatic`派生:

```
FROM abhin4v/hastatic:latest
COPY mywebsite /opt/mywebsite
WORKDIR /opt/mywebsite
CMD ["/usr/bin/hastatic"]
```

构建并运行:

```
$ docker build -t mywebsite .
$ # run with default configs
$ docker run -p 8080:3000 mywebsite
$ # run with custom configs
$ docker run -e PORT=2000 -e NF_FILE=404.html -e IDX_FILE=index.html -p 8080:2000 mywebsite
$ # run with HTTPS support
$ docker run -e TLS_CERT_FILE=certificate.pem -e TLS_KEY_FILE=key.pem -p 443:3000 mywebsite
```

## 配置

Docker 图像支持…

</article>

[View on GitHub](https://github.com/abhin4v/hastatic)