# 使用 vipe 将 vim 添加到管道中

> 原文：<https://dev.to/sdball/add-vim-to-a-pipeline-with-vipe-24o9>

您是否曾经想要在管道中间停下来编辑文本？有了`vipe`就可以！

以`brew search`的输出为例。

```
$ brew search proxy
==> Formulae
aws-es-proxy metaproxy proxytunnel
dnscrypt-proxy mitmproxy sproxy
haproxy oauth2_proxy tinyproxy
ios-webkit-debug-proxy ocproxy twemproxy
libproxy proxychains-ng

==> Casks
proxyman 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想调用`brew info`来获得这些公式的更多细节，我们该怎么做呢？

即使我们通过管道将输出传递给某个东西(例如 cat ),我们仍然有公式和桶头:

```
$ brew search proxy | cat
==> Formulae
aws-es-proxy
dnscrypt-proxy
haproxy
ios-webkit-debug-proxy
libproxy
metaproxy
mitmproxy
oauth2_proxy
ocproxy
proxychains-ng
proxytunnel
sproxy
tinyproxy
twemproxy

==> Casks
proxyman 
```

Enter fullscreen mode Exit fullscreen mode

我们*可以*写一些中间步骤来删除我们不想要的行。但是让我们的管道停止，让我们编辑流，然后继续，这不是很好吗？

```
$ brew search proxy | magic | while read formula; do brew info $formula; done 
```

Enter fullscreen mode Exit fullscreen mode

原来`vipe`可以那么神奇！它会停下来，用前面管道的输出给你一个 vim 会话。当您编写/退出 vim 会话时，输出将被发送到管道中的后续命令。厉害！

## 安装 vipe

你可以在 OSX 买到作为`moreutils`酿造配方一部分的`vipe`。

```
$ brew install moreutils 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

然后将它放在管道中间您想要快速编辑文本的任何地方。这也是查看管道中间输出的好方法。

```
$ brew search proxy | vipe | while read formula; do
  brew info "$formula";
done 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行该命令时,`brew search proxy`输出直接进入 vim 缓冲区。

```
 1 ==> Formulae
 2 aws-es-proxy
 3 dnscrypt-proxy
 4 haproxy
 5 ios-webkit-debug-proxy
 6 libproxy
 7 metaproxy
 8 mitmproxy
 9 oauth2_proxy
10 ocproxy
11 proxychains-ng
12 proxytunnel
13 sproxy
14 tinyproxy
15 twemproxy
16
17 ==> Casks
18 proxyman 
```

Enter fullscreen mode Exit fullscreen mode

我们可以操纵那个缓冲区成为我们想要的。

```
1 mitmproxy 
```

Enter fullscreen mode Exit fullscreen mode

我们精简到一个有趣的公式怎么样？当我们保存并退出时，管道继续进入`while read`循环，将所有输入发送到`brew info`。

```
"/private/var/folders/n6/gxxgmjhs1599x195xzdbnp_40000gn/T/mL0nDL6bCD" 1L, 10C written

mitmproxy: stable 4.0.4 (bottled), HEAD
Intercept, modify, replay, save HTTP/S traffic
https://mitmproxy.org
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/mitmproxy.rb
==> Dependencies
Required: openssl@1.1 ✔, protobuf ✘, python ✘
==> Options
--HEAD
    Install HEAD version
==> Analytics
install: 6,017 (30 days), 15,281 (90 days), 46,094 (365 days)
install_on_request: 5,777 (30 days), 14,445 (90 days), 44,138 (365 days)
build_error: 0 (30 days) 
```

Enter fullscreen mode Exit fullscreen mode

不错！