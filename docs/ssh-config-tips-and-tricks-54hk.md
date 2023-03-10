# SSH 配置提示和技巧

> 原文：<https://dev.to/cpu/ssh-config-tips-and-tricks-54hk>

以下是我最喜欢的几个选项的快速浏览，从最常见的到不太常见的。

## 友好的服务器名称

通常，当我使用云提供商的新服务器时，我不想麻烦给它一个域名。然而，在`ssh`命令中反复输入 IP 地址变得很烦人。谢天谢地`~/.ssh/config`有解:`Host`和`Hostname`。

例如，如果我有一个 IP 地址为`10.11.12.13`的测试液滴，我可以在我的`~/.ssh/config`中添加一个条目，比如:

```
Host testDroplet dropletA
  Hostname 10.11.12.13 
```

现在我可以运行`ssh testDroplet`或`ssh dropletA`而不必记住 IP 地址。我通常缩进`Host`后面的行，以显示它们是为那个主机设置的。让我们给这个服务器添加一些定制的设置。

## 非标准端口

为了避免 SSH 强力攻击，我经常在除了`22`之外的端口上运行`sshd`(例如`9999`)。如果你每次都要记得在`ssh`命令中添加`-p9999`，这可能会很烦人。

我可以向同一个`~/.ssh/config`主机添加一个`Port`选项来避免这种情况:

```
Host testDroplet dropletA
  <snipped>
  Port 9999 
```

## 严格认证

密码是 70 年代的遗物，也是验证用户身份最糟糕的方式之一。因此，我总是为我的服务器设置基于 SSH 密钥的认证。我知道我从来不想对某些主机使用密码认证，那么为什么不干脆禁用它呢？

```
Host testDroplet dropletA
  <snipped>
  PasswordAuthentication no 
```

## 可视主机按键

您可能熟悉 SSH 向您展示的[“主机密钥指纹”](https://superuser.com/questions/421997/what-is-a-ssh-key-fingerprint-and-how-is-it-generated)。通常输出是这样的:

```
$> ssh dropletA
Host key fingerprint is SHA256:IqtYCzrIVi385SzZvoLuQlfSMQRqneh66RFAB/CZYTA 
```

我不知道你怎么想，但我不觉得编码的 SHA256 哈希很容易记住。值得庆幸的是，有一个更具视觉头脑的选项叫做`VisualHostKey` :

```
Host testDroplet dropletA
  <snipped>
  VisualHostKey yes 
```

现在指纹输出是可视化的！

```
$> ssh dropletA
Host key fingerprint is SHA256:IqtYCzrIVi385SzZvoLuQlfSMQRqneh66RFAB/CZYTA
+---[ECDSA 256]---+
|E++oo.           |
|.+=+.o           |
|.++o. o          |
|o. . o           |
| .o = . S        |
|.. B + o         |
|* B = *          |
|+X = = +         |
|+.Bo  +o.        |
+----[SHA256]-----+ 
```

## 本地端口转发

如果 droplet 在端口`1234`上运行服务器本地的测试服务，而该端口没有防火墙规则来允许入站流量，该怎么办？通常我会通过运行`ssh testDroplet -L8888:localhost:1234`来访问它，使[成为本地端口，将 droplet 上的`1234`的](https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding#Local_Port_Forwarding)转发到我本地机器上的`8888`。

为什么不通过给`~/.ssh/config`增加一个`LocalForward`来实现自动化呢！

```
Host testDroplet dropletA
  <snipped>
  LocalFoward 8888 localhost:1234 
```

*(注意，这里的语法与命令行略有不同)*

## 动态端口转发

有时候你不知道要通过服务器转发什么主机/端口。这叫做[动态端口转发](https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding#Dynamic_Port_Forwarding)。在命令行上使用`ssh -D 5555 dropletA`将在本地端口 5555 上创建一个 SOCKS5 代理，它将通过`dropletA`转发。当然还有一个`~/.ssh/config`也相当于`-D`:

```
Host testDroplet dropletA
   DynamicForward 5555 
```

## 跳转/堡垒代理

如果你在高安全性的环境中工作过，你可能会熟悉[“bastion”(或 jumpbox)设计模式](https://en.wikipedia.org/wiki/Jump_server)。想象一下，如果我们有三台服务器，`dropletB`、`dropletC`和`dropletD`，并且只允许 SSH 通过 jumpbox、`dropletA`访问。这意味着我必须运行两个`ssh`命令来访问`dropletC` : `ssh dropletA`，然后是`ssh dropletC`。烦人！

如果你正在使用一个现代的`ssh` (7.3 或更新版本)，你可以在一个命令(`ssh dropletC`)中完成所有这些，使用你的`~/.ssh/config`中的便利的`ProxyJump`特性，用于跳转主机后面的每个服务器:

```
Host dropletA
  <snipped>
Host dropletB
  ProxyJump dropletA
Host dropletC
  ProxyJump dropletA
Host dropletD
  ProxyJump dropletD 
```

如果你用的是旧的`ssh`(我相信 macOS 默认太旧了)你可以用`ProxyCommand` :
伪造`ProxyJump`配置

```
Host dropletB
  ProxyCommand ssh -W %h:%p dropletA 
```

因为`ProxyJump`简单多了，如果你好奇，我会让你阅读`man ssh config`来找出`ProxyCommand`论点。

## 持久的 SSH 连接

我住在偏远地区，有时不得不用非常慢的网速。有时我会开始在一个 droplet ( `ssh dropletA`)上工作，然后想要在不同的 shell 中运行另一个命令(再次运行`ssh dropletA`)。默认情况下，这是两个独立的 SSH 连接，如果你的互联网和我的一样慢，你肯定会注意到再次连接所花的时间。

令人欣慰的是，SSH 有一个很棒的特性，它允许您将第一个连接用作一个持久的“控制主机”，并让所有其他 SSH 连接在它的基础上进行多路复用。

我在我的`~/.ssh/config` :
中使用了以下内容

```
Host dropletA
  <snipped>
  ControlMaster auto
  ControlPersist 4800
  ControlPath ~/.ssh/control/%r.%h.%p.sock 
```

复杂的外观`ControlPath`表示连接主机的信息存储在磁盘上的什么位置。使用像`%l`和`%p`这样的模式有助于确保`ssh`作为不同用户访问同一服务器上的不同端口不会覆盖控制主机，因为那些*应该是*不同的连接。用上面的`ControlPath`当我运行`ssh daniel@dropletA`时，路径`~/.ssh/control/daniel.<dropletA server ip>.<dropletA ssh port>.sock`中的一个文件出现。第二次运行`ssh daniel@dropletA`非常快，因为它使用了现有的连接。

## 全局配置

最后需要注意的是，我经常想为*所有的*服务器设置其中的一些设置，而不仅仅是`dropletA`。您可以在一个特殊的`*`通配符主机:
下添加配置

```
Host *
  User daniel
  VisualHostKey yes
  Compression yes
  PasswordAuthentication no
  ControlMaster auto
  ControlPersist 4800
  ControlPath ~/.ssh/control/%r.%h.%p.sock 
```

现在，对于我 ssh 到的任何主机，我将默认使用用户名`daniel`，拥有可视主机密钥，压缩(慢速互联网记得吗？)，无密码 auth，和一个控制主。

## 结论

我希望这个 SSH 配置的简要介绍对您有所帮助！您可以在`ssh config`手册页:`man ssh config`中进一步了解这些选项中的每一个(以及更多)