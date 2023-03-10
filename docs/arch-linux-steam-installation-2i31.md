# Arch Linux: Steam 安装

> 原文：<https://dev.to/setevoy/arch-linux-steam-installation-2i31>

[![](img/9784b61222c0d3b6a1a5320be0f28709.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNb1hH5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2018/04/archlinux_logo.png) 买了一台新电脑，想在这里玩游戏。

坦克世界已经可以运行了——查看[坦克世界:在 Arch Linux](https://dev.to/setevoy/world-of-tanks-installing-and-running-on-arch-linux-ofo) 上安装和运行，现在我想安装 Steam。

几年前它在 Ubuntu 上运行没有问题，我可以在那里玩文明 V——所以现在让我们试着在 Arch 上运行它。

安装文件[此处> > >](https://wiki.archlinux.org/index.php/Steam#Installation) 。

有几个问题——但它在这里也适用。

安装 Steam 客户端:

```
[setevoy@setevoy-arch-pc ~]$ sudo pacman -S steam 
```

### 版本号:未绑定变量

运行蒸汽:

```
[setevoy@setevoy-arch-pc ~]$ steam
Setting up Steam content in /home/setevoy/.local/share/Steam
/home/setevoy/.local/share/Steam/steam.sh: line 106: VERSION_ID: unbound variable
/home/setevoy/.local/share/Steam/steam.sh: line 106: VERSION_ID: unbound variable
Running Steam on arch  64-bit
/home/setevoy/.local/share/Steam/steam.sh: line 106: VERSION_ID: unbound variable
STEAM_RUNTIME is enabled automatically
Pins potentially out-of-date, rebuilding...
/home/setevoy/.local/share/Steam/steam.sh: line 828: /home/setevoy/.local/share/Steam/ubuntu12_32/steam-runtime/run.sh: No such file or directory
Installing breakpad exception handler for appid(steam)/version(0)
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: swrast 
```

好吧…

*“Steam/Ubuntu 12 _ 32/Steam-runtime/run . sh:没有这样的文件或目录”*——为什么？

第一次讨论谷歌了[这里> > >](https://bbs.archlinux.org/viewtopic.php?id=240965) 。

试着启用`steam-native`(虽然我安装了常用的`steam-runtime`——查看[这里的> > >](https://wiki.archlinux.org/index.php/Steam/Troubleshooting#Steam_runtime) 了解详情):

```
[setevoy@setevoy-arch-pc ~]$ export STEAM_RUNTIME=1 
```

没用。

另有讨论发现[此处> > >](https://github.com/ValveSoftware/steam-for-linux/issues/3851) 。

让我们试试——编辑`/home/setevoy/.local/share/Steam/steam.sh`:

```
...
function detect_release()
{
    if [ -f /etc/lsb-release ]; then
        (. /etc/lsb-release; echo $DISTRIB_RELEASE)
#   elif [ -f /etc/os-release ]; then
    elif $(grep 'VERSION_ID' /etc/os-release > /dev/null 2>&1); then                                                                                                                                                                   
        (. /etc/os-release; echo $VERSION_ID)
    elif [ -f /etc/debian_version ]; then
        cat /etc/debian_version
    else
        # Generic fallback
        uname -r
    fi
}
... 
```

再次运行:

```
[setevoy@setevoy-arch-pc ~]$ steam
Running Steam on arch 4.20.11-arch2-1-ARCH 64-bit
STEAM_RUNTIME is enabled by the user
Pins up-to-date!
/home/setevoy/.local/share/Steam/steam.sh: line 829: /home/setevoy/.local/share/Steam/ubuntu12_32/steam-runtime/run.sh: No such file or directory
Installing breakpad exception handler for appid(steam)/version(0)
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: swrast 
```

好的…现在好一点了。

### libGL 错误:无法加载驱动程序:swrast

接下来的谷歌搜索把我带到了[这个> > >](https://unix.stackexchange.com/questions/279753/steam-doesnt-work-on-arch-linux-x64) 的讨论——让我们试试吧:

```
[setevoy@setevoy-arch-pc ~]$ sudo pacman -S multilib/lib32-nvidia-utils 
```

啊哈！

[![](img/cb342a46c13a6a38cbfa4f2606d49ea9.png "Arch Linux: установка Steam")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190224_192500.png)

有趣的是——`VERSION_ID`错误仍然存在:

[![](img/600c980fbf1b4cd9f135d73ce353745c.png "Arch Linux: установка Steam")](https://rtfm.co.ua/wp-content/uploads/2019/02/Screenshot_20190225_211724.png)

但是现在蒸汽起作用了。

还不能管理文明，但会想出办法的。

### 类似的帖子

*   <small>03/04/2019</small> [Arch Linux:安装、故障排除和播放 EVE Online](https://dev.to/setevoy/arch-linux-installation-troubleshooting-and-playing-eve-online-871) <small>(0)</small>
*   <small>02/24/2019</small> [Arch Linux:用 EFI 和 Windows 双引导安装](https://dev.to/setevoy/arch-linux-installing-with-efi-and-windows-dual-boot-1eoh) <small>(0)</small>
*   <small>05/06/2017</small>[Arch Linux:запускаем暗黑破坏神 2](https://rtfm.co.ua/arch-linux-zapuskaem-diablo-2/) <small>(3)</small>
*   T003/29/2017 t1t2arch:SKYPE-T3T 4 音频和麦克风