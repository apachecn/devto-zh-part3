# 在本地开发中管理多个应用程序 URL

> 原文：<https://dev.to/amree/manage-multiple-app-url-in-local-development-2f12>

如今，我们可能有多个本地应用程序运行在不同的端口上(例如:`http://localhost:3000, http://localhost:3001`等等)。这非常麻烦，因为我们必须记住哪个应用程序运行在哪个端口上。

`haproxy`可以通过不同的端口将 URL 重定向到某个主机(这在`hosts`中是不可能的)。以下是我们在 OS X 的做法:

安装`haproxy`

```
brew install haproxy 
```

Enter fullscreen mode Exit fullscreen mode

在`/usr/local/etc/haproxy.cfg`创建一个新文件，并放入这些内容(根据您的意愿定制):

```
global
  maxconn 4096
  daemon

defaults
  log global
  mode http

  timeout connect 5000ms
  timeout client  50000ms
  timeout server  50000ms

frontend web_gateway
  bind *:80

  # https://cbonte.github.io/haproxy-dconv/1.7/configuration.html#7.1
  acl is_web hdr_beg(host) myapp.local
  acl is_api hdr(host) -i api.myapp.local
  acl is_admin hdr(host) -i admin.myapp.local

  use_backend web if is_web
  use_backend api if is_api
  use_backend admin if is_admin

backend web
  # https://cbonte.github.io/haproxy-dconv/1.7/configuration.html#server
  server web 127.0.0.1:3000

backend api
  server api 127.0.0.1:3001

backend admin
  server admin 127.0.0.1:3002 
```

Enter fullscreen mode Exit fullscreen mode

确保将之前声明的主机放入`/etc/hosts` :

```
127.0.0.1  localhost
127.0.0.1  myapp.local.sg # We can use different domain here
127.0.0.1  myapp.local.hk
127.0.0.1  admin.myapp.local
127.0.0.1  api.myapp.local 
```

Enter fullscreen mode Exit fullscreen mode

运行该命令以确保`haproxy`将一直运行

```
brew services start haproxy 
```

Enter fullscreen mode Exit fullscreen mode