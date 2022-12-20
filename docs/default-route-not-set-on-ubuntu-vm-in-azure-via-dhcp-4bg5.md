# Azure 中的 Ubuntu 虚拟机上未设置默认路由(通过 DHCP)

> 原文：<https://dev.to/amoghe/default-route-not-set-on-ubuntu-vm-in-azure-via-dhcp-4bg5>

## 在不同的虚拟机管理程序上运行相同的 Ubuntu 映像时的意外

我最近在各种虚拟机管理程序(和云 IaaS)上启动服务器时，偶然发现了几个相当棘手的问题。现在，不可否认的是，我做了一些“不正常”的事情——也就是说——试图在其他平台上启动一个虚拟机，该虚拟机是在 VMWare ESX 上运行的虚拟机的副本(使用磁盘的`dd`副本)。让我吃惊的是，虚拟机会:

1.  在 VMWare 上正常启动(显然)，通过 DHCP 配置网络
2.  在 AWS 上正常启动，配置了网络，实例是可 SSH 的
3.  在 Azure 上正常启动，但不会获得任何默认路由，所以*没有 SSH* ！

经过一番打探，我学到了一些东西

## 1。`dhclient`有多种方式设置默认路线

这些都是基于 DHCP 服务器的响应。现在，

1.  一些 DHCP 服务器返回`Classless Static Routes`选项
2.  一些 DHCP 服务器返回`Router`选项

经过大量的调试(这是极其痛苦的，因为对这些问题唯一真正可用的“调试工具”是使用`/etc/dhcp/dhclient-exit-hooks`中的`debug`脚本)之后，我发现一个环境工作得很好，因为它使用了`Router`选项，而在 Azure 上，DHCP 服务器发回了`Classless Static Routes`选项。

而 dhclient 更喜欢`Classless Static Routes`选项(见这里的

在 VMWare 和 AWS 上，DHCP 服务器实际上使用了`Router`选项，这就是它在这两种环境中都能正常工作的原因。

好了，这解决了一个谜——但是考虑到 dhclient 应该知道如何处理这两种选择——为什么它不能在 Azure 上为我的 VM 工作呢？

## 2。`dhclient-exit-hook`脚本来源于

经过进一步的调查，发现我的一个 dhclient-exit-hook 脚本正在做“错误的事情”,并且由于一个放错位置的`exit 0`而提前终止。

没有任何地方(无论是手册页还是 interwebz)明确提到 dhclient-exit-hook 脚本实际上是来源于而不是执行的。

结果是我的一个定制脚本“退出”了执行，从而导致一些剩余的脚本无法运行。最值得注意的是，Ubuntu 上有一个处理`Classless Static Routes`选项(称为`rfc3442-classless-routes`)的默认脚本，它实际上正确地处理了这个问题。然而，由于脚本是按照字典顺序编写的，这个脚本将不会运行，因为我前面提到的问题。

最后要注意的是 StackExchange 上的这篇[帖子](https://serverfault.com/questions/780604/dhclient-doesnt-set-default-gateway/780739#780739)极大地帮助了我找到问题的根源。