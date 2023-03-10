# 在 Manjaro Budgie 上从 AUR 安装 yay 时出错

> 原文：<https://dev.to/mattdark/error-installing-from-aur-with-yay-on-manjaro-budgie-1gl8>

几个月前，我在我的联想 G480 上安装了一个全新的曼哈罗虎皮鹦鹉。当我试图用 **[yay](https://github.com/Jguer/yay)** 安装来自**[【AUR】](https://aur.archlinux.org/)**的软件包时，我得到了以下错误:

```
error cloning package 
```

Enter fullscreen mode Exit fullscreen mode

我尝试安装的每个软件包都出现了同样的错误。我用 **pacman-mirrors** 更新了镜像列表，并重新安装了 yay，但错误仍然存在。

谷歌了一下，没有找到任何解决方案，我认为这个问题与 Git 有关，因为一些包存储在 Git 仓库中。

原来 Git 是 yay 的一个依赖项，但它不是默认安装的。来自 AUR 的安装也是通过 fakeroot 环境完成的。所以解决方案是在使用 yay 之前安装两个包。

```
$ sudo pacman -S git fakeroot 
```

Enter fullscreen mode Exit fullscreen mode

如果你在试图从 AUR 安装软件包时遇到这个错误，我希望它能帮助你。