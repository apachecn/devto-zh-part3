# 登录后 XFCE 空白/黑屏并显示光标(解决方案)

> 原文：<https://dev.to/an3223/xfce-blankblack-screen-with-cursor-after-login-solution-5fce>

最近我遇到了一个问题，从我的显示管理器(LightDM，如果有关系的话)启动 XFCE 后，我会得到一个空白的屏幕。没有面板，没有图标，没有桌面，只有我的光标。这开始于卸载 GNOME 之后(并且在安装 i3 之后再次出现)。作为一个解决方法，我可以在 tty 之间来回切换(比如 Ctrl + Alt + F6，然后 Ctrl + Alt + F7)，这样就可以启动我的桌面了。但是今天我找到了一个真正的解决办法。

**我的解决方案是删除** `~/.config/xfce4/xfconf/xfce-perchannel-xml/displays.xml`，就像在[Arch wiki](https://wiki.archlinux.org/index.php/Xfce#Black_screens_at_boot_with_NVIDIA_and_multiple_monitors)和[Nvidia 论坛](https://devtalk.nvidia.com/default/topic/1029381/linux/black-screen-at-desktop-login-gtx-750-ti-390-25-/3?fbclid=IwAR23GSnvpGS3IKRfYFqdS3ma1Je9NLfkbVemqwHMVT02P_nlZ3BSswnkMiw)的这个帖子上建议的那样(这似乎也是多显示器设置的一个问题)。

在网上搜索这个问题收效甚微，导致我第一次只是暂时解决了这个问题，按照 Arch 论坛的建议，删除了我所有的 XFCE 设置[(**不要这样做**，这是一个*的痛苦*，如果你像我一样在你的设置中投入了这么多时间的话)。我只是在阅读前面提到的 Arch wiki 上的 XFCE 页面时偶然发现了这个解决方案。因此，希望我在这篇文章中留下了足够多的关键词，以帮助下一个不幸的桌面环境爱好者或多显示器超级用户搜索这个问题，如果你是那个人，感谢你的阅读。](https://bbs.archlinux.org/viewtopic.php?id=143529)