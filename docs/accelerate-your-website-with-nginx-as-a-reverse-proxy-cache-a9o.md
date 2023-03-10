# 使用 Nginx 作为反向代理缓存加速您的网站

> 原文：<https://dev.to/shameemreza/accelerate-your-website-with-nginx-as-a-reverse-proxy-cache-a9o>

优化网页性能是一个悬而未决的问题，我们必须继续努力。有多种解决方案可以缩短我们的响应时间。

## 什么是反向代理缓存？

在计算中，反向代理是一种位于客户端和服务器或 web 服务器之间的服务器，用于检索这些服务器的资源。这些资源被返回给客户端，就像是由 web 服务器本身返回的一样。基本上，它是一个可以扮演不同角色的中介。

我们可以为这个代理提供的可能应用是，将它用作内部网络的防火墙、负载平衡器、应用 AB 测试的工具或本文的原因、静态缓存服务器。

[![static cache server](img/5db0c0b5d83b921194073f04a3a4000a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dP3ZR3SM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wvk7ptlr3ohmm4l6yb7k.png) 
图片来源:隐私加拿大(【https://privacycanada.net】T4)

基本上，该服务器用作缓存，如果它没有所请求的资源，则咨询 web 服务器，发生未命中，并且该资源存储在内存中。对同一资源的下一个请求由代理本身以一种非常有效的方式提供服务，在给定的 TTL 期间产生命中并避免调用 web 服务器。通过这种方式，我们可以安装一个集中的静态服务器，从这里我们可以提供所有的静态内容。

有不同的技术可以让我们实现这个场景。最著名的工具之一是 Varnish，但是除了 web 服务器之外，Nginx 也可以配置为反向代理缓存。阅读了几篇关于比较和基准测试的文章后，差别已经很小了，所以今天我们将重点讨论 Nginx-Nginx 场景。

然而，我们可以用不同的组合来表示相同的场景:Varnish-Apache、Varnish-Nginx 或 Nginx-Apache，其中第一个是缓存服务器，第二个是 web 服务器。

## 为什么要 Nginx？

像 Varnish 一样，Nginx 能够充当 web 缓存，但并不是所有的系统管理员都知道这一点。Nginx 可以以非常高效的方式直接提供静态内容，并可以充当前端缓存，就像 Varnish 一样。虽然 Varnish 的唯一任务是充当具有高级选项的缓存，但 Nginx 是多功能的，并提供了几种选择。

事实是，我不知道应该拒绝这两个选项中的哪一个，但是决定只使用 nginx，并不是为了在我们的基础设施中引入新的技术来学习和维护，而且 nginx 的配置似乎比 Varnish 的配置简单得多。

## 安装基础设施

**安装 engine**

要在我们的服务器上安装 nginx，我们只需执行以下命令:

```
sudo apt-get install nginx 
```

默认情况下，nginx 将安装在您的系统上，监听端口 80，提供一个静态的欢迎 html。我们来看看如何配置 nginx。

**配置网络服务器**

首先，我们必须考虑到，将读取我们的文件系统资源的 web 服务器不应该再监听端口 80，因为代理将被替换。

我们将创建一个文件夹来存放我们的资源:

```
mkdir /var/www/assets 
```

之后，我们将配置 nginx，使其监听端口 81，该端口将我们的资源目录定义为 root，并且包括 12 小时的资源过期时间。这可以通过允许配置的正则表达式来配置。为此，我们生成以下文件/etc/nginx/sites-available/static-server

```
server {
listen 81;
server_name {{tu ip o subdominio}};
access_log /var/log/nginx/static-server.log;
location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc)$ {
expires 12h;
root /var/www/assets;
add_header Cache-Control "public";
}
} 
```

最后，我们将把它移到/etc/nginx/sites-enabled/static-server。如果我们现在通过端口 81 向一个资源发出请求，我们应该能够接收到它。

## 配置代理缓存

为了将 nginx 配置为缓存，我们将使用 proxy_cache_path 指令。该指令指示您的机器的一个目录，所有被缓存的资源将存储在该目录中。

该目录应该包含 www-data 组和 700 权限，以便代理可以正确写入。此外，该指令还指示 keys_zone 标识符，该标识符定义了名称、max_size 缓存的最大大小以及缓存中文件夹层次结构的间接级别。

我们还将依赖另一个 proxy_cache_key 指令，它是存储缓存资源的关键。基本上 nginx 对这个结构进行哈希处理，这样就可以选择缓存的级别，例如，它是否在 url 中使用相同的参数。

一旦我们清除了这两个指令，我们将指示代理监听端口 80，并且任何到来的请求检查我们已经定义的缓存区域，否则用 proxy_pass 指令检查我们的 web 服务器监听端口 81

此外，Nginx 还包含一个名为 X-Proxy-Cache 的头，它告诉客户端资源是由缓存返回(命中)还是必须咨询 web 服务器(未命中)。该信息对于调试一切正常非常有趣。

现在，我将离开缓存服务器的完整配置:

```
#/etc/nginx/sites-enabled/caching-server
proxy_cache_path /tmp/nginx levels=1:2 keys_zone=assets_zone:10m inactive=60m;
proxy_cache_key "$scheme$request_method$host$request_uri";
server {
listen 80;
server_name {{tu ip o subdominio}};
access_log /var/log/nginx/caching-server.log;
location /static/ {
proxy_cache assets_zone;
add_header X-Proxy-Cache $upstream_cache_status;
include proxy_params;
proxy_pass http://localhost:81/;
}
} 
```

我希望这篇文章是有趣和有用的，我希望你能告诉我们你是如何优化你的网站的，以及你所面临的问题和你所实施的解决方案。

**相关链接:**

[https://serversforhackers.com/nginx-caching](https://serversforhackers.com/nginx-caching)

[https://www . digital ocean . com/community/tutorials/understanding-nginx-http-代理-负载平衡-缓冲-缓存](https://www.digitalocean.com/community/tutorials/understanding-nginx-http-proxying-load-balancing-buffering-and-caching)