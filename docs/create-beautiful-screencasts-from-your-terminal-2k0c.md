# 从您的终端创建美丽的截屏

> 原文：<https://dev.to/orkon/create-beautiful-screencasts-from-your-terminal-2k0c>

我想展示我的 CLI 应用程序，但不知道解决方案。我想象我可以把我的屏幕录制成视频并转换成 GIF 文件，这样你就可以把它们嵌入到 GitHub 或其他地方。在谷歌搜索了一段时间后，我发现了一种更好的方法，它可以生成 SVG 而不是 gif，而且使用起来毫不费力。

## 安装[加速器](https://github.com/asciinema/asciinema)

这个工具允许你记录你的终端会话。它还有一个网站，你可以上传你的录音，每个人都可以看到它们[https://asciinema.org/](https://asciinema.org/)。我建议在那里创建一个帐户。

```
npm i asciinema -g 
```

不幸的是，录制的会话需要播放器来播放，所以它们不能嵌入任何网站。例如，您不能将其插入 Github 的自述文件中。

## 安装 [svg-term-cli](https://github.com/marionebl/svg-term-cli)

这是一个有用的工具，可以将使用`asciinema`记录的会话转换为 SVG:

```
npm i svg-term-cli -g 
```

## 如何记录

我使用它的最大空闲时间为 2 秒。这将开始录制:

```
asciinema rec -i 2 
```

运行这个程序后，做任何你想在终端中显示的事情。按`Ctrl + D`停止记录。

如果您将会话上传到 asciicinema.org，创建一个 SVG，如下所示:

```
svg-term --cast=YOUR_CAST_ID --out demo.svg --window 
```

之后，您可以在网络浏览器中查看`demo.svg`。

如果您在本地存储记录，创建一个 SVG，如下所示:

```
cat path-to-your-cast-file | svg-term --out demo.svg --window 
```

## 嵌入

现在，您几乎可以在任何地方嵌入 SVG 文件。下面是我的[时事通讯-cli 项目](https://github.com/JustComments/newsletter-cli) :
中的一个例子

```
<p align="center">
  <img src="https://raw.githubusercontent.com/OrKoN/newsletter-cli/master/demo.svg?sanitize=true" width="572" alt="newsletter cli demo">
</p> 
```

下面是它的样子:[参见 Github 上的](https://github.com/justcomments/newsletter-cli#--newsletter-cli--)

有趣的是，dev.to 使用一些图像代理将动画 SVG 转换为静态图像，所以似乎不可能将这样的 SVG 嵌入到内容中。但对封面形象似乎很管用！

*最初发布于[https://60 devs . com/create-beautiful-screencasts-from-your-terminal . html](https://60devs.com/create-beautiful-screencasts-from-your-terminal.html)T3】*