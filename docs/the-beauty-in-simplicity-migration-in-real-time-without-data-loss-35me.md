# 简单之美:实时迁移，无数据丢失。

> 原文：<https://dev.to/ypkuby/the-beauty-in-simplicity-migration-in-real-time-without-data-loss-35me>

这是最初发布在我的博客上的[，在那里我发布所有关于技术、系统管理和更多的东西](https://kuby.ca/)

我们都听说过这样的故事——能够实时迁移，而不会丢失任何数据。我以前为这个博客做过一次，我为我的妻子和我又做过一次。在一天当中，大约有 1k 的访问者在线。以下是我是如何做到的。

# 目标

我不仅想迁移提供商，还想将我的内容迁移到一个新的国家，实际上是相隔几个大洲。从我在 Hetzner 的旧博客主机转移到 DigitalOcean。幸运的是，双方都有 1G 端口(Hetzner 未计量，DO 有几 TB cap)——绰绰有余。

文件大小不是问题，大约 2.4 GB 的文件大小，数据传输总共花了大约 1 分钟(这并不坏，考虑到距离，它可以改进)。

正在发生的问题是实时移动，在幕后有分析引擎，一些无头 API 等。都与数据实时交互。这就是问题所在。

为了解决这个问题，我们需要在 PostgreSQL 上设置一些主/从复制，然后进行完全成熟的主/主复制。接下来，我想我们必须慢慢地将流量从旧主机转移到新主机。最后，关掉旧盒子。

# 解释

随着文件的传输，我将两个虚拟机都设置为 1GB / 25GB SSD / 2TB 带宽实例。我已经启用了 SSH 键。

在浏览 DO 界面时，我注意到他们有云防火墙，这很棒——该界面比 Hetzners 防火墙界面(专用服务器)要好。我首先创建了一个通过标签(生产、cloudflare、博客)过滤的防火墙，因此这个防火墙将应用于带有这些标签的虚拟机。

我只希望从我的跳转服务器(OVH BHS 虚拟机或我的家庭服务器)SSH，HTTP/HTTPS 只从 Cloudflare，并拒绝这个实例的世界其他地方。因为云防火墙非常简单，所以这些规则最终看起来是这样的:

出站，我已经允许所有来源(好的或坏的)。我应该考虑在未来收紧这一点，但现在我会让它保持原样。我想先确保一切都准备好投入生产。

我在我的两个虚拟机上为我们的博客启用了专用网络，这有助于节省带宽，我还在同一个 DC 中将它们链接在一起。太好了。

现在，对于 SQL 实例，我创建了一个名为“int-production”的防火墙，这将拒绝其公共 IPv4 和 IPv6 地址上的所有通信，但可以与需要访问的连接虚拟机(如博客)进行对话。我已经启用了专用网络，所以我可以通过专用网络直接连接到它，再次节省带宽(因为它是在数字海洋上计量的)。

我设置了主/从复制，允许防火墙接受来自 Hetzner 的 IP，也只允许出站到 Hetzner 的 IP。20 分钟后，我们同步起来，并准备电机。我把它翻转到 master/master，开始慢慢地重定向域名。

15 分钟后，我确信主机是相同的，并关闭复制，关闭 Hetzner 节点。我在 UptimeRobot 上运行了我的监控程序，看到了我所期待的——100%的正常运行时间。

但是等等，我所有的错误日志中都有 CF 的 IP，之前我在主机专用服务器上设置了一些规则，现在这些是单独的虚拟机，我需要重新配置它们，我找到了这篇文章 nginx 的脚本，它非常有效，并在上面设置了 crontab。我将其设置为每周一次(周日，格林威治时间晚上 11 点)，这对我的需求来说绰绰有余。

没有数据丢失，我们一直在线，这满足了我不断增长的需求。我妻子和我的博客每天的访问量加起来超过了 50，000 次，旧系统的扩展性不是很好，并且会不断地丢失合法连接，调试所耗费的精力和时间超过了留在旧提供商处的成本。

由于 DigitalOcean 具有更好的灵活性，我已经能够为所有带有标签的虚拟机添加一些相同的规则，而不是像过去那样，在 N 个虚拟机之间单独复制它们。内存、磁盘、入站/出站带宽帮助的警报策略。

# 未来想法

在流量激增的情况下，随着我的辅助项目网络的增长，我会安装一个 DO 负载平衡器，每月只需 10 美元，似乎非常容易使用。将我的虚拟机用于博客和应用程序。我想得到更多的监督，可能会迁移我的 Grafana + Prometheus 设置来做，使用内部网络来节省带宽。

让我们看看它能坚持多久。我希望能够将我所有的产品从我自己的协同定位设备上迁移出来，并将协同定位设备返回到实验室。我准备继续测试 DO 几个月，希望能全面转行。价位合理，位置好(离我住的地方 20ms 以内)，

暂时就这样吧！随着我深入了解更多特性，我将不断更新我的 DO 体验。也许接下来我会试试他们的 k8 服务。