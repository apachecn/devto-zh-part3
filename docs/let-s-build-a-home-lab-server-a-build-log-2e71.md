# 让我们构建一个家庭实验室服务器——构建日志

> 原文：<https://dev.to/nathanbland/let-s-build-a-home-lab-server-a-build-log-2e71>

家庭实验室是我很久以来一直想要的东西。在这篇文章中，我将带你一起经历我的冒险，我所面临的一些困难，以及我是如何解决它们的。

许多人可以从一个家庭实验室开始做起。我也开始使用其中的一个，但是我的需求已经超出了它的能力范围。pi(尤其是 pi 3 model b+)仍然是一个很棒的小工具，如果你刚刚开始，我推荐一个。就价格而言，他们是无可匹敌的。这是一个有点思想的垃圾场，所以如果你想跳来跳去，这里有几个部分:

*   介绍(你在这里)
*   [研究](#research)
*   [构建](#the-build)
*   [存储](#the-storage)
*   [网络](#the-network)
*   [软件](#the-software)
*   [临时演员](#extras)
*   [安装和故障排除](#install-and-troubleshooting)

## 研究

我花了很多时间试图找出什么部分，或系统，我应该得到这个。我想要稳定的性能，但和其他人一样，我不想花一大笔钱去买它。我掉进了 [/r/homelab](https://www.reddit.com/r/homelab/) 和[/r/data boarder/](https://www.reddit.com/r/DataHoarder/)的兔子洞寻找建议。

我发现自己在 serverbuilds.net 查看他们的构建列表。这让我在 ebay 上寻找，这也是我如何遇到专门从事二手企业硬件的 [MET 服务器](https://www.metservers.com/)的原因。我已经找到了我的甜蜜点。

## 打造

MET 上的价格简直让我大吃一惊。出于几个原因，我选择用 [`Dell R710`](https://www.metservers.com/Dell-PowerEdge-R710-6-Bay-LFF-2U-Rackmount-Server) 开始我的配置。第一，它使用的背板(PERC H700)可以处理大型驱动器，并可以支持 SATA 6Gb/s。以下是我最终选择的完整配置:

### 大脑

```
• Dell PowerEdge R710 6-Bay LFF 2U Rackmount Server
--------------------------------------------------------
• Processors: 2x Intel Xeon X5660 2.8GHz 6 Core 12MB Cache Processors.
• Memory: 24GB DDR3 ECC Registered Memory (6 x 4GB)
• RAID Controller: Dell PERC H700 6Gbps SAS/SATA RAID Controller 512MB Cache (0 1 5 6 10 50 60)
• Hard Drive Bay x6 1: Dell 3.5 Tray with screws 
• Daughter Card: Embedded Broadcom NetXtreme II 5709C Gigabit Ethernet NIC
• Management: iDRAC 6 Express Management Module
• Networking: Additional Network Card Not Included
• Power Supplies: 2x Redundant 870W Power Supplies
• Rail Kit: Rail Kit Not Included
• Front Security Bezel: Bezel Included with Key 
```

Enter fullscreen mode Exit fullscreen mode

配置 50 美元起步，很疯狂。CPU 升级 40 美元，内存 54 美元，PSU 升级到 870 瓦是 15 美元，驱动器托架每个 10 美元。这种配置的惊人之处在于它内置了一个 4 端口千兆以太网适配器。

这花了我大约 245 美元*运费*，比我在网上找到的一些空机箱要少。当然，问题是它没有驱动器。他们确实可以选择带驱动器发货，(4Tb，80 美元)，这个价格还不错。然而，我想尝试一下我在网上找到的其他东西，那就是剥离大型外部硬盘驱动器(出于某种原因，价格更低)，并将它们用作内部存储。虽然使用 CPU 和 ram 并不是什么大不了的事情，但是硬盘上的写时间却可以，所以很遗憾我不得不买了新的。

### 存储

```
Bad idea - $6.99 - 1x Kingston Digital 16 GB DataTraveler SE9 G2 USB 3.0 Flash Drive (DTSE9G2/16GB)
Good idea - $67 -  WD Blue 3D NAND 500GB PC SSD - SATA III 6 Gb/s, 2.5"/7mm - WDS500G2B0A 
$8.95 - Protronix SATA Optical Bay 2nd Hard Drive Caddy, Universal for 12.7mm CD/DVD Drive Slot
$110 each - 6x WD 6TB Elements Desktop Hard Drive - USB 3.0 - WDBWLG0060HBK-NESN 
```

Enter fullscreen mode Exit fullscreen mode

这里看似异常的是 USB 闪存驱动器，但最酷的是它可以用作操作系统驱动器，这样我就不必为操作系统占用任何存储空间。最初的计划是使用闪存驱动器存储基础操作系统。闪存驱动器无法处理主操作系统的读/写周期，大约一个月后就报废了。我用光学托架 ssd 支架和 ssd 组合替换了它，从那以后它一直完美地工作。我也没有丢失任何数据，这是一个很大的解脱。

WD elements 驱动器都比其他所有驱动器都来得早，这使我能够在它们的机箱中测试驱动器，以确保它们都正常工作。我做了一个简单的文件传输(大约 10Gb)和 S.M.A.R.T .状态检查。后来，是时候哄它们离开壳了。我关注了一个关于这个过程的 [youtube 视频](https://www.youtube.com/watch?v=JZ-5FbSxpbk)，作者是写`serverbuilds.net`指南、`JDM_WAAAT`、*的同一个人，遗憾的是，他似乎在这里没有账户*。对于我拥有的 6Tb 驱动器来说，这个过程实际上更容易，只需要卸下两个螺钉。

里面的驱动是*不是最好的*，但是给我用就可以了。如果你[买不带外壳](https://smile.amazon.com/WD-Blue-6TB-Hard-Drive/dp/B013HNYVCE)的话，它还会多花 50 *美元*，这对我来说还是没有意义。

## 网络

```
Netgear r7500v2 (already owned)
$40 - Dell PowerConnect 2748 48-port gigabit Ethernet switch
$20 - Cable Matters 5-Pack Snagless Cat 6a / Cat6a (SSTP/SFTP) Shielded Ethernet Cable in Blue 10 Feet 
```

Enter fullscreen mode Exit fullscreen mode

鉴于我计划创建一个带有 4 个千兆端口的链路聚合组(`LAG`)，我知道我需要一些额外的网络硬件。我的路由器很好，但是总共只有四个端口——其中大部分已经在使用，并且不支持链路聚合。

我回到易趣网，找到了一辆二手汽车`Dell PowerConnect 2748`,售价约为 40 美元*,运到了*,这对我现在和未来的需求来说都绰绰有余。更好的是，交换机是受管理的，因此我可以设置我的链路聚合，如果我愿意，还可以设置 VLANs。

对于电缆，我挑选了一些额外的`cat 6a`电缆，它们在各方面都比 5e 类好，而且价格略高。它们还支持 10Gbe，这意味着如果 10Gbe 网络价格合理，我可以升级，而不必购买新电缆。

## 软件

```
• FreeNAS 11
  • NextCloud
  • Plex Media Server
  • Docker 
```

Enter fullscreen mode Exit fullscreen mode

我原本打算使用`unRAID`进行这种设置，因为它的易用性对我很有吸引力，因为它能够将单个磁盘添加到现有的驱动器阵列中。然而，进一步的研究表明，这种优势的缺点包括随着设备的增加，驱动器的写入速度会降低。这是一个问题，因为我对这个系统的一个主要目标是从我的其他设备备份，以及 dropbox 的替代品，这样我就可以停止向他们运送我的钱。在这两种情况下，频繁的写入是常见的，这最终使我倾向于使用 [`FreeNAS`](https://freenas.org/) ，它有更好的(据说)写入速度，以及大部分相同的功能集。它也恰好是完全免费的(不像 unRAID ),是 OSS。

我已经开始使用的两个服务(freeNAS 世界中的插件)是 [`nextCloud`](https://nextcloud.com/) 和 [`plex`](http://plex.tv/) ，尽管 plex 目前正在抗议。

Docker 是在 FreeNAS 上安装和支持的，这很好。

## 加贺

```
$137 - CyberPower CP850PFCLCD PFC Sinewave UPS System, 850VA/510W
$50 - UNIVERSAL DEVICE PLACEMENT PORTABLE 12U FLOORSTANDING 19" SERVER RACK
$40 - Dell R610 R710 Static Server Rail Kit 2 Post 4 Post R G483G L K291G
$7.50 - Pasow 50pcs Cable Ties Reusable Fastening Wire Organizer
$70 - SiliconDust HDHomeRun Connect Duo 2-Tuner 
```

Enter fullscreen mode Exit fullscreen mode

这个清单上的前四项并不是真正的“额外项目”,但也不适合其他地方。我认为需要 UPS 来保护这样的系统。一个架子，虽然很小，却能创造空间管理和组织的奇迹。导轨与服务器一起使用，但也与机架一起使用，因为我没有从 MET 购买套件。

可重复使用的电缆扎带是纯粹的生活质量项目，但使处理网络和电源电缆更容易。

电视调谐器绝对是额外的，如果我不打算将它与 plex 出色的直播电视和 DVR 系统一起使用，我甚至不会想要它。

## 安装和故障排除

安装`freenas 11`很简单，但并不轻松。如果你走我最初尝试的路线，即运行闪存驱动器的安装，技术上你需要两个*闪存驱动器。一个用来放安装镜像，一个用来安装操作系统*到*。幸运的是，我有另一个闪存盘可以达到这个目的。不过这次安装的一个痛点是，这个系统——因为是旧的硬件——没有 usb 3。因此，使用 usb 2.0 安装到闪存驱动器上是一个非常耗时的过程。后来安装到 ssd 更快，主要是因为它的 SATA 接口，但也因为我发现了内置在该服务器中的远程管理实用程序，让您可以通过网络安装 ISO，这意味着我根本不必使用 USB 来安装系统，这很棒。*

正如我上面提到的，硬盘驱动器很容易拆卸，但是当我不小心将驱动器安装在盒中太靠前的位置时，我确实打破了驱动器盒，因此没有与背板接触。一个螺丝卡住了一个球童，我把它拆了(这完全是我的错)，这让我在易趣上又花了 7 美元买了一个球童。

一旦驱动器都正确安装在驱动器托架中，我就不得不将它们配置为各自的 RAID 0 阵列，以使它们能够通过 freenas，这是 freenas 非常不喜欢的，因为它消除了一些驱动器监控。如果我使用支持直接通过的 RAID 控制器会更好，但这个不支持，在这一点上，我厌倦了在这个项目上花钱。

ZFS 池很容易配置。我只使用了一个奇偶校验磁盘，这在这种大小的池(总容量为 36tb)中是不可取的，但为了奇偶校验而损失 12TB 的空间不是我目前愿意做的事情。

freenas 界面相当容易使用，在创建了上面的数据池之后，下一个挑战是我真正想要的软件。Plex 和 nextcloud。

Nextcloud 很简单，freenas 将 nextcloud 作为一个“插件”,它使用监狱的概念——类似于虚拟机——来运行你想要的软件，与系统的其余部分隔离。我在这里需要做的唯一配置是在基于 web 的 nextcloud 安装过程中设置用户名和密码。超级简单。

Plex 是另一回事。我希望能够直接从网络驱动器上访问 plex 文件(如图片和视频)。这意味着我必须在 freenas 中设置一个 windows samba 共享。Freenas 实际上使这变得微不足道，并有一个完整的向导来帮助你做到这一点。我设置了一个名为 share 的用户，可以从我的网络连接到它。太棒了。在这一点上，我认为我已经准备好了，因为 plex 也有一个由 freenas 提供的官方插件，就像 nextcloud 一样。不管我怎么做，我都无法加载 web 界面以通过 plex 的初始配置。这非常令人沮丧，几乎让我放弃了。

我终于[找到了一个指南](https://www.ixsystems.com/community/threads/tutorial-how-to-install-plex-in-a-freenas-11-2-jail.19412/)，它指导我在你自己的监狱里设置 plex，所以本质上是创建插件，但是是从头开始。经过一点点的尝试和错误，这个方法对我很有效。这意味着我的两个服务都如我所愿地开始运行了。

## 结论

这是一个*漫长的*过程——主要归功于 USB 2 和一个故障的闪存驱动器——涉及许多令人头痛的问题和反复试验。以至于我知道我在创建这个构建日志时错过了细节。如果你对任何步骤有任何问题，或者我做的任何事情看起来跳过了时间，请留下评论，我会努力更新和解决它。我最终没有在这个系统上设置 docker 环境，而是使用 jails 来实现这个目的。由于 freenas 分配内存和 cpu 资源的方式，监狱要简单得多。有什么好主意让我用这个做点什么吗？*也*请留言评论。

感谢你的阅读，并和我一起经历这次冒险。