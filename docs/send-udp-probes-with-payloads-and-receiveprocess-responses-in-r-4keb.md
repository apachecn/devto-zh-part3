# 发送 UDP 探测(带有有效负载)并接收/处理响应

> 原文：<https://dev.to/hrbrmstr/send-udp-probes-with-payloads-and-receiveprocess-responses-in-r-4keb>

我们花了$DAYJOB 的时间非常努力地帮助量化和修复连接到互联网的 Ubiquiti 网络工作设备中的一个相当大的配置缺陷。

无处不在的网络设备——路由器、交换机、无线接入点等。—是企业级组件，使用起来非常愉快。我们的家庭网络中充斥着这种设备。Ubiquiti 节点具有“发现服务”,因此它们可以被识别并置于控制器的管理之下。大约有 50 万人忽略了加入控制器，或者只是让发现服务运行并可通过互联网接口访问。O_O

我们使用各种各样的技术在互联网上执行发现和内容扫描，从 [zmap](https://zmap.io/) 到 Python 和其他介于两者之间的东西。用 Ruby 或 Python 编写一个快速的 UDP 客户端来与使用 UDP 的节点对话并获得响应是非常容易的。不幸的是，R 只有内置的`connection`对 TCP 通信的支持。考虑到 R 是如何产生的，以及它存在的主要用途，这是很有意义的。我希望能够测试我自己的，非互联网暴露的 Ubiquiti 设备，并从 R 开始做，因此产生了 [`udpprobe`](https://git.sr.ht/~hrbrmstr/udpprobe) 包。

你可以通过你选择的社交编码平台安装它(在检查源代码后，因为你不应该盲目相信任何互联网 git 回购):

```
devtools::install_git("https://git.sr.ht/~hrbrmstr/udpprobe")
# or
devtools::install_gitlab("hrbrmstr/udpprobe")
# or
devtools::install_github("hrbrmstr/udpprobe") 
```

Enter fullscreen mode Exit fullscreen mode

对你们 *Windows* 的人来说，一些**好消息**是*实际上也能在你的传统操作系统上工作*！

### 这个包可以做什么？

很高兴你问了。

我们一会儿会谈到泛在部分。首先，我们将手工为地址`example.com`的 DNS 查找创建有效负载。你每天都要进行成千上万次 DNS 查询，但是很可能从来没有发现它们真正的样子。这些查找通常仍然在 UDP 上执行，协议和记录格式都有[完整的文档记录]。下面是对`example.com`的`A`记录请求的样子:

```
library(udpprobe)

c(
  0xaa, 0xaa, 0x01, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x00, 
  0x00, 0x00, 0x07, 0x65, 0x78, 0x61, 0x6d, 0x70, 0x6c, 0x65, 
  0x03, 0x63, 0x6f, 0x6d, 0x00, 0x00, 0x01, 0x00, 0x01
) -> dns_req 
```

Enter fullscreen mode Exit fullscreen mode

字符串`example.com`是`0x65 0x78 0x61 0x6d 0x70 0x6c 0x65 0x2e 0x63 0x6f 0x6d`，你可以在原始向量的第 14 位找到它(*技术上来说*它仍然是一个数字向量，但我用十六进制表示，这样更容易引用它)。紧接着是一个终止符，然后是一个 2 字节的序列，告诉 DNS 服务器我们正在寻找一个`A`记录。让谷歌做点工作:

```
(resp <- udp_send_payload("8.8.8.8", 53, dns_req))
## [1] aa aa 81 80 00 01 00 01 00 00 00 00 07 65 78 61 6d 70
## [19] 6c 65 03 63 6f 6d 00 00 01 00 01 c0 0c 00 01 00 01 00
## [37] 00 0c 47 00 04 5d b8 d8 22 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细看的话，你应该可以在那里再找到`example.com`。我们将假设响应是正常的，并取出它发回的 IP 地址:

```
paste0(as.integer(tail(resp, 4)), collapse = ".")
## [1] "93.184.216.34" 
```

Enter fullscreen mode Exit fullscreen mode

并且，用耶鲁安漂亮的`curl`包:
来验证一下

```
curl::nslookup("example.com")
## [1] "93.184.216.34" 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Ubiquiti 套件找乐子

如果你读了前面链接的博客文章，你就会知道要和 Ubiquiti gear 对话，我们需要发送`0x01 0x00 0x00 0x00`到 UDP 端口 10001。因为我计划扩展`ubpprobe`包来包含标准探针的助手函数，所以我们将使用目前提供的单一函数来与已知的公开系统对话。我没有给你 IP 地址，而是把它存储在一个环境变量中:

```
(x <- ubnt_discovery_probe(Sys.getenv("UBNT_TEST_HOST")))
## [1] 01 00 00 a0 02 00 0a dc 9f db 3a 5f 09 8a ff bd a9 02
## [19] 00 0a dc 9f db 3b 5f 09 c0 a8 02 01 01 00 06 dc 9f db
## [37] 3a 5f 09 0a 00 04 00 01 cb 0d 0b 00 15 39 36 39 20 2d
## [55] 20 4a 75 76 65 6e 61 6c 20 52 69 62 65 69 72 6f 0c 00
## [73] 03 4c 4d 35 0d 00 11 4e 45 54 53 55 50 45 52 2d 53 49
## [91] 51 55 45 49 52 41 0e 00 01 02 03 00 22 58 4d 2e 61 72
## [109] 37 32 34 30 2e 76 35 2e 36 2e 35 2e 32 39 30 33 33 2e
## [127] 31 36 30 35 31 35 2e 32 31 31 39 10 00 02 e8 a5 14 00
## [145] 13 4e 61 6e 6f 53 74 61 74 69 6f 6e 20 4c 6f 63 6f 20
## [163] 4d 35 
```

Enter fullscreen mode Exit fullscreen mode

那个快捷函数只是调用:

```
udp_send_payload(Sys.getenv("UBNT_TEST_HOST"), 10001L, c(0x01, 0x00, 0x00, 0x00)) 
```

Enter fullscreen mode Exit fullscreen mode

与 DNS 不同，Ubiquiti 响应有效载荷没有正式记录，但 Ubiquiti 论坛上的人们已经解决了大部分问题，我们为更多“未知”字段添加了一些额外的覆盖范围。我们可以使用内置的解析器来解析这些有效负载的响应，看看它是什么类型的设备以及它运行的是什么固件:

```
(y <- parse_ubnt_discovery_response(x))
## [Model: LM5; Firmware: XM.ar7240.v5.6.5.29033.160515.2119; Uptime: 1.4 (hrs) 
```

Enter fullscreen mode Exit fullscreen mode

是的，它甚至有一个漂亮的打印机。下面是一些内幕(再次声明，我已经编辑了一些你不应该知道的东西，因为它可能会伤害目标:

```
str(y, 1)
List of 10
 $ name : chr "969 - Juvenal Ribeiro"
 $ model_long : chr "NanoStation Loco M5"
 $ model_short: chr "LM5"
 $ firmware : chr "XM.ar7240.v5.6.5.29033.160515.2119"
 $ essid : chr "NETSUPER-SIQUEIRA"
 $ uptime : int 118619
 $ macs : chr [1:2] "dc:9f:db:3a:xx:xx" "dc:9f:db:3b:xx:xx"
 $ ips : chr [1:2] "REDACTED", "REDACTED"
 $ ux0e : raw 02
 $ ux10 : raw [1:2] e8 a5
 - attr(*, "class")= chr "ubnt_d" 
```

Enter fullscreen mode Exit fullscreen mode

这是太多的信息泄露到互联网上，500，000 个节点高兴地为任何人提供这些信息。

### 鳍

我需要添加对 UDP 超时和动态响应大小的支持(4K 有一个临时的硬编码限制)。我在 Windows 虚拟机上测试了它，它确实可以工作，但是如果你在那个平台上做更多的测试，你会很高兴的。

踢踢轮胎，归档问题和 PRs，欢迎来到 R 中的 UDP 世界！