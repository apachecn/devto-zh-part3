# 使用 NGINX 构建反向代理

> 原文：<https://dev.to/akrami/use-nginx-to-build-a-reverse-proxy-2koe>

代理服务器是一个中介服务器，它会将来自客户端的每个请求转发到不同的目的地。目标服务器不能直接看到客户端，所以它们不知道关于这些客户端的任何事情。

接受来自客户端的请求后，代理服务器将为该请求分配一个端口，并将该请求转发到其目的地。在该端口上收到回复后，响应将通过之前的连接转发给客户端。

[![proxy server](img/2800d7aabc84447415521e173044bfc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LYIxK5iF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://iakrami.ir/assets/img/posts/proxy_server.png)

与代理服务器不同，反向代理服务器位于 web 服务器旁边。客户端将反向代理后面的 web 服务器视为一个服务器，无法区分它们。

[![reverse proxy server](img/ace30982d3fde012b094ead6ab653a59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_c7ys_Pf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://iakrami.ir/assets/img/posts/reverse_proxy.png)

反向代理将请求转发给一个或多个处理它们的普通服务器。来自代理服务器的响应被返回，就好像它直接来自原始服务器一样，使得客户端不知道原始服务器。

#### 反向代理服务器的常见用途:

##### 负载均衡

反向代理服务器可以充当后端服务器前面的负载平衡器，以最大化速度和容量利用率的方式在一组服务器之间分发客户端请求，同时确保它们都不会过载。如果其中一个发生故障，负载平衡器会将流量重定向到其余的活动负载。

##### 网页加速

反向代理可以压缩请求，并缓存通常请求的内容，从而加快客户端和服务器之间的流量流动。

##### 加密/ SSL 加速

SSL 加密通常不是由 web 服务器完成的，而是由反向代理来处理。这样，所有跨 web 服务器的服务都通过一个配置进行加密和保护。

## 安装 NGINX

在我们撰写本文时，NGINX 的当前版本是`1.14.2`。

#### CentOS/RHEL:

1.  安装 EPEL 存储库:

```
$ sudo yum install epel-release 
```

Enter fullscreen mode Exit fullscreen mode

1.  更新存储库:

```
$ sudo yum update 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装 NGINX 开源:

```
$ sudo yum install nginx 
```

Enter fullscreen mode Exit fullscreen mode

1.  验证安装:

```
$ sudo nginx -v 
```

Enter fullscreen mode Exit fullscreen mode

#### Debian/Ubuntu:

1.  更新 Debian 仓库信息:

```
$ sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装 NGINX 开源包:

```
$ sudo apt-get install nginx 
```

Enter fullscreen mode Exit fullscreen mode

1.  验证安装:

```
$ sudo nginx -v 
```

Enter fullscreen mode Exit fullscreen mode

其他安装方法和操作系统可在此链接获得:[安装 NGINX 开源](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)

## 配置 NGINX

假设我们在服务器上有两个主要服务。一个由 apache 在端口 8080 上托管，另一个是在端口 8090 上的 nodejs 服务。

第一个服务的 URL 如下:

```
http://localhost:8080/login
http://localhost:8080/home
http://localhost:8080/assets/css/home.css
http://localhost:8080/assets/js/home.js 
```

Enter fullscreen mode Exit fullscreen mode

下面是第二个服务的 URL:

```
http://localhost:8090/api
http://localhost:8090/api/getAll
http://localhost:8090/api/set 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在来看看我们的配置。这个文件的主位置是`/etc/nginx/nginx.conf`。如果找不到，就创建它:

```
server {
    listen 80;
    listen [::]:80;

    server_name example.com; 
} 
```

Enter fullscreen mode Exit fullscreen mode

你应该把`example.com`换成自己的域名。

我们已经在`line 2`的端口 80 上定义了一个新的服务器。`server_name`是我们的域名。现在我们想定义反向代理中的 URL 规则。就在`server_name`后面加上这几行。

```
location /app {
    proxy_pass http://localhost:8080/;
}
location /api {
    proxy_pass http://localhost:8090/api/;
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，如果 URL 以`/app`开头，请求将被转发到第一个服务。

```
http://localhost/app/login => http://localhost:8080/login
http://localhost/app/home => http://localhost:8080/home
http://localhost/api/getAll => http://localhost:8090/api/getAll
http://localhost/app/xxxx => http://localhost:8080/xxxx
http://localhost/api/xxxx => http://localhost:8090/api/xxxx 
```

Enter fullscreen mode Exit fullscreen mode

## 高级配置

实时 web 应用程序通常不缓冲数据，所以我们可能还想在反向代理服务器上禁用它。

```
location /api {
    proxy_pass http://localhost:8090/api/;
    proxy_buffering off;
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能还需要为请求设置或添加标题:

```
location /app {
    proxy_pass http://localhost:8080/;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $host;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个配置使用内置的`$remote_addr`变量将原始客户机的 IP 地址发送给代理主机。

默认情况下，请求的主机头不会被转发，而是根据`proxy_pass`语句进行设置。

您可以使用正则表达式来定义自定义位置:

```
location ~ ^/app/css/(.*)$ {
    proxy_pass http://localhost:8080/assets/css/$1;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
http://localhost/app/css/home.css => http://localhost:8080/assets/css/home.css 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用`rewrite` :

```
location /app/ {
    rewrite ^/app/dashboard/(.*)$ /app/home?path=$1 break;
    proxy_pass http://localhost:8080/;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果命中重写规则，则忽略指令中指定的 URI，并将完整的已更改请求 URI 传递给服务器:

```
http://localhost/app/dashboard/addUser => http://localhost:8080/home?path=addUser
http://localhost/app/assets/css/home.css => http://localhost:8080/assets/css/home.css 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 NGINX 负载均衡器

定义您希望在其上平衡请求负载的服务器:

```
upstream backend {
    server 10.1.0.101; 
    server 10.1.0.102;
    server 10.1.0.103;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在反向代理的参数
中使用负载平衡器

```
server {
    listen 80; 

    location / {
       proxy_pass http://backend;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用 NGINX 进行 SSL 加密

首先，这样定义认证:

```
http {
    ssl_certificate /root/certs/example.com/example.com.crt;
    ssl_certificate_key /root/certs/example.com/example.com.key;
    ssl_ciphers EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH;
    ssl_protocols TLSv1.1 TLSv1.2; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以通过以下方式为您的服务器启用 HTTPS:

```
server {
    listen 203.0.113.30:443 ssl;
    listen [2001:DB8::5]:443 ssl;
    server_name example1.com www.example1.com;
    root /var/www/example1.com;
}
server {
    listen 203.0.113.40:443 ssl;
    listen [2001:DB8::6]:443 ssl;
    server_name example2.com www.example2.com;
    root /var/www/example2.com;
} 
```

Enter fullscreen mode Exit fullscreen mode