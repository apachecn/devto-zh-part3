# 新鲜的 PHP 开发 noIDE 冒险

> 原文：<https://dev.to/cess11/fresh-php-development-noide-adventures-3nh4>

跨不同开发任务的即时交互响应和公共界面极大地提高了我的工作效率。开始了 PHP web 开发的新工作，日常工作的主机操作系统是 Windows 10，这被证明是一个挑战。我将在开发下一个同样的应用程序的全新版本的同时进行遗留代码维护，主要是在 PHP/MySQL/Linux 栈上。

碰巧的是，从 Windows 开发环境中瞄准 Linux 服务器环境并不简单。如果我没有弄错的话，在这种情况下，支付并运行一个大的 IDE 是很常见的，并且可能会订购一批手工制作的容器，并与不可调试的服务而不是旧的守护进程进行斗争。

我立即试用了 Windows 子系统 Linux，但结果有点令人失望。不知何故，我期望的不仅仅是缺乏 GUI 和 MobaXterm 极其不稳定的解决方案(只是不要去那里)，所以我想我已经离开微软仙境足够长的时间，以至于忘记了在这里必须忍受的艰苦斗争。

然而，我习惯了 SSH:进入远程服务器，在终端上工作也没问题，这基本上是使用 WSL 的经验。离独一无二的辉煌还差得很远，但已经足够好了。

在那里，我主要做新应用程序的工作，用 [tmux](https://github.com/tmux/tmux/wiki) 、 [vim+plugins](https://vimawesome.com/) 和 [ripgrep](https://github.com/BurntSushi/ripgrep) 作为主要工具，所有这些都很好地安装并运行在 WSL 下。以前我使用标记文件，但是 ripgrep 已经取消了，这一次我一点也不用担心了。真是太好了

```
:!rg 'function weirdMethod' -A 5 -B 5 
```

Enter fullscreen mode Exit fullscreen mode

在 vim 中(或者在新的 tmux 窗格中的命令行等效物),立即了解如何定义和注释 weirdMethod ()以及它在哪里。特别是当处理混合了两种浏览器布局语言、一种浏览器脚本语言和两种后端语言的大型文件时，有时会出现不稳定的情况，拥有一个一致的、快速的、支持正则表达式的界面会有很大帮助。

使用 WSL 共享文件非常方便，不需要使用 SSH/SFTP 进行传输，可以通过挂载点访问主机文件系统。除此之外，大多数情况下这是常规的无头 VPS 体验。

因为我需要一个 PHP、web 服务器等特定版本的设置，所以我研究了 Hyper-V 并运行了一个 Linux VM。我只是不是那种容器型的猴子。起初这很容易，只需快速重启以打开 BIOS 中的硬件管理程序支持，并且使用一个支持良好的 Linux 映像也很好。

VM:s 通常有点慢，但我很快发现 Hyper-V 只针对运行无头客户机的中高端服务器。UI 严重滞后，并且不断出现图形噪声，例如，在窗口管理器中的工作区之间切换意味着我还需要在 tmux 窗口之间快速切换，以便渲染和清理屏幕来查看我的代码。

所以我去寻找补救方法。起初，我只是在论坛上耐心地描述这不是一项虚拟桌面计算技术，但通过搜索，我设法找到了一些来自微软的 Linux 虚拟机工具和支持 XRDP 连接虚拟机的说明。

令人高兴的是，我突突地通过它，重新启动，突突了一些，再次启动，并得到了 XRDP VNC 登录欢迎表单。巨大的成功，至少我是这么认为的。

有一个很明显的问题，登录后我进入了相当可怕的 Ubuntu Unity 桌面环境，而不是我喜欢的和定制的 i3 设置。

惊慌和激动中，我摸索出一个终端，从屏幕上撕下包含“unity”的一行。xsession 并立即输入“i3wm”来代替。

大错特错。

重启并输入我的凭据后，XRDP 连接突然中断。一遍又一遍。在思考这个问题的时候，我花了一点时间耐心地重试，才意识到自己的错误。

i3 二进制文件没有命名为 i3wm，因此。xsession 脚本在登录时崩溃并烧毁，终止了 XRDP 连接。

经过一些思考和阅读 Powershell 中的`help Set-VM`,我想出了如何在 Windows 端关闭 XRDP，转而将我放入 LightDM 欢迎界面，并且还意识到我可以通过 SSH 进入机器来编辑。xsession 文件。所以我做了修复工作，重启了几次后，它也被粗鲁地删除了所有其他预装的 Unity 相关的调用。xsession 文件已被诅咒。

与基本的 Hyper-V VM-connection 相比，XRDP 真的很快，而且几乎完全没有不时出现在屏幕上的奇怪图形伪影。我不得不改变终端字体，以使它更好地呈现，更容易阅读，但除此之外，i3 只是工作，也很好。

如果你也有类似的情况但是还没有找到那个 Github repo，那就去那里试试吧。只要确保您的虚拟机启动到正在启动的 sshd(并且可以通过您的网络或虚拟交换机访问)，这样您就可以[PuTTY](https://www.putty.org/)-回滚任何错误，同时取消这些脚本为您安装的默认设置，以防您设法破坏 LightDM 配置并从该配置中被锁定。

在 Windows 端切换连接模式的命令是这样的，先打开 XRDP，再关闭:

```
PS C:\Windows\system32> Set-VM -VMName your-vm-name-here -EnhancedSessionTransportType HvSocket
PS C:\Windows\system32> Set-VM -VMName your-vm-name-here -EnhancedSessionTransportType VMBus 
```

Enter fullscreen mode Exit fullscreen mode

Powershell 可能需要管理员权限才能生效。它应该一直保持开放的机会，砖一个人的 Windows 系统，并获得升级到一些独特的东西。

除此之外，我们还运行一些源代码控制并保存远程 repos，但是很少有人能够自由决定是在 PHPStorm 巨像下受苦还是用 WSL 或 Hyper-V 中的 tmux-vim-ripgrep 将自己整洁优雅地集成到客户操作系统中。

哦，作为皇家大结局，这是你需要运行的 regedit 来切换大写和左 Ctrl:

```
REGEDIT4

          [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
          "Scancode Map"=hex:00,00,00,00,00,00,00,00,03,00,00,00,1d,00,3a,00,3a,00,1d,00,00,00,00,00 
```

Enter fullscreen mode Exit fullscreen mode

搞清楚这一点非常麻烦、无聊和讨厌(如果这个十六进制碰巧是错的，请发表评论，我会分享我存储的另一个，它肯定是正确的)。相比之下，在启动脚本中加入 xkbdmap 之类的东西，RegEdit 简直是一场噩梦。是的，我知道有些应用程序可能会破坏系统，但我不喜欢他们的穿着，所以他们不受欢迎。