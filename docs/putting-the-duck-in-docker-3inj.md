# 把鸭子放进码头。

> 原文：<https://dev.to/henrikklarup/putting-the-duck-in-docker-3inj>

假设您正在设置一个家庭自动化系统，并且您想在离家时访问它。记住你的 IP 地址是很难的，你的 ISP 可能会随时更换。你需要更聪明的东西。你需要 DuckDNS。

DuckDNS 允许你把你的 IP 和你选择的一个 DuckDNS 域连接起来，就像 myawesomehomeautomation.duckdns.org。现在那很酷！

但是 DuckDNS 怎么知道它应该把你导向哪个 IP 呢？它不会，至少不会通过纯粹的魔法。你需要告诉它。你可以手动这样做，但是你的 ISP 可能会改变你的 IP 地址，除非你已经付费。你需要一个服务来报告它。

但是运行一个服务是很难的，你需要安装正确的依赖项来运行这个特定的服务，并且你需要确保在它关闭的时候重启它。

幸运的是，只要你的服务器上有 docker，互联网上的某个人就能保护你。

如果你不知道 docker，请点击这里查看一下。

所以让我们开始吧。

首先进入 [DuckDNS](https://www.duckdns.org) 并登录。
在这里你可以选择你想要的域名。在这里你也可以找到你的令牌。

让我们创建一个 docker 容器。

```
docker create \
  --name=duckdns \
  -e TZ=Europe/Copenhagen \
  -e SUBDOMAINS=myawesomehomeautomation \
  -e TOKEN=<your_token_here> \
  --restart unless-stopped \
  linuxserver/duckdns 
```

现在我们有一个 docker 容器可以使用了。启动时，这将开始发送一个请求到鸭 DNS 与我们的公共 IP。它还使用 docker 来不断重新启动它，由于某种原因而不是我们自己停止它。

所以让我们运行它。

```
docker run duckdns 
```

我们走吧。

我们现在可以导航到 myawesomehomeautomation.duckdns.org，我们应该打我们自己的服务器。我们还可以看到，DuckDNS 接口中报告的 Ip 与来自像[whats MIP](https://www.whatismyip.com/)这样的站点的 IP 相匹配。