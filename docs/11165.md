# 通过 Caddy 服务器代理对 Heroku 的请求

> 原文：<https://dev.to/scottw/proxying-requests-to-heroku-via-caddy-server-39l>

我想尝试使用 [Caddy 服务器](https://caddyserver.com)作为 Heroku 上的应用程序的反向代理。

如果你不熟悉 Caddy，它是一个用 Go 编写的新的轻量级 web 服务器，完全是为了利用 HTTPS(通过让我们加密)而构建的。Caddy 是通用 web 服务器，但目前，我只对将其用作反向代理感兴趣。

您可以通过在下面添加一个`Caddyfile`来创建一个最小版本的[反向代理](https://caddyserver.com/docs/proxy)

```
yourdomainname.com
proxy / proxieddomain.com 
```

然后你启动球童 <sup id="fnref1">[1](#fn1)</sup> ，你来自 yourdomainname.com*的请求*要代理给 proxieddomain.com*。*

 *然而，这对 Heroku 来说并不奏效。当一个请求被代理给 Heroku 时，它需要知道这个请求指向哪个应用程序。很可能，*没有在 Heroku<sup id="fnref2">T3【2】T5 上注册。</sup>*

 *如您所料，Caddy 允许您指定一些头，这些头被传递给接收代理请求的服务器。

更好的是，Caddy 有一个方便的助手，`transparent` <sup id="fnref3">[3](#fn3)</sup> ，它包装了标准的头。

使用`transparent`，我们最初的例子现在看起来像这样:

```
yourdomainname.com
proxy / proxieddomain.com {
  transparent
} 
```

为了让你跟上进度，`transparent`是<sup id="fnref1">[1](#fn1)</sup>T5】的简写

```
yourdomainname.com
proxy / proxieddomain.com {
  header_upstream Host {host}
  header_upstream X-Real-IP {remote}
  header_upstream X-Forwarded-For {remote}
  header_upstream X-Forwarded-Port {server_port}
  header_upstream X-Forwarded-Proto {scheme}
} 
```

代码越少=越好！除了这里。Heroku 失败，因为主机报头被设置为除了*proxieddomain.com*之外的任何值。这个花了我不少时间才弄明白。[非常感谢卷发！](https://dev.to/scottw/proxy-with-curl-2jg4-temp-slug-3254936)

我联系了 Heroku，他们确认主机头是内部使用的

> Heroku 路由器使用主机报头在平台上路由流量。如果您指定的主机没有在应用程序上设置为自定义域，这些类型的请求将会失败，因为它们将无法被路由。

使用`header_upstream Host {host}`将主机头设置为*yourdomainname.com*<sup id="fnref4">T4【4】T5</sup>。

但是最后，下面的代码运行并提供了`transparent`的所有好处，只是多了几行代码:

```
yourdomainname.com
proxy / proxieddomain.com {
  header_upstream X-Real-IP {remote}
  header_upstream X-Forwarded-For {remote}
  header_upstream X-Forwarded-Port {server_port}
  header_upstream X-Forwarded-Proto {scheme}
} 
```

您可能还想包含`header_upstream X-Forwarded-Host {host}`或`header_upstream Origin {host}`，以便知道哪个域实际上被代理。

* * *

1.  如果你完全是新手，检查一下入门指南，确保你首先生成了你的 SSL 证书 [↩](#fnref1)

2.  否则，为什么需要代理它呢？😀 [↩](#fnref2)

3.  "像大多数后端应用程序所期望的那样，从原始请求中传递主机信息." [↩](#fnref3)

4.  配置中的{}是[球童的占位符](https://caddyserver.com/docs/placeholders)。 [↩](#fnref4)**