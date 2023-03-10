# Mojave + VirtualBox

> 原文：<https://dev.to/asyazwan/mojave-virtualbox-4ki6>

我终于决定把我的苹果电脑从 High Sierra 升级到 Mojave。然而，对于我的工作机器，VirtualBox 只是停止工作并在启动时抛出`NS_ERROR_FAILURE`。类似于[这个](https://stackoverflow.com/questions/52689672/virtualbox-ns-error-failure-0x80004005-macos)。尝试重新安装不会起作用，因为安装程序会在最后一步失败。

我已经尝试了*T2 建议的一切，从禁用网守到卸载/重新安装。几乎所有的解决方案都建议点击安全&隐私标签上的`Allow`，但它从未出现过。*

最终对我起作用的是:[Mac OS——VirtualBox 5.2 无法在 Mac OS 10.13 上安装——询问不同的人](https://apple.stackexchange.com/a/360123/332949)归结为:

进入恢复模式(在加载屏幕期间使用 Cmd + R 重启)并打开终端:

```
spctl kext-consent disable
spctl kext-consent add VB5E2TV963
spctl kext-consent enable 
reboot 
```

这基本上是最终使安装成功的`Allow`部分。`VB5E2TV963`是我们允许的 Oracle id。

如果你也是类似的情况，希望这能有所帮助。