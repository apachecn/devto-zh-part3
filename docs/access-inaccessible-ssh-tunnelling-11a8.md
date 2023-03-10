# 无法访问:SSH 隧道🔥🔥🔥

> 原文：<https://dev.to/nickitax/access-inaccessible-ssh-tunnelling-11a8>

我妈妈总是告诉我要避免去黑暗恐怖的隧道。

但是宋承宪隧道好玩有趣！🔌

[![tunnel](img/1c9fc36a1dbc5fb439804b840fe15714.png)](https://i.giphy.com/media/l0NwK1Xwv669wVd4Y/giphy.gif)

SSH(代表安全外壳)是一种以安全的方式与远程机器连接和交互的方式/协议。一个非常典型的情况是云实例，我们需要从本地设备访问它。为此，我们可以使用公钥/私钥对，或者直接使用如下帐户凭证登录:

```
ssh -i ~/.ssh/some_key nick@remote_host 
```

现在让我们想象一个不同的场景:

我们正在开发使用数据库的超级棒的 NodeJS 后端应用程序。其中一个集成测试需要真正连接到位于某个远程实例上的数据库。

假设我们正在连接到 MongoDB:

```
mongoose.connect('mongodb://remote_host:27017/db’); 
```

怎么办？🤔

当然，也有一个在远程机器上暴露数据库端口的选项，但是这肯定*不是很安全*。另外，让我们假设我们无法访问安全组和任何其他配置，但是我们能够通过 SSH 访问该实例…

有办法！

我们可以创建一个到该实例的隧道，并将本地端口转发到我们远程机器上的端口！

[![first](img/904552a97b57bfb83d93521690a0829a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HX8lYKiZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ezk4og51v4lrj8g5mvlq.png)

开始一个隧道是相当容易的:

```
ssh -i ~/.ssh/some_key nick@remote_host -L 127.0.0.1:27017:remote_host:27017 -N 
```

上面的代码试图做的是使用`~/.ssh/some_key`建立到`remote_host`的 SSH 连接，并将任何本地连接`(127.0.0.1)`转发到端口`27017`以重定向到`remote_host`上的连接。

如果有任何问题，您还可以添加-vvv 标志来获取更多日志。

`-N`在结束状态下不执行任何远程命令。(手册页是怎么说的)

现在，如果我们将 NodeJS 应用程序配置为连接到本地机器 localhost:

```
mongoose.connect('mongodb://127.0.0.1:27017/db’); 
```

我们应该能够以安全的方式访问我们的远程数据库！是啊！

听起来很酷？

我们有一点不同的情况:

让我们想象一下，我们仍然有 NodeJS 应用程序和远程实例(让我们称之为`instance A`),我们能够 SSH 到它们。但是这一次，我们的数据库将位于另一个实例上(姑且称之为主机名为`remote_host_2`的`instance B`，我们的本地机器无法到达实例 B(因为它有不同的安全规则)，但是`instance A`可以到达它。现在怎么办？

我们可以建一条…更长的隧道！就像这样:

```
ssh -i ~/.ssh/some_key nick@remote_host -L 127.0.0.1:27017:remote_host_2:27017 -N 
```

[![second](img/80e1cdbed67321287f5b305c467d4aa7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yNUTMsNk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4nr69wa9b82hfrq2rqa7.png)

与我们之前的隧道唯一不同的是，我们不是将流量重定向到`remote_host`，而是重定向到`remote_host_2`(这是我们的实例 B)。

当我们建立到`instance A`的 SSH 隧道时，我们能够到达`instance A`可以访问的任何其他实例(即使我们的本地机器不能到达它)。

太棒了，对吧？但是还有一个**更棘手的**场景…

假设您正在使用一个客户端应用程序，它与多个从节点的主节点通信。主人和所有的奴隶一样，都有自己的主机名。我们可以通过 SSH 访问主实例，但是从实例对我们来说是不可访问的。主设备能够连接到从设备。忘了说:客户端应用**不能修改【T1:)**

[![crazy](img/06fe07c3c664b7d5235abd638e51190c.png)](https://i.giphy.com/media/1gdnAzeb8Oih5npNtp/giphy.gif)

我们的客户端应用程序向主节点发送 HTTP 请求，主节点用要连接的从节点列表进行响应。假设周围有 3 个从机，那么我们得到 3 个主机地址:

```
a.slave.host:2020
b.slave.host:2020
c.slave.host:2020 
```

(对，端口都一样！)

还有一个高手:

```
master.host:2020 
```

我们可以建一个酒吧…等一下。

[![third](img/9c5317a0541ad20f6f1cb41adb8e9248.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JDR6lUOs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oi6fsjk3iwn1b49naghu.png)

如果我们的客户端应用程序从 master 接收到一个从属主机列表(我们无权访问),我们该如何为此建立一个隧道呢？

* * *

## 1。在本地机器上创建虚拟网络接口

我们要做的是为本地机器上的每台主机创建一个专用的虚拟网络地址。

我们可以创建一个从 192.168.1.201 到 192.168.1.203 的地址范围。(但是首先检查这些地址是否已经在你的机器上被占用了！)

我们需要创建 IPv4 和 IPv6

只需在苹果电脑或任何其他平台上谷歌一下。

## 2。将创建的本地地址的 DNS 解析与远程主机名相关联

我们为每个从节点创建了三个专用的本地地址，但是这对我们有什么帮助呢？很多，跟进就好。

现在我们需要声明一个 DNS 联网规则:如果有一个指向 a.slave.host 的请求，它应该转到 192.168.1.201(每个节点都是一样的)。

要声明 DNS 地址解析，让我们看看/etc/hosts 文件(在 Mac 和 Linux 上):

```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
#
127.0.0.1   localhost
255.255.255.255 broadcasthost
::1             localhost 
```

该文件代表 IP 地址与主机名的关联。

检查注释后的第一行:

```
127.0.0.1   localhost 
```

它明确表示“localhost”主机名将指向 127.0.0.1

在我们的场景中，我们需要以相同的方式将每个创建的 IP 地址与节点主机名关联起来，就像这样:

```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
#
127.0.0.1   localhost

a.slave.host 192.168.1.201
b.slave.host 192.168.1.202
c.slave.host 192.168.1.203

255.255.255.255 broadcasthost
::1             localhost

# These are IPv6 addresses which were declared during virtual network interface creation: 

1::2 a.slave.host
1::3 b.slave.host
1::4 c.slave.host 
```

现在，如果您尝试导航到这些主机名中的任何一个，它都会指向我们创建的虚拟地址！

现在是最后一部分:我们的类固醇隧道！

## 3。隧道

现在，一旦我们把所有的碎片组合在一起，我们就可以创建我们的难以置信的隧道了

```
ssh -i ~/.ssh/some_key nick@remote_host 
-L 192.168.1.201:2020:a.slave.host:2020
-L 192.168.1.202:2020:b.slave.host:2020
-L 192.168.1.203:2020:c.slave.host:2020 -N 
```

现在发生了什么？

现在，当我们的客户端应用程序将接收到要连接的主机名列表时，它将尝试向每个从属节点主机名发出请求，这些主机名与专用的本地虚拟网络接口地址相关联，该地址通过隧道连接到实际节点！

有用！我们做到了！🔥🔥🔥

[![we did it](img/ecae4aca121623690a6c46eec3d9dafd.png)](https://i.giphy.com/media/229OX0vSVNys10AZnS/giphy.gif)

特别感谢我的同事，他们分享了关于隧道的知识:

[https://github . com/fxbonet](https://github.com/fxbonnet)