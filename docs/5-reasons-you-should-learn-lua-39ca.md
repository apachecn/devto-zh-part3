# 你应该学习 Lua 的 5 个理由

> 原文：<https://dev.to/jwoertink/5-reasons-you-should-learn-lua-39ca>

如果你没听说过 [Lua](https://www.lua.org/) ，那是一种真的很小的编程语言。也许你听说过它，但是没有理由去学习它，因为你选择的编程语言做了你需要它做的一切。无论如何，这里有一些你应该学习 Lua 的理由。

## 5。

你听过/用过 [nginx](https://www.nginx.com/) 吗？你可以通过 [openresty 设置](https://github.com/openresty/lua-nginx-module)在 nginx 内部使用迷你 Lua 脚本。这将允许你做的事情，如动态分配 SSL 证书，通过让我们加密到不同的领域。

```
auto_ssl:set("allow_domain", function(domain)
  return ngx.re.match(domain, "^(mysite.com|yoursite.com)$", "ijo")
end) 
```

Enter fullscreen mode Exit fullscreen mode

用 Apahce 代替 nginx？Lua 仍然为您提供[服务](http://httpd.apache.org/docs/trunk/developer/lua.html)。

## 4。

网络中使用的另一个流行工具是 Redis 。如果使用 redis 来存储时间线事件 id 列表之类的东西，可能需要对这些数据进行一些疯狂的排序。在 redis 中写语句可能有点疯狂，有时你选择的语言可能没有你希望的那么快。例如，如果你使用红宝石，有一种宝石叫做[金刚狼](https://github.com/Shopify/wolverine)。您编写一个 Lua 脚本，完成所有您需要的疯狂排序，然后将数据传回您的 rails 应用程序。这给了你一个巨大的性能提升。

## 3。

如果您的 web 应用程序需要大量的性能，但您没有能力重写不同的内容，那么您可以在应用程序中使用大量缓存。一种方法是在你的应用程序前涂上清漆。Varnish 将处理所有的缓存，并且仅在缓存过期时代理回您的应用程序。

Varnish 使用这些 vcl 脚本，这可能会有点令人困惑。用一点点 lua 魔法你可以编写一些不错的编程缓存。在多租户的情况下，可能基于域缓存不同的长度？

## 2。

你喜欢玩电子游戏吗？也许你听说过其中的一些？

*   愤怒的小鸟
*   文明 V
*   相差甚远
*   加里的模型
*   魔法门之英雄无敌 V
*   黑洛杉矶
*   黑手党 2
*   罗布乐思
*   圣徒 2 排
*   模拟城市 4
*   星球大战前线
*   史蒂芬妮
*   战锤
*   魔兽世界

所有这些游戏都在某种程度上使用 Lua，还有更多。大部分游戏或者游戏引擎都会用 C++或者 C#编写，但是在前端使用 Lua 进行脚本编写，可以让这些游戏快速开发。不需要每次都重新编译。编译您的引擎，并让它运行 lua 脚本。可以从 [Love2d](https://love2d.org/) 或者 [defold](https://www.defold.com) 入手

## 1。

因为 Lua 写起来很好玩！如果你不懂，学一门新语言也无妨。如果你已经知道如何编程，学习 lua 应该会很快。通读这段 lua 的 [15 分钟，你会对语法有一个不错的理解。在这一点上，这只是一个挖掘的问题！](http://tylerneylon.com/a/learn-lua/)