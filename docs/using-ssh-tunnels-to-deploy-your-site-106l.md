# 使用 SSH 隧道部署您的站点

> 原文：<https://dev.to/emceeaich/using-ssh-tunnels-to-deploy-your-site-106l>

注:*更新于 2019-09-09*

即使在云时代，在开放的互联网上运行本地服务仍然是一件事。

例如，我有一组使用 [Corsica](https://github.com/mozilla/corsica) 的本地仪表盘，我想在路上访问它们。您可能希望向客户端显示正在进行的工作，而不必担心部署到外部主机。你可能想连接到一个你运行的安全摄像头连接到一个树莓派。或者在非特权端口上运行 web 应用程序。

很长一段时间以来，这样做的方法是在你的路由器上设置端口转发，并使用个人 DNS 服务。端口转发需要路由器配置，并限制您向 web 公开一项服务，您的 ISP 很可能会阻塞端口 80 和 443。

有了 SSH 隧道，您可以将您的服务公开给互联网，而不需要麻烦的路由器配置:

```
ssh -R 80:localhost:8080 external.example.com 
```

你在`localhost:8080`的服务将会出现在[http://external.example.com](http://external.example.com)的网站上。

主机`external.example.com`需要能够从公共互联网访问。您可以在云中托管自己的端点，但是 SSH 隧道的端点可以作为服务从几个提供商那里获得。

以下是我对我尝试过的两种服务的记录。

## Pagekite

Pagekite 是一项付费服务。该公司称这些隧道为“风筝”，并对它们使用的带宽进行计量。

要使用它，你安装他们的[脚本](https://pagekite.net/pk/pagekite.py)，或者[一个包](https://pagekite.net/pk/pagekite.py)并运行:

```
pagekite.py 8080 yourname.pagekite.me 
```

您的服务将在[https://your name . page kite . me](https://yourname.pagekite.me)提供。

如果您没有帐户，脚本会提示您设置一个帐户。Pagekite 会帮你监视隧道。它们为[在引导时使用 rc 脚本](https://pagekite.net/wiki/Howto/GNULinux/ConfigureYourSystem/)启动服务提供文档。同样，您必须在启动隧道之前启动服务。

如果您只想托管一组静态文件:

```
pagekite.py /path/to/folder yourname.pagekite.me 
```

它们通过在默认情况下阻止对`/wp-admin/`和类似路径的请求，为所谓的“路过”攻击提供[内置防御。您还可以添加密码，或通过 IP 地址限制访问(对于物联网摄像头示例很有用。)](https://pagekite.net/support/security/)

对于 6 台主机和 150，000 Mb 的传输，我每年支付 40 美元。他们还为物联网设备制造商提供白标服务。

## 服务

Serveo 的港口转运服务是免费的。它是由它的创造者 [@trevordixon](https://twitter.com/trevordixon) 提供的。要将它用于在端口 3000 上运行的服务，请运行命令:

```
ssh -R 80:localhost:3000 serveo.net 
```

哪个会返回:

```
Hi there
Forwarding HTTP traffic from https://randomname.serveo.net
Press g to start a GUI session and ctrl-c to quit. 
```

注意，产生的 url 是`https`，所以您不必设置自己的证书。

您可以在 ssh 命令中指定一个子域。

```
ssh -R emma:80:localhost:8888 serveo.net 
```

如果子域`emma`可用，您可以在`https://emma.serveo.net`联系到。

使用类似于`autossh`和启动脚本的工具，您可以确保您的服务在重启之间存活。请记住，您还需要启动您正在隧道化的服务。这里有一整篇[关于用 systemd](https://www.everythingcli.org/ssh-tunnelling-for-fun-and-profit-autossh/) you 设置`autossh`的帖子，在这里很有用。

Serveo 的易用性也是它的缺陷。任何人都可以用它来设置转发，serveo.net 证书是一个通配符，所以如果浏览器信任你使用 Serveo 的服务，它也会信任使用它来转发的所有其他服务。

## 其他服务

在这篇文章首次发表后，京发表了一篇关于其他免费隧道服务的文章。

## 告诫

一个隧道只能和终点一样好。您必须信任服务的端点。如果它妥协了，那么你必须考虑你的服务妥协了。以前，当心怀不轨的人利用这些服务来传播恶意软件时，谷歌已经将这些服务列入黑名单。需要帐户的服务可能不是自由的，但是有一定的责任。