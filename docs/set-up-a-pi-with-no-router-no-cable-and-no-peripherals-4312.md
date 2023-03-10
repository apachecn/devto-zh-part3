# 设置一个没有路由器、电缆和外围设备的 Pi

> 原文：<https://dev.to/nichartley/set-up-a-pi-with-no-router-no-cable-and-no-peripherals-4312>

如果你在大学，你试图建立一个树莓派，你可能会遇到一个错误。同上，如果你没有自己的路由器，你的 ISP(或房东)不会给你访问控制页面。这个问题是你需要得到你的 RPi 的 IP 地址，但是你没有任何方法得到它！

所以不用普通的路由器...为什么不自己做呢？当然，你可以出去买一个，但是你可能已经有一个能够产生热点的设备了。你的笔记本电脑或许可以做到，你的手机也可以。为什么不用它们呢？

> **注意**:如果你已经有了设置 RPis 的经验，你可以跳过本教程的其余部分。最后一段是最大的秘密。剩下的只是将正常的技术应用到这个特定的案例中。

# 要求

1.  RPi。我将使用 Raspberry Pi 3 Model B+，但这应该适用于任何支持 Raspbian 的机器。不过，这应该可以在任何 Raspbian 机器上运行。此外，你*将*仍然需要你的 RPi 的正常的强制性配件-电源线，SD 卡等。
2.  任何支持移动热点的设备。我用我的 Android 手机来做这个教程，但是不管怎样，说明都是相似的。
3.  SSH 客户端。我使用与 Linux 的 Windows 子系统打包在一起的 CLI，因为它很方便，但是有几十种，实际上任何一种都可以。

# 指令

## 准备好你的 Pi

你需要做的第一件事是准备好你的 Pi。有一个*吨*的教程闪存 Raspbian 到你的 SD 卡；你可以追随他们中的任何一个。

> **注意** : *请勿*安装 NOOBS！如果你有一个显示器和键盘连接到你的 Pi，NOOBS 是伟大的。如果有，就要用！然而，本教程是建立在你不安装的前提下的，所以确保你安装的是 *Raspbian* ，而不是 NOOBS。

一旦你在 SD 卡上安装了 Raspbian，你需要添加两个文件，都在 SD 卡的根目录下:

1.  名为`ssh`的空文件
2.  `wpa_supplicant.conf`(见下文)

在你最喜欢的编辑器中打开`wpa_supplicant.conf`，然后

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
     ssid="TODO"
     psk="TODO"
     key_mgmt=WPA-PSK
} 
```

保持文本编辑器打开。我们将通过替换这些行上的`TODO`来设置`ssid`和`psk`的值。

> **注意**:如果你不在美国，你应该更改`country=`行来匹配你国家的 [ISO 3155-1 alpha-2 代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements)。当错误的国家被供应时，我还没有看到事情破裂，但这并不意味着他们不能。

## 设置路由器

具体步骤因您用作路由器的产品而异。我将描述几个常见系统的过程，但是对于您正在使用的系统，网上肯定有教程，即使这里没有。

不过，总的来说:

1.  创建一个热点。
2.  将`ssid`设置为网络的 SSID。
3.  将`psk`设置为网络密码。

### 窗口

一台 Windows 10 机器将需要互联网连接或[一些拨弄](https://superuser.com/a/1229057/394502)来创建一个热点，但它给你同样的好处:打开你的 WiFi 菜单，在右下角或底部中间寻找一个标有“移动热点”的按钮。点击它。你现在有一个热点开始。

一旦它变成蓝色，右击它，点击“进入设置” <sup>1</sup> ，找到显示网络名称和密码的部分。回到`wpa_supplicant.conf`，将`ssid`设置为“网络名称”旁边的值，将`psk`设置为“网络密码”旁边的值。确保所有的字符都完全正确，包括大小写。

### 安卓

> **注意**:大部分手机都允许这样；然而，你的可能不是，或者可能有稍微不同的东西的名字。运用你最好的判断力。

打开设置。找到标有“连接”或“网络与互联网”的类别，然后轻按它。点击标签为“移动热点和网络共享”的选项，或类似的东西。轻按标记为“移动热点”或“Wi-Fi 热点”的选项旁边的开关。在你连接它之前，进入设置并尝试找到标有“WiFi 共享”的东西——这将防止你在连接 RPi 时意外使用移动数据。在我的手机上，一旦我打开 WiFi 共享，我实际上可以完全关闭 WiFi(或至少断开我的本地 WiFi)并仍然使用它，但这可能对你的手机不起作用。

和以前一样，您需要将`ssid`和`psk`分别设置为网络名称和密码。两者都应该在主屏幕上可见，但如果你找不到它们，请尝试点击右上角的三点菜单图标，然后“配置移动热点”。每个字段都将被很好地标记。

您还应该查看安全性下拉列表。有可能是 WPA2-PSK 法案，如果是这样的话，你不需要做任何事情。如果是别的原因，你需要学习更多关于`wpa_supplicant.conf`的知识来正确配置你的 RPi。

## 把两者放在一起

既然您已经运行了“路由器”并配置了 RPi 的 SD 卡，请将 SD 卡放入 RPi 并插上电源线。给它一些时间来启动-这需要一点时间。

最后，您应该会看到 RPi 出现在连接到您网络的设备列表上。在 Windows 10 热点上，你会在一个表格中看到设备的名称、IP 地址和 MAC 地址。在你的手机上，你可能只会看到一个名字——试着点击它，或者点击并按住它来显示关于它的额外信息。

您需要为下一步获取 RPi 的 IP 地址。应该贴清楚标签。一定要一模一样的复制；任何错别字都会让人讨厌。

## 连接到圆周率

这其实是最简单的一步。使用您将用来控制 RPi 的机器，连接到 RPi 所在的同一网络。以用户`pi`和密码`raspberry`的身份 SSH 到您刚刚得到的 IP 地址。恭喜你！您现在在 RPi 中，可以用它做任何事情，就像在普通的终端设置中一样。

顺便说一下，记得更改 RPi 的默认用户名和密码！即使你现在不打算用它来做任何其他人可以访问的事情，养成更改默认密码的习惯也比学会忘记密码要好。

顺便说一下，一个重要的警告是:用 RPi 或您的人机接口连接到不同的网络会中断 SSH 连接。如果您更改 RPi 所在的网络，*您的连接将在另一个连接建立之前断开*。

# 下一步

从这里，您可以完全控制您的 RPi。如果您的路由器连接到互联网，您甚至可以从互联网上安装东西。如果你够勇敢，你甚至可以设置 X11 转发，这样你就可以用图形方式*控制你的 RPi。你甚至可以走开，让 RPi 做一会儿自己的事情——当你回来时，它会自动重新连接到其`wpa_supplicant.conf`中的 WiFi，你可以像以前一样访问它。*

 *不过，如果你想让它连接到另一个网络，就比较麻烦了。当然，你可以将`wpa_supplicant.conf`改为指向另一个网络，重新启动，让它保持原样——假设你的所有细节都是正确的，它会很好地连接到那个网络的*。问题是当你想控制它的时候。*

如果您有 RPi 呼叫总部，您当然可以编写该系统，以便它可以从您的服务器发送类似“重新连接到我的热点”的命令。你也可以让一个脚本扫描本地可用的 WiFi，如果你的热点可用，重新连接到它。方法有不少，没有一种一定比其他的好。这取决于你想做什么，你有什么资源。

祝你好运！*