# 设置简单的 DNS 服务器

> 原文：<https://dev.to/nuculabs_dev/setting-up-a-simple-dns-server-365l>

在我配置完 FTP 服务器之后，我希望能够“命名”我的服务器，所以我安装了一个轻量级的 DNS 服务器。

> “dnsmasq 是一个轻量级的 DNS，TFTP，PXE，路由器广告和 DHCP 服务器。它旨在为局域网提供耦合的 DNS 和 DHCP 服务。

我选择了 bind 9 dns 服务器，因为它非常容易设置，我几乎第一次就做对了。

要安装和配置 dnsmasq，通常需要做以下工作:

```
# On Ubuntu
sudo apt-get install dnsmasq
sudo cp /etc/dnsmasq.conf /etc/dnsmasq.conf.orig 
```

Enter fullscreen mode Exit fullscreen mode

用这个配置覆盖**/etc/dnsmasq . conf**:

```
#/etc/dnsmasq.conf domain-needed
bogus-priv

expand-hosts

# The address 192.168.0.176 is the static IP of this server 
# You can find this ip by running ifconfig and look for the 
# IP of the interface which is connected to the router. listen-address=127.0.0.1
listen-address=192.168.0.176
bind-interfaces

# Use open source DNS servers server=8.8.8.8
server=8.8.4.4
server=208.67.220.220

# Create custom 'domains'.
# Custom 'domains' can also be added in /etc/hosts address=/busyserver.net/192.168.0.176 
```

Enter fullscreen mode Exit fullscreen mode

基本上就是这样。

您可以通过运行:`sudo systemctl status dnsmasq`来检查 dnsmasq 服务器的状态。

要实际使用服务器并让您的计算机识别您的自定义域，您需要告诉您的计算机使用安装了 dnsmasq 的服务器的 IP，在我的例子中是 192.168.0.176。

感谢您的阅读！

[推特](https://twitter.com/denisnutiu) | [多福拉](https://m.do.co/c/22f012126c25)