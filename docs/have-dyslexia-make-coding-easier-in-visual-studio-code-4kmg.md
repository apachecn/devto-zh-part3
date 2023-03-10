# 有阅读障碍？使 Visual Studio 代码中的编码更容易

> 原文：<https://dev.to/deadlybyte/have-dyslexia-make-coding-easier-in-visual-studio-code-4kmg>

# 在 Visual Studio 代码中使用 open readisation 字体

如果你像我一样，患有诵读困难症，有时觉得阅读困难📕👨‍💻用你的 <abbr title="Integrated development environment">IDE</abbr> 使用的标准等宽字体❓你的代码，那么下面的步骤你可能会感兴趣。

## 什么是开放阅读障碍字体？

维基百科提供了一些字体的背景。

> “开放式阅读障碍是一种免费的字样/字体，旨在减轻阅读障碍引起的一些常见阅读错误，尽管它的好处在科学研究中受到质疑。这种字体是由 Abelardo Gonzalez 创造的，他通过开源许可发布了这种字体。该设计基于 DejaVu Sans，也是一种开源字体。

## 下载开放阅读字体

首先，如果你还没有从下面的链接下载令人敬畏的免费开放阅读字体

[https://www.opendyslexic.org/](https://www.opendyslexic.org/)

一旦你有了 openreadisc 字体产品，然后下载 openreadisc Mono zip 文件(等宽字体在编码时效果最好)。

**注意:**一个等宽字体的字母和字符各自占据相同的水平空间。这些通常被称为固定宽度字体。

## 安装开放式阅读障碍单声道字体

**注意:**这些步骤记录了如何通过 Windows 10 安装字体。欢迎在评论区分享如何在其他操作系统上安装的细节。

在安装 openreadisc Mono 字体之前，你需要解压它。解压缩后，双击 openreadiscmono-regular . OTF 文件，这将在 Windows 字体查看器中打开该字体。现在点击**安装**按钮。

[![OpenDyslexic Mono font in Windows Font Viewer](img/d05f6e6ba50f6bce355225b7d4f6df02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ch5tYBpG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2e0jxvm2gufrakuv7gs7.png)

## 设置 Visual Studio 代码

打开 Visual Studio 代码，从`File`菜单中选择`Preferences`、`Settings`或使用键盘快捷键`Ctrl` + `,`(在 Mac 上为`Cmd` + `,`)。根据您的偏好，可以为用户或工作区进行这些字体更改。此时，您需要确保选择了正确的选项卡。

键入`font`作为搜索词，这会过滤设置，只显示与字体相关的设置。找到“编辑器:字体系列”(这应该是显示的第一个设置)。在“字体系列”输入框中，将`OpenDyslexicMono`添加到文本的开头。

```
OpenDyslexicMono, Consolas, 'Courier New', monospace 
```

**注意:**变更自动保存。

Visual Studio 代码正在后台对设置文件进行以下更改。

```
{  ...  "editor.fontFamily":  "OpenDyslexicMono, Consolas, 'Courier New', monospace"  } 
```

下面是一个使用 opened 字体打开文件的例子。

[![IDE Using OpenDyslexic Font](img/5df53bd429c6cfac57ad35eda9a4fbe5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2_B6LEE2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tokk3z7vl7eq5pej5wi3.png)

我发现开放式阅读障碍单声道字体很有帮助，我希望它对其他开发者也有所帮助。通过评论来听听你使用它的经验，以及它是否让生活变得更容易，这将是很好的。

享受快乐编码！！！💻