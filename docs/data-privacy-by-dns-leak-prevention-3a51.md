# 通过 DNS 防泄漏保护数据隐私

> 原文：<https://dev.to/alialp/data-privacy-by-dns-leak-prevention-3a51>

如今，数据隐私之桥是最热门的话题之一，虽然隐私是我们所有人得到的承诺，但我们意识到我们正在被监视，如果不是被官员监视，至少是被我们的宠物监视:)

## 一期

每当你打开手机或电脑或任何其他连接到互联网的设备时，你就将你的个人数据暴露在这个世界面前，这个世界包括好人和坏人，因此问题是如何尽量减少我们生活中的开门次数。

> #### [虚拟专用网络-维基百科](https://en.wikipedia.org/wiki/Virtual_private_network)
> 
> 虚拟专用网络(VPN)将专用网络扩展到公共网络，使用户能够通过共享或公共网络发送和接收数据，就像他们的计算设备直接连接到专用网络一样。因此，跨 VPN 运行的应用程序可以从专用网络的功能、安全性和管理中受益。

第一种解决方案是 VPN，简单来说就是这样

[![](img/38cfb2c82f5603e1223a75ddaf41782e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Du0BsSk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AoFdpeGQoN4Wsm5eoa2q-TA.png)

正如已经提到的，这个模式很简单，如果我们想看得更深一层，应该是这样的

[![](img/0f6e0e7de9e3e2073a7cbfef6ac53b47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29zXirAT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ADLEw067sHEUt9bAcxb8xeA.png)

你可以认为 DNS 是一个电话簿或联系应用程序，它会将网站名称转换为其编号，称为 IP 地址。

> #### [域名系统-维基百科](https://en.wikipedia.org/wiki/Domain_Name_System)
> 
> 互联网维护两个主要的名称空间，域名层次结构和互联网协议(IP)地址空间。域名系统维护域名层次结构，并提供域名和地址空间之间的转换服务。互联网名称服务器和通信协议实现域名系统。

可以看出，每当你想访问一个网站时，你的浏览器必须请求 DNS 服务器收集该网站的 IP 地址，然后它才能尝试连接到该网站。

> 问题是，如果你不小心设置 DNS 地址，你可能最终使用 VPN 连接，而你的互联网提供商知道你所有的活动，这种现象被称为 DNS 泄漏，换句话说，即使你没有被暴露，但如果你的 VPN 服务是优质的，你什么也不用付。

在幕后，大多数 VPN 提供商都在为您处理 DNS 覆盖，只需将您的 DNS IP 地址设置为他们的即可。

> 无论如何，即使 VPN 提供商声称他们正在处理 DNS 覆盖与否，问题是你怎么知道呢？

## 解

### Linux & Mac

先决条件:

```
sudo apt-get install curl 
```

Enter fullscreen mode Exit fullscreen mode

```
#!/bin/sh 
dns1=$(nslookup -q=A whoami.akamai.net | grep "Address" |grep -v "#" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")                         
dns2=$(nslookup -q=A resolver.dnscrypt.org | grep "Address" |grep -v "#" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")                    
dns3=$(nslookup -q=A whoami.ultradns.net | grep "Address" |grep -v "#" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")                     
curl http://ipinfo.io/$dns1/country                                                                                                
curl http://ipinfo.io/$dns2/country                                                                                               
curl http://ipinfo.io/$dns3/country 
```

Enter fullscreen mode Exit fullscreen mode

## 窗口

对于 windows 用户，很抱歉，下面的解决方案也适用于您，但是如果您是系统管理员，并且您需要一个针对 windows 的本地解决方案，请在这里的评论部分留下您的请求

先决条件:

> #### [在 Windows 10 上安装 Windows Subsystem for Linux(WSL)](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
> 
> Windows 10 上用于 Linux 的 Windows 子系统的安装说明。

然后在 Ubuntu 模拟器

```
sudo apt-get install curl 
```

Enter fullscreen mode Exit fullscreen mode

```
#!/bin/sh 
dns1=$(nslookup -q=A whoami.akamai.net | grep "Address" |grep -v "#" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")              
dns2=$(nslookup -q=A resolver.dnscrypt.org | grep "Address" |grep -v "#" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")         
dns3=$(nslookup -q=A whoami.ultradns.net | grep "Address" |grep -v "#" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")          
curl http://ipinfo.io/$dns1/country                                                                                     
curl http://ipinfo.io/$dns2/country                                                                                    
curl http://ipinfo.io/$dns3/country 
```

Enter fullscreen mode Exit fullscreen mode

在下面的结果中，您可以看到一个 DNS 泄漏的实例

[![](img/3cc212f62f21dd23577e16a71aa40e63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cN9yq4vN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ao2ANKMIVKzUBt8fkyFhs6w.png)

我的原籍是土耳其，所以如果名单上有土耳其，你可以肯定你的原籍国正在处理你的请求，所以你不是完全匿名的。

下面你可以看到一个未泄漏的 DNS 配置

[![](img/5b44b05812325b351a50595895f767c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9oVLoB-h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AO6rAFmX7v0aW86WD8hE4gg.png)

这意味着您的 DNS 请求正在按照您的配置进行解析，因此您可以感觉离互联网上的匿名又近了一步，这意味着还有 99 步要走:)