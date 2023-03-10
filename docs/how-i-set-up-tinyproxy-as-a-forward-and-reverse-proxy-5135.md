# 我如何将 Tinyproxy 设置为正向和反向代理

> 原文：<https://dev.to/isabelcmdcosta/how-i-set-up-tinyproxy-as-a-forward-and-reverse-proxy-5135>

[Tinyproxy](https://tinyproxy.github.io/) 是针对 POSIX 操作系统的轻量级 HTTP/HTTPS 代理守护进程，在 Github 上[开源。](https://github.com/tinyproxy/tinyproxy)

我试用了这个工具，在通信的客户端设置了一个[正向代理](https://en.wikipedia.org/wiki/Proxy_server)，在服务器端设置了一个[反向代理](https://en.wikipedia.org/wiki/Reverse_proxy)。我想使用它，这样我就可以在正向代理和反向代理之间的网络上做实验，而不需要客户端和服务器的参与。

反向代理主要用作负载平衡器，我们连接到一个反向代理，然后它决定将请求发送到哪台机器。

> “(…)通过反向代理，可以使多个站点看起来像是单个站点的一部分(…)”

来自 Tinyproxy 的配置文件的手册。

## 要求

以下要求代表了我在实验中使用的内容:

*   一个 Linux OS 发行版的映像——我用了一个[Debian](https://www.debian.org/)GNU/Linux 8.9(Jessie)；
*   4 台虚拟机作为客户端、正向代理、反向代理和服务器——我使用了 [VirtualBox](https://www.virtualbox.org/) 来运行这些虚拟机，使用 Debian 的操作系统映像；
*   服务器必须运行一个 Web 服务器——我使用了 [Apache HTTP server](https://httpd.apache.org/) ,返回默认的 HTML 页面说“它工作了！”；
*   客户端必须安装浏览器或命令行工具，如 [curl](https://curl.haxx.se/) ，以处理 HTTP 请求；
*   正向和反向代理机器应该已经安装了[tiny proxy](https://tinyproxy.github.io/)——接下来我将展示如何在 Debian 机器上安装它。我用的版本是 1.8.3。

## 测试架构

[![Test architecture for the experiment](img/75e8562cc842abd9286a99b24487fbd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S7m4F6OM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11148726/54482693-974c2480-483f-11e9-86b1-b93afc0fd2f1.png)

我在 Github 上创建了[isabellcosta/testing-tiny-proxy](https://github.com/isabelcosta/testing-tiny-proxy)存储库，其中包含运行正向代理和反向代理角色所需的配置文件。

## 网络配置

VirtualBox 允许您配置虚拟机的网络设置。我使用 Nat 网络设置，它允许我在同一个网络中拥有所有的机器。这些是分配给每台机器的 IP。

*   客户端-10 . 0 . 2 . 33
*   转发代理— 10.0.2.35
*   反向代理— 10.0.2.36
*   服务器— 10.0.2.34

这些 IP 地址很重要，因为它们会出现在如何测试系统的例子中。

## 安装 Tinyproxy

要安装 Tinyproxy，您必须在正向和反向代理机器的终端中键入以下命令:

```
apt-get install tinyproxy 
```

Enter fullscreen mode Exit fullscreen mode

## 设置系统

Tinyproxy 根据配置文件工作。我写了两个配置，一个用于正向代理，另一个用于反向代理。

**转发代理配置文件:**

```
## forwardproxy.conf -- tinyproxy daemon configuration file

User nobody
Group nogroup

Port 8888
Listen 10.0.2.35
BindSame yes

Timeout 600

DefaultErrorFile "/usr/share/tinyproxy/default.html"
StatFile "/usr/share/tinyproxy/stats.html"
Logfile "/var/log/tinyproxy/tinyproxy.log"
#Syslog On
LogLevel Info
PidFile "/var/run/tinyproxy/tinyproxy.pid"

# Comment to use only the forward proxy
# Uncomment to forward the traffic to the reverse proxy
#Upstream 10.0.2.36:8888

MaxClients 100
MinSpareServers 2
MaxSpareServers 5
StartServers 2
MaxRequestsPerChild 0

Allow 127.0.0.1
Allow 10.0.2.0/24

ViaProxyName "tinyproxy1"

ConnectPort 8888
ConnectPort 80

# The following two ports are used by SSL.
ConnectPort 443
ConnectPort 563 
```

Enter fullscreen mode Exit fullscreen mode

**反向代理配置文件:**

```
## reverseproxy.conf -- tinyproxy daemon configuration file

User nobody
Group nogroup

Port 8888
Listen 10.0.2.36

BindSame yes
Timeout 600

StatFile "/usr/share/tinyproxy/stats.html"
Logfile "/var/log/tinyproxy/tinyproxy.log"
#Syslog On
LogLevel Info
PidFile "/var/run/tinyproxy/tinyproxy.pid"

MaxClients 5
MinSpareServers 2
MaxSpareServers 5
StartServers 2

MaxRequestsPerChild 0

Allow 127.0.0.1
Allow 10.0.2.0/24
Allow 10.0.2.35

ViaProxyName "tinyproxy2"

ConnectPort 8888
ConnectPort 80

# The following two ports are used by SSL.
ConnectPort 443
ConnectPort 563

ReversePath "/test/" "http://10.0.2.34:80/"
#ReversePath "/" "http://10.0.2.34:80/"
ReversePath "/wired/" "http://www.wired.com/"

ReverseOnly Yes
ReverseMagic Yes
ReverseBaseURL "http://10.0.2.36:8888/" 
```

Enter fullscreen mode Exit fullscreen mode

要使用特定配置运行 tinyproxy，只需执行以下操作:

```
tinyproxy -c <configuration-file> 
```

Enter fullscreen mode Exit fullscreen mode

例如:tinyproxy -c forwardproxy.conf

## 测试系统

首先确保服务器正在相应地运行，并且您可以从任何一台机器上使用下面的命令访问服务器，因为它们都在同一个网络中。你可以使用 [curl](https://curl.haxx.se/) 命令行工具或者浏览器
来测试这个

```
curl http://10.0.2.34:80/ 
```

Enter fullscreen mode Exit fullscreen mode

现在要测试整个系统，如果你想使用 curl，你可以在客户机控制台上输入:

```
curl -v --proxy http://10.0.2.35:8888 http://10.0.2.36:8888/ 
```

Enter fullscreen mode Exit fullscreen mode

这是前面命令的输出:

```
root@debian:/home/debian# curl -v --proxy http://10.0.2.35:8888 http://10.0.2.36:8888
* Rebuilt URL to: http://10.0.2.36:8888/
* Hostname was NOT found in DNS cache
*   Trying 10.0.2.35...
* Connected to 10.0.2.35 (10.0.2.35) port 8888 (#0)
> GET http://10.0.2.36:8888/ HTTP/1.1
> User-Agent: curl/7.38.0
> Host: 10.0.2.36:8888
> Accept: */*
> Proxy-Connection: Keep-Alive
> 
< HTTP/1.1 200 OK
< Via: 1.0 tinyproxy2 (tinyproxy/1.8.3), 1.1 tinyproxy1 (tinyproxy/1.8.3)
< Last-Modified: Mon, 11 Jun 2007 18:53:14 GMT
< Date: Tue, 12 Dec 2017 23:01:37 GMT
< Content-Type: text/html
< ETag: "2d-432a5e4a73a80"
< Set-Cookie: yummy_magical_cookie=/; path=/
* Server Apache/2.4.29 (Unix) is not blacklisted
< Server: Apache/2.4.29 (Unix)
< Content-Length: 45
< Accept-Ranges: bytes
< 
<html><body><h1>It works!</h1></body></html>
* Connection #0 to host 10.0.2.35 left intact 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是使用 Wireshark 工具。此工具允许您查看网络流量。在测试系统之前，开始运行 Wireshark。通过在本地网络中进行测试，您可以看到从客户端到服务器的全部流量。在请求并收到服务器的响应后，如果您通过“http”过滤 Wireshark 捕获，您应该会看到类似下图的结果。

[![[Wireshark](https://www.wireshark.org/) capture of the communication between the client and the server, passing through the proxies.](img/efd2105445cb0e268d99963b74edeb0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sgxDHgOz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/11148726/54482695-9dda9c00-483f-11e9-989a-36168012c209.png)

在这个 [Wireshark](https://www.wireshark.org/) 捕获中，您可以看到两个方向的流量:客户端↔正向代理↔反向代理↔服务器。

要检查日志文件以及正向和反向代理，可以在任一台机器上键入以下命令:

```
cat /var/log/tinyproxy/tinyproxy.log 
```

Enter fullscreen mode Exit fullscreen mode

如果你想用另一种方式测试，你可以在[isabellcosta/testing-tiny-proxy](https://github.com/isabelcosta/testing-tiny-proxy)存储库上修改代理的配置文件。

## 提示&注意事项

*   如果你想设置其他路径，你可以使用“ReversePath”关键字。例如:reverse path "/test " "[http://10 . 0 . 2 . 34:80/"](http://10.0.2.34:80/%E2%80%9D)—这样就可以通过输入"[http://10 . 0 . 2 . 36:8888/test "](http://10.0.2.36:8888/test%E2%80%9D)来访问服务器
*   我总是收到错误 400 错误请求，因为我使用这个工具的方式不对。我使用 curl 作为端点连接服务器，而不是反向代理。反向代理不像正向代理那样工作，所以不要使用“上游”关键字将流量转发到反向代理。

*[也贴在我的个人网站上。](https://isabelcosta.github.io/2018/09/06/setup-tinyproxy-as-forward-and-reverse-proxy.html)T3】*