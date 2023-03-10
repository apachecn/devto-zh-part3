# 在戴尔 XPS 笔记本电脑上安装 Ubuntu 18.04

> 原文：<https://dev.to/hammacktony/installing-ubuntu-1804-on-dell-xps-laptops-5b0j>

大约一个月前，我在密苏里州的圣路易斯开始了一份新工作。到达后，我得到了一台 Dell XPS 500 笔记本电脑作为我的开发机器。随即，我格式化了驱动器，安装了 Ubuntu 18.04。好吧，至少我试过了。在戴尔 XPS 笔记本电脑上安装 Ubuntu 18.04(或任何最新版本)时，您必须先做一些特殊的事情。这是由于 Nvidia 显卡不能很好地与 Ubuntu 兼容。

经过多次反复试验，我终于找到了如何让 Ubuntu 与戴尔 XPS 笔记本电脑兼容的方法。

请遵循以下步骤:

1.  在 Bios 中禁用快速引导和安全引导(或安全加载程序)。

2.  插入带有 Linux 发行版的可启动 USB(我的是 Ubuntu 18.04)

3.  当你看到加载程序“安装 Ubuntu”等...按“e”编辑一行:
    替换`quiet splash`到`nomodeset`按 F10 开机。

安装完成后，您必须重启。

1.  这一次你需要加载 GRUB 菜单，(在 bios 启动后和操作系统图标出现前按住 Shift 键，如果你看到图标，就太晚了。再次按“e”编辑一行:
    在以`linux`开头的那一行，在那一行的末尾加上`nouveau.modeset=0`。(保持`nomodeset`从前的样子)

2.  启动 linux 后，安装 nvidia 驱动程序。

运行`ubuntu-drivers devices`。我们可以看到当前的显卡和推荐的驱动程序。如果你同意这个建议，运行`sudo ubuntu-drivers autointsall`。
如果您不同意该建议，您可以通过`sudo apt-get install nvidia-384`安装单独的驱动程序。

1.  重启。然后就完事了。

* * *

最初发布于[tonyhammack.com](http://www.tonyhammack.com/blog/post/installing-ubuntu-18-04-on-dell-xps-laptops)