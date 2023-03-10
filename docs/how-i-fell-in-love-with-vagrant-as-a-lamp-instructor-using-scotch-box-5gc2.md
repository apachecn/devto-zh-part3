# 我是如何爱上一个用苏格兰盒子做灯光师的流浪汉的

> 原文：<https://dev.to/vetswhocode/how-i-fell-in-love-with-vagrant-as-a-lamp-instructor-using-scotch-box-5gc2>

[![A bunch of the logos for the tech that we're using, LAMP, XAMPP, WAMP, MAMP, Scotch Box, Vagrant](img/8d9b740259e8d82413289badda21d544.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nyT0mmRS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wgkpzsq5ov5ddyvh3fgw.png)

# 我的假设

#### 基础知识

我是 web 开发训练营的讲师，在俱乐部教 LAMP stack。在现场，我们提供电脑给学生使用。有时候学生会用自己的。

#### 问题

我开始为新开发人员考虑一些很酷的问题。教这些学生最相关的技能是什么？对于设置他们的开发环境，代码新手真正需要了解多少？当从事一项新工作时，他们实际上会从零开始建立自己的环境吗？我需要多少时间来复习课堂上设置的环境？

#### 我个人的回答

我是一个第二职业开发人员，我已经设置了我所有的环境。即使计算机的内部工作与代码无关，它仍然与开发相关，所以我认为教授这一点很重要。

#### 别人可能在乎的原因

一些招聘经理期望新开发人员应该在工作之外开发应用程序，以显示对该行业的激励和承诺。我认为在对全新开发人员的技术访谈中，我没有看到很多关于他们的环境是如何设置的问题。我认为一个专业的开发环境可以提供谈话要点，指出惊人数量的技术技能，这对一个团队来说是非常有价值的。

# 我的旅程

我在编写代码的兽医学院学习开发，这是一门完整的 JS 堆栈课程。我个人更关注 JS 语言基础，很少关注节点或服务器部署。这让我获得了第一份工作，但当我到达那里时，我在入职的前几周感到非常失落。那是一家使用 [MAMP](https://www.mamp.info) 的 PHP 商店。我的大部分工作都是在前端，很少接触 PHP 和后端。

那份工作之后，我开始教书。我用我熟悉的技术教学。因此，我和我的学生的开发环境在 Windows 上使用 MAMP。MAMP 代表 Mac + Apache + MySQL + PHP，这意味着我在 Windows 上使用一个 Mac 版软件只是因为我对它感到舒服。因此，当我在一些机器上遇到 MAMP 的问题时，为了解决问题，我会告诉他们尝试使用代表“跨平台”+ Apache + MySQL + PHP + Perl 的 [XAMPP](https://www.apachefriends.org/index.html) 。不出所料，它有时会坏掉，所以我做了什么？我会说让他们检查一下 [WAMP](http://www.wampserver.com/) ，因为它是针对 Windows 的。所以，现在我们有一个很好的开发团队来取得成功，对不对？我们没有强制 PHP/*AMP/MySQL 版本化。所以每个人都在不同的系统类型上使用不同的软件。这导致了大量的 bug。我把它记为现实世界将要发生的事情给他们上的一堂好课。上了几节课，在下一节课开始前休息了一段时间，我已经能够做一些兼职项目了。

#### 第一步是承认你有问题

认识到这个问题后，就决定如何改进。我有一些空闲时间来启动和运行这个项目，但不是全部。我没有信心能够在没有做过 Docker 的情况下让它及时工作。我以前听说过流浪者，但它看起来仍然很吓人。我以前曾手动配置过虚拟机(vm ),但考虑如何使用您创建的虚拟机快照来协调整个教室仍然是一件令人头疼的事情。杰罗姆·哈达威建议我去试试[苏格兰威士忌](https://box.scotch.io/)。这差不多是我开始一个项目所需要的全部说服力。

##### 从苏格兰盒子到流浪汉(技术货)

如何苏格兰盒

1.  下载[虚拟盒子](https://www.virtualbox.org/wiki/Downloads)
2.  下载[流浪汉](https://www.vagrantup.com/downloads.html)
3.  `git clone https://github.com/scotch-io/scotch-box my-project`
4.  `cd my-project`
5.  `vagrant up`

*轰！*用一个基本的苏格兰盒子启动并运行。用你的浏览器导航到他们给你的 IP 地址( [http://192.168.33.10](http://192.168.33.10) )，你应该会看到一个预建的登陆页面。Ssh ( `vagrant ssh`)进入框中可以看到`var/www`直接链接到你开始流浪的公共文件夹的文件系统。

现在，我做错的是我开始快速打开和关闭盒子，而不是检查正确的关闭。我开始注意到一个奇怪的行为，我可以 ssh 到框中，但当我导航到 url 时，我会得到`site is unreachable`。

当我调用`vagrant up`时，我注意到一些警告。

例如，其中一个警告:

```
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 5.1.21
    default: VirtualBox Version: 6.0 
```

[来宾添加功能](https://www.virtualbox.org/manual/ch04.html)据我所知，它安装在 VirtualBox 内部的虚拟机上，是这两者进行通信的方式。因此，如果 Guest Additions 被破坏，我会认为没有任何东西会在机器上工作，因为它将无法运行任何命令。

所以我跟随[凯文·范·松内维尔德的帖子](https://kvz.io/blog/2013/01/16/vagrant-tip-keep-virtualbox-guest-additions-in-sync/)，找到了这个命令`vagrant plugin install vagrant-vbguest`。有很多关于如何更新客人添加的问题和答案。*没有变化*。

我尝试了一大堆不同的东西，像不同的流浪命令，重新安装一切，吹走软件包，仍然没有骰子*没有变化*。幸运的是，俱乐部里有一个疯狂聪明的家伙，他走过并在适当的时候说“如果你遇到路障，请告诉我”。我把我的笔记本电脑拿给他，我们从未和流浪者一起工作过，但已经在红帽工作了很长时间，我们开始一起排除故障。

# 以下是他采取的一些步骤:

*   打开盒子，看看是否够得着。

```
└──╼ $ping 192.168.33.10
PING 192.168.33.10 (192.168.33.10) 56(84) bytes of data.
64 bytes from 192.168.33.10: icmp_seq=1 ttl=64 time=0.487 ms
64 bytes from 192.168.33.10: icmp_seq=1 ttl=64 time=0.574 ms (DUP!)
64 bytes from 192.168.33.10: icmp_seq=2 ttl=64 time=0.544 ms
64 bytes from 192.168.33.10: icmp_seq=2 ttl=64 time=0.544 ms (DUP!) 
```

> 那些`(DUP!)`意味着存在某种 TCP 错误。

*   `ifconfig`查看本地接口是如何连接到机器的。

```
└──╼ $ifconfig 
vboxnet0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
    inet 192.168.33.1  netmask 255.255.255.0  broadcast 192.168.33.255 
```

> 这表明 virtualbox 路由器被设置到了`192.168.33.1`接口。

*   是时候`ping`路由器了

```
└──╼ $ping 192.168.33.1
PING 192.168.33.1 (192.168.33.1) 56(84) bytes of data.
64 bytes from 192.168.33.1: icmp_seq=1 ttl=64 time=0.108 ms
64 bytes from 192.168.33.1: icmp_seq=2 ttl=64 time=0.125 ms
64 bytes from 192.168.33.1: icmp_seq=3 ttl=64 time=0.112 ms 
```

> 看起来不错，因为我们看到了 ping 返回所需的时间

*   `curl`页面，看看有什么回来

```
└──╼ $curl 192.168.33.10:80
curl: (56) Recv failure: Connection reset by peer 
```

> curl 发出一个普通的 GET http 请求，我们得到一个`Connection reset`错误。这加强了这可能是 TCP 错误的想法。TCP 按顺序发送数据包，如果数据包乱序或损坏，TCP 会抱怨，我们会看到这样的情况，连接会关闭。

*   用`route -n`检查路由表

```
└──╼ $route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.1        0.0.0.0         UG    600    0        0 wlan0
10.0.0.0        0.0.0.0         255.255.0.0     U     600    0        0 wlan0
192.168.33.0    0.0.0.0         255.255.255.0   U     0      0        0 vboxnet0 
```

> 我们正在检查是否存在某种类型的路由环路。但希望渺茫。一切似乎都没问题。

我们浏览并查看了我之前运行的所有命令，看是否有任何提到流浪者的内容。此外，我们还打开了盒子，检查了那边的路由并列出了接口。列出所有这些东西后，它开始神奇地工作了。

这不是我们想要的答案。我们想知道是什么坏了。归功于魔法，我感谢他的麻烦，并回去学习流浪汉和苏格兰盒。

那天晚些时候，我开始有同样的问题。我下班了，想了一会儿。他预感到这是一个网络问题，这让我从我的主人有连接问题而不是流浪者有问题的角度来解决这个问题。

## 答案

我收到的另一条信息是:

`==> default: Fixed port collision for 22 => 2222\. Now on port 2201.`

在我看来，这不像是一个错误或警告。它只是看起来像发生过的事情，并不是一个问题。我谷歌了一下“如何检查正在运行的进程”，找到了`sudo lsof -i -P -n`。我发现实际上有两个单独的进程在监听端口 2222 和 2201。因此，我所要做的就是杀死第一个进程，并重新加载流浪者，一切都结束了漂亮的工作。

# 我告诉你这一切就是为了告诉你这个

我不知道要花多长时间，我才能一头扎进文档里学会流浪。我不知道自己是否会对自己设置的虚拟机感到舒服。我认为对开发者来说，学习相关的技能是非常重要的。我不认为 Scotch Box 会帮助我适应一般的 Vagrant、VirtualBox 和虚拟机。这是一次非常棒的体验，我迫不及待地想继续使用这些技术并继续发展。