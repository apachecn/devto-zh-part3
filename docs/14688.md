# 和 Manjaro 乱搞

> 原文：<https://dev.to/jks7743/messing-with-manjaro-321m>

# 我的背景

虽然我在 Ubuntu 的 CLI 上有相当多的经验，无论是在我的学校机器上还是在我的 WSL(Linux 的 Windows 子系统)上，我都没有花太多时间来选择我自己的 Linux 风格并将其用作我的日常驱动程序，但是在我的一个朋友推荐了 Manjaro 之后，我决定尝试一下。

# 什么是曼哈罗？

Manjaro 是一个基于 Arch 的 Linux 发行版，但是有一些关键的不同。Manjaro 非常注重可用性和“开箱即用”的思想，而 Arch 则注重简单性，依靠用户让事情为自己服务。Arch 的发布速度很快，这使它成为前沿，但是非常不稳定。而 Manjaro 在保持滚动发布时间表的同时保持了非常稳定的 OS。

# 为什么是 Manjaro？

因为 Manjaro 关注的是稳定性，所以与 Arch 相比，它的发布进度要慢得多。这是一种权衡，但用户将能够拥有一个更加可靠的操作系统。Manjaro 还使用 Arch 的强大包管理器 pacman。在这样做的时候，Manjaro 与 Arch 用户库是兼容的。如果你愿意多等一会儿，你会有一个非常好的操作系统。但如果你更想了解 Arch 的最新版本，你可以转到他们的“最新”发布时间表。Manjaro 对许多进入 Linux 的人来说也是一个非常友好的用户界面，因为它有 pamac(如果你用的是 KDE 版本，还有 Octopi)，Manjaro 的 GUI 可以用来下载新的软件包。

# 下载曼哈罗

下载 Manjaro 时你需要做的第一件事是挑选一个桌面，从 xfce 到 cinnamon 和 mate 有大量不同的选项，如果你想了解更多，我建议你自己做研究，但我建议你选择 [xcfe](https://manjaro.org/download/xfce/) 或 [KDE](https://manjaro.org/download/kde/) 我选择 KDE 版本是因为它的灵活性，而且硬件限制对我的笔记本电脑来说不是问题。[如果你使用正确的工具，下载 Manjaro](https://manjaro.org/support/firststeps/#install-manjaro) 非常容易。你可以在虚拟机上测试它，或者从 usb 实时启动，如果你喜欢，你可以从实时启动安装它。至于让 usb 可引导，我建议使用[图像写入器](https://launchpad.net/win32-image-writer/)而不是 Rufus，因为我在 grub 版本上有问题。如果你需要更多信息，我从 Manjaro 网站上链接了一个指南。

# 征服曼哈罗

我的一个朋友告诉我，你需要征服你的操作系统，然后它才能成为你使用的强大工具。我非常喜欢这个术语，而且我肯定要经历几个循环才能让 Manjaro 按照我想要的方式工作。它还不完美，但我现在处于一个很好的可用位置。这里有几件我不得不经历的事情，当你启动 Manajaro 时，你可能也必须处理。

## 安装软件包

我做的第一件事就是安装所有的软件包，要安装 pacman，你需要在 Konsole 中输入`pacman -S`。如果你对终端还不太适应，或者只是想浏览一下软件包，我建议你安装带有`pacman -S pamac`的 pamac，然后从你的开始菜单打开它。

## 嘟嘟嘟的响声

我在 pamac 中寻找不同的包，我不小心按下了搜索栏上的 backspace 和额外时间*哔声*。我立即想到，这可能是恼人的，我的电脑静音，但哔哔声仍然响亮，当然清楚。所以我查找如何修复它，无意中发现了[这一页](https://wiki.archlinux.org/index.php/PC_speaker)。有点不清楚，我就这么做了。

我打开我的 Konsole，输入了这一行`rmmod pcspkr`，网站声称这将禁用 BIOS 扬声器。我回到 pamac 进行测试，声音消失了。我解决了这个问题，但是每次我启动电脑的时候我都必须这么做。所以我需要一种方法让它在启动时被禁用。我发现最简单的方法就是黑名单，你可以在这里读到关于[的内容。
为此:](https://wiki.archlinux.org/index.php/Kernel_module#Blacklisting)

1.  使用`cd /etc/modprobe.d/`进入 modprobe.d 目录
2.  使用您最喜欢的文本编辑器(和 sudo 命令)，添加一个包含行`blacklist pcspkr`的. conf 文件。

问题解决了！(这不会禁用正常音频)

## 禁用我的触摸屏

另一件不太清楚的事情是禁用我坏掉的触摸屏，它会在不做任何事情的情况下随机按下。我知道这个硬件问题已经持续了一段时间。~~经过大量研究后，我下载了一个名为 xorg-xinput 的包，并能够解决这个问题。以下是一些步骤。~~
~~1。使用`pacman -S xorg-xinput`~~
~~2 打开 Konsole 并下载 xinput。运行`xinput`并查找您想要禁用的设备名称~~
~~3。运行`xinput disable 'ELAN touchscreen'`,但是使用你的设备的名字代替~~

~~就这样！~~

编辑:
当你重新启动电脑时，这将不起作用，我用来解决这个问题的方法是我从[这个线程](https://unix.stackexchange.com/questions/127443/how-do-i-disable-the-touch-screen-on-my-laptop)中得到的。我是这样做的:

1.  用`cd /usr/share/X11/xorg.conf.d`移动到 xorg conf 目录
2.  使用`ls`查看您的。conf 文件只是为了确保
3.  用您最喜欢的 vim 文本编辑器打开 10-evdev.conf 文件:`sudo vim 10-evdev.conf`。
4.  找到“evdev 触摸屏总汇”部分，并将`Option "Ignore" "on"`添加到该部分。

看起来应该是这样的
[![10-evdev.conf](img/b1e2893834e8f6f69a04bf5b0748a92a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rbRq3ZPh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/df5ncnk9qlqndqag1bs3.png) 
在我保存文件并重启电脑后，触摸屏被禁用了！
希望这有所帮助！

我喜欢摆弄 Manjaro，如果你想尝试 Linux 或者从另一个发行版切换，Manjaro 将是一个很好的起点！