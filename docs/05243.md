# Linux Mint 上的 Android 设备调试:“错误:设备权限不足:udev 需要 plugdev 组成员身份”

> 原文：<https://dev.to/jarroo/android-device-debugging-on-linux-mint-error-insufficient-permissions-for-device-udev-requires-plugdev-group-membership-369>

当我为终于找到一个与我的戴尔 XPS 13 (2019 版，型号 9380)兼容的 Linux 发行版而高兴时，我插上了我的 Moto 测试设备，打算继续开发一个 Android 应用程序。按下运行按钮立即让 Android Studio (3.4)施展了编译魔法，但很快就停止了。一条愤怒的错误信息等待着我:

```
error: insufficient permissions for device: udev requires plugdev group membership 
```

Android Studio 还留了一张纸条，让我指向他们关于主题为 T1 的开发者页面。正如我所怀疑的那样，似乎我还需要做一些配置工作来让设备调试在 Linux 上工作。然而，按照谷歌的指示建立亚洲开发银行并没有解决我的问题。但是，关于将自己加入 udev 组的部分很重要，因为它与下一段中描述的实际解决方案相关联。

```
sudo usermod -aG plugdev $LOGNAME 
```

一次不合时宜的遛狗(这么说吧，我没想到会遇上倾盆大雨)，几个有点沮丧的谷歌搜索之后，我偶然发现了 2013 年的一篇博文(！)关于[“为 USB 调试 Android 设备添加 udev 规则”](http://www.janosgyerik.com/adding-udev-rules-for-usb-debugging-android-devices/)的主题，作者 Janos Gyerik。

我不会假装知道为什么需要这个额外的配置，但它描述了查找设备的标识符并将其添加到前面提到的 plugdev 访问组，因此 Android Studio 可以正确地访问 USB 设备。

这就对了，多亏了 2013 年的一些好建议，我的 Android 设备可以使用 USB 调试连接了！

*原贴于[jarroo.com](https://jarroo.com/android-device-debugging-on-linux-mint-error-insufficient-permissions-for-device-udev-requires-plugdev-group-membership/)T3】*