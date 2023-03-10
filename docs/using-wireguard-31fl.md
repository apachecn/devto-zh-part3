# 使用铁丝网

> 原文：<https://dev.to/luzero/using-wireguard-31fl>

wireguard 是一个现代、安全、快速的 vpn 隧道，安装极其简单，几乎在任何地方都可以使用。

由于[这个](https://git.zx2c4.com/wireguard-rs/about/)看起来挺有意思，所以花了一点赌注玩了一下，就想到了写一个小教程。

我通常使用 [Gentoo](https://gentoo.org) (和 [macos](https://apple.com) )所以这个指南是关于 **Gentoo** 。

## 一般概念

Wireguard 建立由[公钥](https://en.wikipedia.org/wiki/Public-key_cryptography)标识的**对等体**，并管理[虚拟网络接口](https://en.wikipedia.org/wiki/TUN/TAP)以及它们之间的路由(可选)。

**服务器**只是一个**对等点**，它知道对等点的 loots，而**客户端**知道如何直接到达**服务器**，如此而已。

## 在 Gentoo 中设置

Linux 上的 Wireguard 是作为内核模块实现的。

所以一般来说，你必须构建模块和用户空间工具(`wg`)。
如果您想拥有一些高级功能，请确保您的内核具有以下设置:

> `IP_ADVANCED_ROUTER`
> `IP_MULTIPLE_TABLES`
> T2】

之后使用`emerge`将得到你需要的一切:

```
$ emerge wireguard 
```

### 工具

工具的默认发行版附带了`wg`命令和一个名为`wg-quick`的助手脚本，该脚本使启动和关闭虚拟网络接口变得更加容易。

```
wg help
Usage: wg <cmd> [<args>]

Available subcommands:
  show: Shows the current configuration and device information
  showconf: Shows the current configuration of a given WireGuard interface, for use with `setconf'
  set: Change the current configuration, add peers, remove peers, or change peers
  setconf: Applies a configuration file to a WireGuard interface
  addconf: Appends a configuration file to a WireGuard interface
  genkey: Generates a new private key and writes it to stdout
  genpsk: Generates a new preshared key and writes it to stdout
  pubkey: Reads a private key from stdin and writes a public key to stdout
You may pass `--help' to any of these subcommands to view usage. 
```

```
Usage: wg-quick [ up | down | save | strip ] [ CONFIG_FILE | INTERFACE ]

  CONFIG_FILE is a configuration file, whose filename is the interface name
  followed by `.conf'. Otherwise, INTERFACE is an interface name, with
  configuration found at /etc/wireguard/INTERFACE.conf. It is to be readable
  by wg(8)'s `setconf' sub-command, with the exception of the following additions
  to the [Interface] section, which are handled by wg-quick:

  - Address: may be specified one or more times and contains one or more
    IP addresses (with an optional CIDR mask) to be set for the interface.
  - DNS: an optional DNS server to use while the device is up.
  - MTU: an optional MTU for the interface; if unspecified, auto-calculated.
  - Table: an optional routing table to which routes will be added; if
    unspecified or `auto', the default table is used. If `off', no routes
    are added.
  - PreUp, PostUp, PreDown, PostDown: script snippets which will be executed
    by bash(1) at the corresponding phases of the link, most commonly used
    to configure DNS. The string `%i' is expanded to INTERFACE.
  - SaveConfig: if set to `true', the configuration is saved from the current
    state of the interface upon shutdown.

See wg-quick(8) for more info and examples. 
```

## 创建一个配置

Wireguard 非常简单，你可以用你最喜欢的文本编辑器准备一个配置，或者通过手动设置*虚拟网络设备*，然后保存结果`wg showconf`来生成一个配置。

然后，配置文件可以增加特定于`wg-quick`的选项(如`Address`)或直接传递给`wg setconf`，而其他网络细节由您常用的工具管理(如 [ip](https://wiki.gentoo.org/wiki/Iproute2) )。

### 创建您的密钥

第一步是创建标识您的*对等方*的公钥-私钥对。

*   `wg genkey`为您生成一个私钥。
*   你把它交给`wg pubkey`来获得你的公钥。

单行:

```
$ wg genkey | tee privkey | wg pubkey > pubkey 
```

###准备一个配置文件
`wg-quick`和`wg setconf`都使用一个类似于 *ini 的*配置文件。

如果你把它放在`/etc/wireguard/${ifname}.conf`中，那么`wg-quick`将只需要接口名，并为你查找。

最低配置需要一个`[Interface]`和一个`[Peer]`集。
您可以稍后添加额外的*同行*。
一个*服务器*将指定它的`ListenPort`，并通过它们的`PublicKey`来识别*对等体*。

```
[Interface]
Address = 192.168.2.1/24
ListenPort = 51820
PrivateKey = <key>

[Peer]
PublicKey = <key>
AllowedIPs = 192.168.2.2/32 
```

一个*客户端*会有一个*对等体*，它定义了一个`EndPoint`，并且可以选择不在其*接口*描述中指定`ListenPort`。

```
[Interface]
PrivateKey = <key>
Address = 192.168.2.2/24

[Peer]
PublicKey = <key>
AllowedIPs = 192.168.2.0/24
Endpoint = <ip>:<port> 
```

通过`AllowedIPs`掩码，您可以指定想要通过 *vpn* 路由多少流量。
通过设置`0.0.0.0/0`你告诉你想通过它路由所有的流量。

> **注意** : `Address`是`wg-quick`特有的选项。

### 使用一个配置

假设你已经创建了`/etc/wireguard/wg0.conf` :
，那么`wg-quick`很容易使用

```
$ wg-quick up wg0 
```

```
$ wg-quick down wg0 
```

如果您使用的是版本`0.6.1`的[netifrc](https://wiki.gentoo.org/wiki/Netifrc)wire guard 受支持，您可以使用如下配置:

```
config_wg0="192.168.2.4/24"
wireguard_wg0="/etc/wireguard/wg0.conf" 
```

与上面的`wg0.conf`文件类似，但是去掉了`wg-quick`特定的选项。

## 总结归纳

与几乎所有其他 *vpn* 解决方案相比，Wireguard 的设置非常容易。

非 linux 系统目前可以使用一个 [go 实现](https://git.zx2c4.com/wireguard-go/about/)，未来可以使用一个 [rust 实现](https://git.zx2c4.com/wireguard-rs/about/)(欢迎帮助)。

Android 和 [macos](https://itunes.apple.com/us/app/wireguard/id1451685025) 已经有了一些漂亮的前端，即使在这些平台上也能轻松设置。

希望你喜欢:)