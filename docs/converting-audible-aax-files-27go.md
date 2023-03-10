# 转换音频*。aax 文件

> 原文：<https://dev.to/j6s/converting-audible-aax-files-27go>

Audible 有很多可供选择的有声读物，可以下载并随时收听。然而，有一个很大的问题:所有的有声读物都有 DRM，只能使用官方的 audible 应用程序来听。

想用汽车音响听有声读物吗？祝你好运。你不想保存有声读物，因为你想在 10 年后听它？没有。不想使用没有 google play 服务的 android 设备，或者(但愿不会)期待 linux 智能手机在 2019 年登陆？亚马逊在嘲笑你。

幸运的是，事情并不像我最后一段让你相信的那样可怕。如果你在谷歌上搜索了一下，你肯定会看到[这篇关于 code-bude.net](https://en.code-bude.net/2017/02/12/how-to-convert-audible-aax-files-to-mp3-in-linux/)的博客文章，它概述了如何将 aax 文件转换成 mp3。然而，自 2017 年以来，事情变得更容易了:

*   基于 selenium 的 [`audible-activator`](https://github.com/inAudible-NG/audible-activator) 工具已经被一个名为 [`inAudible-NG/tables`](https://github.com/inAudible-NG/tables) 的离线工具/轨迹预设所取代，它应该更可靠(我无法让 audible-activator 工作)。
*   不需要其他工具:`ffmpeg`支持开箱即用的音频转换。

## 我们需要什么？

接下来，我们需要安装两个软件:- [`inAudible-NG/tables`](https://github.com/inAudible-NG/tables) 可以克隆一次使用- `ffmpeg`可能已经安装在您的系统上

`inAudible-NG/tables`将为我们提供解密文件所需的激活哈希。这个散列对每个帐户都是唯一的，这意味着你只需要为你所有的有声读物生成一次。

`ffmpeg`将转换资料库中的文件。aax 格式到你喜欢的任何格式。我推荐用 opus(最好的压缩)或者 mp3(最好的支持)。

## 获取激活字节

为了提取“激活字节”，我们首先需要一个 aax 文件的文件散列:

```
$ ffprobe audiobook.aax
[...]
[aax] file checksum == 27ae5bf7df0bab8401776657d90dca85XXXXXXXX
[aax] activation_bytes option is missing!
[...] 
```

Enter fullscreen mode Exit fullscreen mode

然后这个散列被传递给 rcrack(从`inAudible-NG/tables` ):

```
$ ./rcrack . -h 27ae5b47df0bab6401776657d90dca85XXXXXXXX
[...]
result
----------------------------------------------------------------
27ae5b47df0bab6401776657d90dca85XXXXXXXX [...] hex:c345eXXX 
```

Enter fullscreen mode Exit fullscreen mode

## 转换文件

转换文件可以用所有常用的 ffmpeg 选项来完成，还有一个额外的`-activation_bytes`选项包含上面的十六进制散列。示例:

`$ ffmpeg -i audiobook.aax -activation_bytes c345eXXX freedom.mp3`

## 备注

*   此处显示的所有步骤都是为了保存文件。如果你想听高质量的有声读物，那就去买，不要盗版。
*   上面例子中的散列和激活字节显然是匿名的。