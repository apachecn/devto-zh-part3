# Python 设置 linux 代理

> 原文：<https://dev.to/theredspy15/python-set-linux-proxy-2io8>

因此，我正在为一个树莓派的 Python 项目工作，该项目将每天从一个网站下载代理列表。

然而，每当我查找/谷歌如何设置系统代理，我无法实现任何东西。当我加载 firefox 时，我的 IP 地址总是一样的。

列表中的代理是 http、https、socks4 和 socks5。

有人知道在 Linux 上通过 Python(Debian)根据代理类型动态设置**系统**代理的方法吗？

我目前拥有的:

```
os.environ[str(proxyData["protocol"]) + "_proxy"] = str(proxyData["ip"]) + ":" + str(proxyData["port"]) 
```

Enter fullscreen mode Exit fullscreen mode

**编辑:**
我拍什么:
客户端->(openvpn)->Pi->(Python/Proxy)->互联网