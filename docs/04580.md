# 修复写入 NAS 系统时的速度问题

> 原文：<https://dev.to/4nduril/fix-speed-issue-when-writing-to-nas-system-3oca>

我刚刚修复了我的 FreeBSD 家庭服务器的一个问题。它被设置为 Mac (AFP)和 Linux 客户端(NFS)的文件服务器。我的局域网是千兆位的，所以读写速度的限制因素应该是服务器中的硬盘驱动器。

该服务器有一个 Core i3-6100T CPU @ 3.20GHz，8GB RAM，一个 ZFS 设置，带有两个镜像 vdevs，每个包含两个通过 SATA3 连接到主板的磁盘。当然还有板载 Gbit 网卡(Realtek)。

我很清楚地知道写速度在 50-60MB/秒左右，这是我所期望的。但是最近，它惊人地下降到大约 1MB/秒。我只是想不出，为什么。我怀疑有线电视，法新社，内存什么的。

直到今天，我才开始怀疑网络接口。但是我今天有时间进行了一些谷歌搜索，即使我没有直接找到解决方案，我也偶然发现了一些与`ifconfig`的输出相关的东西。所以我黑进控制台盯着它。

```
re0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=8209b<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM,WOL_MAGIC,LINKSTATE>
        ether 4c:cc:6a:b3:3c:f5
        hwaddr 4c:cc:6a:b3:3c:f5
        inet6 fd23:16:7:7::1 prefixlen 64
        inet6 fe80::4ecc:6aff:feb3:3cf5%re0 prefixlen 64 scopeid 0x1
        inet 192.168.10.118 netmask 0xffffff00 broadcast 192.168.10.255
        nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
        media: Ethernet autoselect (10baseT/UTP <full-duplex>)
        status: active 
```

Enter fullscreen mode Exit fullscreen mode

你发现了吗？

```
 media: Ethernet autoselect (10baseT/UTP <full-duplex>) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那真是……不幸。`ifconfig -m re0`的输出给了我:

```
 supported media:
            media autoselect mediaopt flowcontrol
            media autoselect
            media 1000baseT mediaopt full-duplex,flowcontrol,master
            media 1000baseT mediaopt full-duplex,flowcontrol
            media 1000baseT mediaopt full-duplex,master
            media 1000baseT mediaopt full-duplex
            media 100baseTX mediaopt full-duplex,flowcontrol
            media 100baseTX mediaopt full-duplex
            media 100baseTX
            media 10baseT/UTP mediaopt full-duplex,flowcontrol
            media 10baseT/UTP mediaopt full-duplex
            media 10baseT/UTP
            media none 
```

Enter fullscreen mode Exit fullscreen mode

所以我运行了`sudo ifconfig re0 media 1000baseTX mediaopt full-duplex`并且成功了。之后，我还运行了`sudo ifconfig re0 media autoselect`，它也将媒体类型设置为 1000baseT 全双工。我不知道为什么系统会出错(或者什么时候出错)，但是我会监控下次重启后会发生什么。也许我必须做一些配置，但也许这只是一个吻痕。

速度再次达到 60MB/秒。