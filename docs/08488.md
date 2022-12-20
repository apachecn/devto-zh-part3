# 让我们加密的快速 SSL

> 原文：<https://dev.to/ianknighton/quick-ssl-with-let-s-encrypt-4856>

[![Quick SSL with Let's Encrypt](img/63b28e5a9515521ed9240e7eb936b2ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wXNQ9Z5N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1526374965328-7f61d4dc18c5%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

听着，我明白了...关于在几乎所有可以想象的环境中使用 Let's Encrypt 的帖子并不缺乏。

然而，当它来到我最近遇到的情况时，什么都没有...所以我要把它加到堆里。

## 问题

我将把它分解成一系列操作:

*   我在一个负载平衡器后面有两台服务器。
*   其中一台服务器的任务是更新来自“让我们加密”的证书，并将其复制到负载平衡器。
*   无论出于何种原因，该操作都会失败，证书也会过期。
*   更糟糕的是，由于我的机器最近重新格式化，我无法通过 SSH 连接到任何一台服务器。

以这样的方式开始一天，嗯？

## 解

因为我仍然拥有对 DNS 的控制权，所以我将使用`dns challenge` for Let's Encrypt 为指定的域名创建一个证书。

```
sudo certbot -d <domain-name> --manual --preferred-challenges dns certonly 
```

此命令将引导您逐步设置证书，然后为您提供一个 TXT 记录和子域，以添加到您的 DNS 记录中。一旦您确认这些更改已经完成，您就可以完成该过程，证书和密钥将被存储并复制到您的负载平衡器。

在很多情况下，比如在部署之前测试一个想法，这样做非常方便。

我希望这能像帮助我一样帮助其他人！