# 足迹的第一步

> 原文：<https://dev.to/danielmutu/first-steps-for-footprinting-4fi4>

来源:[https://www . Amazon . com/Hacker-7-0-Stuart-McClure/DP/8850332009/ref = Sr _ 1 _ 4？ie = UTF8&qid = 1538512511&Sr = 8-4&关键词=黑客](https://www.amazon.com/Hacker-7-0-Stuart-McClure/dp/8850332009/ref=sr_1_4?ie=UTF8&qid=1538512511&sr=8-4&keywords=hacking)

最近，我加深了对足迹的了解，特别是关于 Tor 和 Privoxy。Tor 和 Privoxy 是两个帮助用户在网络上匿名的软件。

一些技巧:

*   不要用 torrent 下载。
*   如果不在虚拟机中，请不要打开 PDF，DOC。
*   不要调整窗口大小(你可以通过屏幕大小跟踪)。
*   不要启用插件。
*   你必须使用 HTTPS 连接。

Tor 允许你通过 TCP 隐藏你的 IP 地址。
它可以在:[https://www.torproject.org/](https://www.torproject.org/)T3】如果安装正确，监听本地端口 9050。

第二个工具是 Privoxy。这是一个网页过滤代理，可在:[https://www.privoxy.org/](https://www.privoxy.org/)
安装后，它监听本地端口 8118。

安装后，您需要以这种方式修改 Tor WebBrowser 的服务器代理:代理 HHTP: 127.0.0.1 端口:8118

快乐的黑客们！:)