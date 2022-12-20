# 在 Raspberry Pi 上安装 Visual Studio 代码

> 原文：<https://dev.to/0xkoji/install-visual-studio-code-on-raspberry-pi-451k>

我用 Visual Studio 代码很久了，所以有时候用 vim 的时候真的很想用在 Pi 上(当然 vim 是超级好看的 lol)

## 安装`Visual Studio Code`

目前最新版本有一个问题，所以需要安装稳定的版本。

更新于 2019 年 4 月 20 日

```
$ wget -qO - https://packagecloud.io/headmelted/codebuilds/gpgkey | sudo apt-key add -
$ sudo apt-get install code-oss=1.29.0-1539702286 
```

Enter fullscreen mode Exit fullscreen mode

ref
[https://github . com/head melted/code builds/issues/67 # issue comment-466255567](https://github.com/headmelted/codebuilds/issues/67#issuecomment-466255567)