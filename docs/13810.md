# 在容器中将 coreens 作为 dns 服务器运行

> 原文：<https://dev.to/robbmanes/running-coredns-as-a-dns-server-in-a-container-1d0>

如果你曾经需要或想要建立自己的 DNS 服务器，那么这是给你的。最近，我发现自己拥有一个 Raspberry Pi，我决定从 Pi 上的容器中提供 DHCP 和 DNS 服务，而不是依赖于我的家庭路由器，这样，当我通过 VPN 连接到我的网络时，我就可以解析我的所有主机及其各自的名称。我打算将所有其他请求转发到不在我的本地网络中的另一个服务器，但仍然使用我定制的主机名来服务本地系统。

最近，我发现自己越来越多地使用 containers 和 OpenShift，这是 Red Hat 的 Kubernetes 发行版(声明，我为 Red Hat 工作)，在 Kubernetes 的上游，提供的 DNS 服务器之一是 [CoreDNS](https://coredns.io/) 。我已经用它玩了一段时间了，我想我应该制作这个关于如何自己启动它的教程，使用 Docker Hub 上 [CoreDNS 提供的容器映像](https://hub.docker.com/r/coredns/coredns)。

我还想指出的是，我们在这里使用了 CoreDNS 功能的非常非常小的一部分。CoreDNS 最突出的一点是它的插件可定制性，它通过插件与 Kubernetes 的直接集成使它变得非常强大。尽管如此，如果 DNS 服务器对你来说是新的，或者`bind`或`unbound`让你有点害怕，也许可以根据你的个人需求提供 CoreDNS。

事不宜迟，下面是我如何让它工作。

首先，从 Docker Hub 本地下载容器映像。如果你使用 Docker，你可以这样做:

```
# docker pull coredns/coredns 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要配置一个`Corefile`，它作为 CoreDNS 守护进程的配置文件；这里可以传入许多选项(可以在 CoreDNS 手册中看到)，但我将通过我使用的示例。

```
[~/containers/coredns] # cat Corefile 
.:53 {
    forward . 8.8.8.8 9.9.9.9
    log
    errors
}

example.com:53 {
    file /root/example.db
    log
    errors
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们一个一个地看一下`Corefile`的选项。值得注意的是，每个括号中的部分表示一个 DNS“区域”，它根据正在解决的问题设置核心 DNS 的行为。

首先，请注意最初的括号部分。它以`.:53`开头，表示该区域是一个全局区域(带有“.”表示所有流量)，并且它正在侦听端口 53(默认情况下为 udp)。我们在此设置的参数将应用于所有未指定特定区域的传入 DNS 查询，如解析`github.com`的查询。我们在下一行看到，我们将这样的请求转发到辅助 DNS 服务器进行解析；在这种情况下，对该区域的所有请求将被简单地转发到位于`8.8.8.8`和`9.9.9.9`的谷歌 DNS 服务器。

第二，我们有另一个为`example.com`指定的区域，也监听 UDP 端口 53。对属于该区域的主机的任何查询都将引用文件数据库(类似于 bind 所做的)以在那里进行查找；稍后会有更多。例如，对“server.example.com”的查询将绕过全局区域“.”并且落入为“example.com”提供服务的区域，使用`file`指令将引用数据库文件来找到正确的记录。

这就是这个`Corefile`的全部内容，对于一个简单的转发 DNS 服务器来说，它也服务于带有主机名的本地客户机。现在我们必须创建我们引用的 DNS 数据库文件，`example.db`，并用我们的主机填充它。

虽然这不是一个 DNS 初级读本，但我还是要介绍一下这个文件是如何工作的。这里有两个主要的记录，我将讨论第三个；它们是 SOA、A 和 CNAME DNS 记录，它们将构成我们的 DNS 配置。

最初，我们*必须*配置一个`SOA`记录，或者一个“授权开始”记录。这是该区域中的 DNS 服务器用来向正在进行查询的客户端声明其权限的初始记录，我们必须以它开始文件。下面是一个可以在这个文件中使用的 SOA 记录示例:

```
example.com.        IN  SOA dns.example.com. robbmanes.example.com. 2015082541 7200 3600 1209600 3600 
```

Enter fullscreen mode Exit fullscreen mode

要单独浏览每个部分:

*   `example.com.`指该 DNS 服务器负责的区域。
*   `SOA`指记录的类型；在这种情况下，是“权威的开始”
*   `dns.example.com`指该 DNS 服务器的名称
*   `robbmanes.example.com`指该 DNS 服务器的管理员的电子邮件。请注意，`@`符号只是用句号标出；这不是错误，而是如何格式化。
*   `2015082541`指序号。这可以是您喜欢的任何内容，只要它是在此配置中不重复使用的序列号或者包含无效字符。关于如何设置，通常有规则可循，特别是通过设置关于最后修改的有效日期，如 2019 年 2 月 8 日 22:00 点的`2019020822`。
*   `7200`指刷新率，以秒为单位；在这段时间之后，客户端应该重新检索 SOA。
*   `3600`是以秒为单位的重试率；此后，任何失败的刷新都应该重试。
*   `1209600`是指在客户端不再将该区域视为“权威”区域之前经过的时间(秒)。此 SOA 中的信息在此时间后过期。
*   `3600`是指以秒为单位的生存时间，这是区域中所有记录的默认值。

一旦我们按照自己的喜好编写了我们的`SOA`,我们就可以为我们希望解析的每个主机添加额外的记录。我使用静态 DHCP 租约为某些 MAC 地址分配我的 IP 地址，为此我首先添加了 DNS 服务器，这样它就可以自己解析:

```
dns.example.com.    IN  A   192.168.1.2 
```

Enter fullscreen mode Exit fullscreen mode

一个`A`记录表示一个名字，在这个例子中是`dns.example.com`，它可以被规范地直接映射到一个 IP 地址`192.168.1.2`。如果我再加一个`A`记录:

```
host.example.com.   IN  A   192.168.1.10 
```

Enter fullscreen mode Exit fullscreen mode

然后我可以给它分配一个`CNAME`记录，它将作为某种“别名”，将流量引导回`host.example.com` :

```
server.example.com. IN  CNAME   host.example.com. 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这里添加任意多的条目，或者查找不同类型的记录以满足您的需要。我以一些基本的东西结束，就像这样:

```
example.com.        IN  SOA dns.example.com. robbmanes.example.com. 2015082541 7200 3600 1209600 3600
gateway.example.com.    IN  A   192.168.1.1
dns.example.com.    IN  A   192.168.1.2
host.example.com.   IN  A   192.168.1.3
server.example.com. IN  CNAME   host.example.com 
```

Enter fullscreen mode Exit fullscreen mode

之后，当我们完成 DNS 区域文件和`Corefile`时，我们可以将它们放在同一个目录中，并准备将它们导出到一个新运行的 coredns 容器中。我把这两个文件都放在了目录`~/containers/coredns/` :

```
$ pwd
/home/robb/containers/coredns

$ ls
Corefile  example.db 
```

Enter fullscreen mode Exit fullscreen mode

为了运行容器，`coredns`二进制文件在它所在的直接目录中查找任何名为`Corefile`的文件，并将其用作配置。不幸的是，在我们从 Docker Hub 中提取的`coredns/coredns`映像中，它位于`/`的根目录中，不能作为卷挂载。我们需要手动传递我们的`Corefile`，并确保我们的区域`example.com:53`中的`file`指令是容器中指向 DNS 区域数据库文件的直接路径。为此，我将它们映射到容器中的`/root`，并传递了`-conf`选项，该选项允许用户指定核心文件的路径；这是我用来启动 CoreDNS 容器的命令:

```
# docker run -d --name coredns --restart=always --volume=/home/robb/containers/coredns/:/root/ -p 53:53/udp coredns/coredns -conf /root/Corefile 
```

Enter fullscreen mode Exit fullscreen mode

之后，我通过检查日志和`docker ps -a` :
来确保我的容器运行正常

```
# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                        NAMES
8a6c9a5c0538        coredns/coredns     "/coredns -conf /roo…"   About an hour ago   Up About an hour    53/tcp, 0.0.0.0:53->53/udp   coredns

# docker logs coredns
.:53
example.com.:53
2019-02-09T04:50:24.060Z [INFO] CoreDNS-1.3.1
2019-02-09T04:50:24.061Z [INFO] linux/arm, go1.11.4, 6b56a9c
CoreDNS-1.3.1
linux/arm, go1.11.4, 6b56a9c 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以从同一个子网中的客户机用`dig`查询我们的服务器，以确保它按预期工作。我的 DNS 容器运行在 IP 为`192.168.1.2` :
的主机上

```
$ dig @192.168.1.2 host.example.com

; <<>> DiG 9.11.3-1ubuntu1.3-Ubuntu <<>> @192.168.1.2 host.example.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30400
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 9faccdae88cb6576 (echoed)
;; QUESTION SECTION:
;host.example.com       IN  A

;; ANSWER SECTION:
host.example.com    0   IN  A   192.168.1.3

;; Query time: 3 msec
;; SERVER: 192.168.1.2#53(192.168.1.2)
;; WHEN: Fri Feb 08 23:01:22 MST 2019
;; MSG SIZE  rcvd: 93 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们有了一个易于配置和维护的 CoreDNS 配置！到目前为止，我一直在使用`docker restart`来重新加载容器，并重新读取`Corefile`和 DNS 区域文件，但你绝对应该知道 CoreDNS 的 [reload](https://coredns.io/plugins/reload/) 插件，它消除了重新启动容器的需要。

这是对 CoreDNS 的一个非常基本的介绍，我希望你能从这个伟大的 DNS 守护进程中得到一些有用的东西。