# 如何在 termux 上收听 youtube 视频

> 原文：<https://dev.to/voyeg3r/how-to-listen-youtube-videos-on-termux--39d9>

您需要在 android 上安装 termux

在您的 android 上运行这些命令

```
pkg install mpv termux-api neovim python pipx
pipx install yturl 
```

Enter fullscreen mode Exit fullscreen mode

注意:“ [pipx](https://pypi.org/project/pipx/) —在隔离环境中安装和运行 Python 应用程序”

将此代码放在您的~/上。bashrc(安卓)。我在我的 android 上使用了一个别名 neovim，但现在我知道如何通过这里的文档使它变得更容易。

```
cat <<-'EOF' >> ~/.bashrc
yt(){
    mpv --no-video "$(yturl $(termux-clipboard-get))"
}
EOF 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你观看 youtube 视频时，你可以选择分享并获得链接，然后打开你的 termux 并输入

```
yt 
```

Enter fullscreen mode Exit fullscreen mode

## 使用纯 mpv

```
mpv --no-video url 
```

Enter fullscreen mode Exit fullscreen mode

## 对 neovim 的完全 python 支持

```
pipx install neovim 
```

Enter fullscreen mode Exit fullscreen mode

参考文献:[https://unix.stackexchange.com/a/229815/3157](https://unix.stackexchange.com/a/229815/3157)