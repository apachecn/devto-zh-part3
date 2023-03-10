# OpenVPN: OpenVPN 访问服务器设置和 AWS VPC 对等配置

> 原文：<https://dev.to/setevoy/openvpn-openvpn-access-server-set-up-and-aws-vpc-peering-configuration-5fpg>

[![](img/2f17955485173af6a486496a669b8802.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wzHUWC0j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2014/07/openvpn-logo-1.png)*OpenVPN 接入服务器*准备好使用 OpenVPN 服务器，只需要最低限度的配置。

免费版允许你有两个客户端。如果您需要更多，您可以购买额外的许可证。

### 基础设施描述

目前，为了访问我们的资源，如 Jenkins、Nexus 等，我们在 AWS 安全组中使用允许规则，每个用户都有自己的 IP 地址。

当我们是一个小团队时，这是一个不错的方法，但随着我们的快速发展，越来越多的开发人员/QA-必须找到一个更好的解决方案。

在这篇文章中，我们将使用 OpenVPN 访问服务器 AWS AMI 建立一个 OpenVPN 服务。

毕竟，一切都将是这样的:

[![](img/c126ea83e02e4c2ff9ba742468b42891.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/openvnas-vpc.png)

你可以在这里> > > 找到 OpenVPN 的文档，一些 VPC 对等的例子将会在这篇文章中展示。

### AWS:创建 OpenVPN 即服务

#### 运行 EC2

找到 AMI:

[![](img/3cb24428a813885f0987e1feafe0a1f7.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_141753.png)

你可以稍后添加许可证，所以现在，让我们使用它的免费版本。

举一个 *t2.micro* 的例子——这时候就足够公平了:

[![](img/a9aeb82a62936fa723d4b4feabf2036e.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_141844.png)

#### VPC

为我们的 VPN 创建一个新的 VPC:

[![](img/6f5a1b2635cf0db5235c0b9ca3f10c41.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_141911.png)

因为我们将使用 VPC peering-确保你的网络没有重叠。

在这种情况下——我有詹金斯 VPN 与 *10.0.4.0/24* CIDR 和 VPC VPN CIDR—*10 . 0 . 9 . 0/24*:

[![](img/6e84356ae36b0acb58102415459e368c.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_142535.png)

在此 VPC 中创建新子网:

[![](img/b4aa05f47baafe207166c307c30cacc0.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_142639.png)

使用这些 VPC 和子网配置新的 EC2 网络:

[![](img/f85e99cdc1cda657ac878166d9114713.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_142725.png)

##### 互联网网关

创建互联网网关(IGW ),以便从您的新 VPC 访问互联网:

[![](img/45a6d0fdd952956a73734fea96093f1e.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143646.png)

[![](img/15a80c1c9a21755327b13f668334e060.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143710.png)

将此 IGW 附加到您的 VPC:

[![](img/ac22f5c291224ef3b863045cb865c477.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143744-1.png)

[![](img/a38d5028fe0b8697debdbe625d86c531.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143801.png)

##### 路由表

接下来，必须更新一个*路由表*，用于通过创建的 IGW 将流量路由到 *0.0.0.0/0* 网络(互联网)。

转到您的新子网，找到*路由表*选项卡，点击它:

[![](img/437bd70a6f4b366a72d505ea77c6bbf5.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_164934.png)

添加一条途经 IGW 的新路线:

[![](img/423fb45cd1d152d6e52a402bf98c446d.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_165044.png)

#### 安全组

AWS 将建议您使用一个具有预定义规则集的安全组。

由于这是一个概念验证，您可以保持原样:

*   TCP 22 — SSH 访问
*   TCP 943 — OpenVPN 作为管理用户界面访问端口
*   TCP 443 — OpenVPN 作为用户 UI 访问端口
*   UDP 1194 —用于 VPN 客户端的 OpenVPN UDP

[![](img/9c23cd3387aa0181dbbc90f2bf5dec0d.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_142819.png)

在某种生产设置中，您必须仅通过可信 IP 来限制 943 和 22。

启动实例，为访问创建新的 RSA 密钥:

[![](img/2c4c4f56aa3feb7cb5c3e6ee43b607f4.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_142936.png)

#### 弹性 IP

创建一个 EIP 以用于您的新主机:

[![](img/2ba3b0af74981cd9473810d00d54e446.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143444.png)

附上:

[![](img/7aa0bff126b20b9d59ac0ed877091a01.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143509.png)

[![](img/16e405354476ddd57239cebf25b16f88.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_143928.png)

#### 源/目的地检查禁用

对于 NAT 的正确工作需要禁用[源/目标检查(见 post】](https://rtfm.co.ua/aws-migraciya-rtfm-2-5-nastrojka-nat-na-bastion-ec2-kak-zamena-nat-gateway/)[AWS:rtfm 2.5-【rt FM 2.5】—NAT 堡垒 EC 2-【NAT gateway】](https://rtfm.co.ua/aws-migraciya-rtfm-2-5-nastrojka-nat-na-bastion-ec2-kak-zamena-nat-gateway/)

[![](img/78e540cd9697f4080c5f309656467188.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_170508.png)

现在我们已经完成了 EC2 让我们开始配置新的 OpenVPN 服务。

### OpenVPN AS 设置

使用 *openvpnas* 登录到主机的 SSH:

```
$ ssh -i dev-vpn-eu-west-1.pem openvpnas@52.18.110.226 
```

阅读:-)许可，接受它:

[![](img/e7a8f682ffd60b6ad53bf6f2a36f60dd.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_165411.png)

#### 初始化和首次配置

首次登录时，OpenVPN AS 将自动启动其向导。

您可以稍后使用`ovpn-init`运行它:

```
$ sudo ovpn-init --ec2 
```

现在，您可以回答 *Yes* 这里的所有内容都是默认值:

1.  这将是主访问服务器节点吗？//是
2.  请指定管理 Web UI 要使用的网络接口和 IP 地址:(1)所有接口:0.0.0.0 (2) eth0: 10.0.9.8 // 2
3.  请为管理 Web 用户界面指定端口号。//是–943
4.  请指定 OpenVPN 守护程序的 TCP 端口号//是–443
5.  默认情况下，客户端流量应该通过 VPN 路由吗？//否–我们只会将流量路由到我们自己的资源
6.  默认情况下，客户端 DNS 流量应该通过 VPN 路由吗？//没有
7.  通过内部数据库使用本地身份验证？//是
8.  检测到专用子网:['10.0.9.0/24']默认情况下，客户端是否可以访问专用子网？//是
9.  您希望以“openvpn”的身份登录到管理用户界面吗？//是
10.  >请指定您的 OpenVPN-AS 许可证密钥(或留空以便以后指定)://留空

[![](img/5ce744dfc3cb23dfe57a1f914893e2e2.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_170054.png)

设置`openvpn`用户的密码:

```
$ openvpnas@openvpnas2:~$ sudo passwd openvpn
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully 
```

现在，您可以使用之前创建的 EIP 打开您的管理页面——*[https://52.18.110.226:943/admin](https://52.18.110.226:943/admin)*。

因为我们没有配置主机名/URL，所以在此同意:

> 您的连接不是私人的
> 
> 攻击者可能试图从**52.18.110.226**窃取你的信息(例如，密码、信息或信用卡)。了解更多信息
> 
> NET::ERR _ CERT _ AUTHORITY _ valid

(检查 [OpenVPN: SSL 和主机名配置](https://rtfm.co.ua/en/openvpn-ssl-and-hostname-configuration/) post -这是后来添加的)

使用由`passwd`创建的 *openvpn* 用户和密码登录管理页面:

[![](img/97ac52451316b2cced4467e79be0c755.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_171015.png)

[![](img/1c129d20f7b9b84efb2095909342f1a1.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_171033.png)

#### VPN 客户端连接

转到用户的 UI(最后没有`/admin`)—*【https://52.18.110.226:943/?src=connect】T2*:

[![](img/9a41f0537eae840283426c0c5087c490.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_171743.png)

[![](img/ee4db3b50ab5dc7e7db50367da564e3d.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_171814.png)

自己点击*(用户锁定配置文件)*，下载`client.ovpn`文件，其中包含您本地 VPN 客户端的设置:

```
$ cat ~/.openvpn/client.ovpn
Automatically generated OpenVPN client config file
Generated on Wed Feb 20 15:19:38 2019 by openvpnas2
Default Cipher
cipher AES-256-CBC
Note: this config file contains inline private keys
and therefore should be kept confidential!
Note: this configuration is user-locked to the username below
OVPN\_ACCESS\_SERVER\_USERNAME=openvpn
... 
```

在你的本地 PC 上安装`openvpn`包，这里使用的是 Arch Linux:

```
$ sudo pacman -S openvpn 
```

使用`--auth-user-pass`选项启动:

```
$ sudo openvpn --config ~/.openvpn/client.ovpn --auth-user-pass
Wed Feb 20 17:22:42 2019 OpenVPN 2.4.6 x86\_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Apr 24 2018
Wed Feb 20 17:22:42 2019 library versions: OpenSSL 1.1.1a  20 Nov 2018, LZO 2.10
Enter Auth Username: openvpn
Enter Auth Password: \*\*\*\*\*\*\*
...
Wed Feb 20 17:22:51 2019 ROUTE\_GATEWAY 172.16.64.1/255.255.252.0 IFACE=wlp2s0 HWADDR=10:f0:05:64:0a:73
Wed Feb 20 17:22:51 2019 TUN/TAP device tun0 opened
Wed Feb 20 17:22:51 2019 TUN/TAP TX queue length set to 100
Wed Feb 20 17:22:51 2019 do\_ifconfig, tt-\>did\_ifconfig\_ipv6\_setup=0
Wed Feb 20 17:22:51 2019 /usr/bin/ip link set dev tun0 up mtu 1500
Wed Feb 20 17:22:51 2019 /usr/bin/ip addr add dev tun0 172.27.232.3/21 broadcast 172.27.239.255
Wed Feb 20 17:22:56 2019 ROUTE remote\_host is NOT LOCAL
Wed Feb 20 17:22:56 2019 /usr/bin/ip route add 52.18.110.226/32 via 172.16.64.1
Wed Feb 20 17:22:56 2019 /usr/bin/ip route add 10.0.9.0/24 metric 101 via 172.27.232.1
Wed Feb 20 17:22:56 2019 /usr/bin/ip route add 172.27.224.0/20 metric 101 via 172.27.232.1
Wed Feb 20 17:22:56 2019 Initialization Sequence Completed 
```

现在，让我们检查一些布线。

首先，让我们向 CloudFlare 发出一个请求，它必须通过我办公室的路由器/网关传递:

```
$ traceroute 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 60 byte packets
1  \_gateway (172.16.64.1)  3.109 ms  3.213 ms  3.428 ms
2  ru-german.relc.com (194.183.169.25)  4.771 ms  5.148 ms  6.039 ms
3  \* \* \*
4  ex4.thinx.pl (212.91.2.13)  16.468 ms  16.778 ms  17.277 ms
5  cloudflare.thinx.pl (212.91.0.28)  23.842 ms  24.154 ms  24.386 ms
6  one.one.one.one (1.1.1.1)  17.787 ms  15.023 ms  15.199 ms 
```

是的，它是。

现在来看 OpenVPN 主机——必须直接进入:

```
$ traceroute 10.0.9.8
traceroute to 10.0.9.8 (10.0.9.8), 30 hops max, 60 byte packets
1  10.0.9.8 (10.0.9.8)  52.095 ms  52.149 ms  53.512 ms 
```

很好。

#### VPN-隧道流量路由

出于测试目的，让我们在放置 OpenVPN 的同一 VPC 中添加一个新的 EC2:

[![](img/c3898bfe6f35a895f13713c696df9712.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_174010.png)

[![](img/a8c220d00b859a031ba03bac9ff575d0.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_174106.png)

让我们再查查路线。

在本地 OpenVPN 客户端启动期间，您必须注意到一条消息，其中包含要在您的系统中添加的新路由:

> 2019 年 2 月 20 日星期三 17:22:56/usr/bin/IP 路由添加 172.27.224.0/20 公制 101 通过 172.27.232.1

检查它–使用新实例的私有 IP 尝试 SSH:

```
$ ssh -i dev-vpn-eu-west-1.pem ubuntu@10.0.9.6
The authenticity of host '10.0.9.6 (10.0.9.6)' can't be established.
ECDSA key fingerprint is SHA256:6ldjTtHvQJviBb/9aXwFvwh7nwKjOdpePCxO6TddAJA.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.0.9.6' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-1021-aws x86\_64)
...
ubuntu@ip-10-0-9-6:~$ 
```

一切都好。

### VPC 的对等

下一步是在我们的工作站、VPN 服务器和 Jenkin 的 VPC 之间配置 VPC 对等和流量路由。

阅读更多关于 VPC-对等[这里> > >](https://rtfm.co.ua/aws-nastrojka-vpc-peering/) ，

创建新的 *VPC 对等连接*:

[![](img/eedfe7b0bfabb0ad86c8a7a93000e6af.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_174420.png)

[![](img/46c8cfa84498b41403aaa2cee7f1afb8.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_174441.png)

接受其请求:

[![](img/b5171de689de4122235af8a3ebd205b2.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_174605.png)

#### VPC-对等路由

如果您尝试使用 Jenkins 的私有 IP 连接到 Jenkins，现在这将不起作用:

```
ubuntu@ip-10-0-9-6:~$ ssh admin@10.0.4.13
ssh: connect to host 10.0.4.13 port 22: Connection timed out 
```

因为 *10.0.9.0/24* (OpenVPN VPC)和 *10.0.4.0/24* (詹金斯 VPC)网络之间没有添加路由。

所以现在我们必须添加两条新路线:

1.  在 Jenkins 路由表中-*10 . 0 . 4 . 0/28 =>10 . 0 . 9 . 0/24*通过上面创建的 VPC 对等
2.  在 OpenVPN 路由表中-*10 . 0 . 4 . 0/28 =>10 . 0 . 9 . 0/24*通过上面创建的 VPC 对等

编辑*Jenkins rt b-9597 e6ec | Jenkins-dev-route-table*表:

[![](img/092f1216eabddcc46bc8e29db9c41e8d.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_175149.png)

添加新路线:

[![](img/0853c12376f6fcf945000af157976496.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_175241.png)

对 VPN 的路由表重复上述步骤:

[![](img/b901dc6a1c8247aff37a4940c46d94c7.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_175335.png)

从 VPN 网络检查:

```
ubuntu@ip-10-0-9-6:~$ ping 10.0.4.13
PING 10.0.4.13 (10.0.4.13) 56(84) bytes of data.
64 bytes from 10.0.4.13: icmp\_seq=1 ttl=64 time=0.470 ms
64 bytes from 10.0.4.13: icmp\_seq=2 ttl=64 time=0.483 ms 
```

从 Jenkins 主机到 VPN 网络再到任何主机:

```
admin@jenkins-dev:~$ ping 10.0.9.6
PING 10.0.9.6 (10.0.9.6) 56(84) bytes of data.
64 bytes from 10.0.9.6: icmp\_seq=1 ttl=64 time=0.437 ms
64 bytes from 10.0.9.6: icmp\_seq=2 ttl=64 time=0.914 ms 
```

请记住，Jenkins 有自己的*安全组*——在那里添加新的允许规则。

[![](img/42e87a4b3094204651e16a2cfa2d79c4.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_180338.png)

从 VPN 网络检查 SSH:

```
ubuntu@ip-10-0-9-6:~$ ssh admin@10.0.4.13
The authenticity of host '10.0.4.13 (10.0.4.13)' can't be established.
ECDSA key fingerprint is SHA256:+0LP1+neT4udq96jFLdLpjuvTrWCzlAt5GZZpfw2rKI.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.0.4.13' (ECDSA) to the list of known hosts.
admin@10.0.4.13: Permission denied (publickey). 
```

然后回来:

```
admin@jenkins-dev:~$ ssh 10.0.9.6
The authenticity of host '10.0.9.6 (10.0.9.6)' can't be established.
ECDSA key fingerprint is SHA256:6ldjTtHvQJviBb/9aXwFvwh7nwKjOdpePCxO6TddAJA.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.0.9.6' (ECDSA) to the list of known hosts.
Permission denied (publickey). 
```

很好。

#### VPN 路由

回到您的工作站，检查流量将如何使用 Jenkins 主机的私有 IP 路由到该主机:

```
$ traceroute 10.0.4.13
traceroute to 10.0.4.13 (10.0.4.13), 30 hops max, 60 byte packets
1  \_gateway (172.16.64.1)  3.243 ms  3.453 ms  3.656 ms
2  ru-german.relc.com (194.183.169.25)  3.962 ms  4.221 ms  4.642 ms
... 
```

它通过办公室的路由器传递——这绝对不是我们想要的。

进入 *VPN 管理= > VPN 设置*–*[https://52.18.110.226:943/admin/vpn\_settings](https://52.18.110.226:943/admin/vpn%5C_settings)*:

[![](img/a4dfc5c656e81490dc96ccc36c153ad7.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_182317.png)

为 *10.0.4.0/24* 子网添加新的 NAT 规则:

[![](img/8096eff8d44364c835240ca09046da5b.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_182349.png)

保存，按*更新正在运行的服务器*:

[![](img/fe5a528c2798aae67534de62460e94f0.png "AWS: настройка OpenVPN Access Server и VPC peerings")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190220_182501.png)

重新启动本地客户端:

```
$ sudo openvpn --config ~/.openvpn/client.ovpn --auth-user-pass
...
Wed Feb 20 18:25:37 2019 ROUTE remote\_host is NOT LOCAL
Wed Feb 20 18:25:37 2019 /usr/bin/ip route add 52.18.110.226/32 via 172.16.64.1
Wed Feb 20 18:25:37 2019 /usr/bin/ip route add 10.0.4.0/24 metric 101 via 172.27.232.1
Wed Feb 20 18:25:37 2019 /usr/bin/ip route add 10.0.9.0/24 metric 101 via 172.27.232.1
Wed Feb 20 18:25:37 2019 /usr/bin/ip route add 172.27.224.0/20 metric 101 via 172.27.232.1
Wed Feb 20 18:25:37 2019 Initialization Sequence Completed 
```

这是我们的新路线—*10 . 0 . 4 . 0/24 公制 101 途经 172.27.232.1* 。

检查您的本地路由表:

```
18:26:28 [setevoy@setevoy-arch-work ~]  $ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.16.64.1     0.0.0.0         UG    303    0        0 wlp2s0
10.0.4.0        172.27.232.1    255.255.255.0   UG    101    0        0 tun0
10.0.9.0        172.27.232.1    255.255.255.0   UG    101    0        0 tun0
52.18.110.226   172.16.64.1     255.255.255.255 UGH   0      0        0 wlp2s0 
```

再次检查跟踪:

```
$ traceroute 10.0.4.13
traceroute to 10.0.4.13 (10.0.4.13), 30 hops max, 60 byte packets
1  172.27.232.1 (172.27.232.1)  55.090 ms  55.207 ms  55.399 ms
... 
```

现在它通过 OpenVPN IP 作为网关。

检查 SSH 是否通过私有 IP 工作:

```
18:27:41 [setevoy@setevoy-arch-work ~]  $ ssh -i jenkins-dev-eu-west-1.pem admin@10.0.4.13
...
admin@jenkins-dev:~$ 
```

*“管用！”* (c) :-)

### [DNA](#dns)

最后要考虑的是 DNS 设置。

由于我们的 Jenkins 必须使用其公共 IP 和域名*从互联网上的几个地址访问 dev.ci.example.com*，并通过其私有 IP-使用 VPN 连接-需要找到一种方法将其解析到不同的 IP。

查看 [OpenVPN: DNS 和 dnsmasq 配置](https://rtfm.co.ua/en/openvpn-dns-and-dnsmasq-configuration/)帖子了解详情。

完成了。

### 类似的帖子

*   <small>02/21/2019</small>[OpenVPN:настрокаOpenVPN 接入服务器и AWS VPC 对等](https://rtfm.co.ua/openvpn-nastrojka-openvpn-access-server-i-aws-vpc-peering/) <small>(0)</small>
*   <small>02/22/2019</small>[OpenVPN:DNS 和 dnsmasq 配置](https://rtfm.co.ua/en/openvpn-dns-and-dnsmasq-configuration/) <small>(0)</small>
*   <small>03/02/2019</small>[Arch Linux:OpenVPN–resolv . conf 未更新](https://rtfm.co.ua/en/arch-linux-openvpn-resolv-conf-not-updated/) <small>(0)</small>
*   <small>2019 年 2 月 22 日</small> [【开放 VPN:каоаоооооооооооооооооазоаоаоаоаоаоаоаоаоаоазо】](https://rtfm.co.ua/openvpn-nastrojki-dns-i-dnsmasq/)
*   <small>03/02/2019</small>[arch Linux:openvpn–необновляетсяresolv . conf](https://rtfm.co.ua/arch-linux-openvpn-ne-obnovlyaetsya-resolv-conf/)<small>(0)</small>