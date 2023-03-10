# 我如何写在线文章

> 原文：<https://dev.to/azure/how-i-write-online-articles-1lfb>

## 开门见山

我喜欢写作，当我写作时，我更喜欢用 markdown 写作。最近，人们一直在问我的写作过程，所以我决定在这篇短文中写下来。这是我的过程，你不应该觉得有义务使用它。希望这能帮到你。

## 我的写作过程

所有这些都假设我有一个想法，我知道我在写什么。这本身是一个完全不同的话题，不在这里讨论。如果你想知道我是如何写作的，这是最合适的地方。

### 我的目标

我写作的主要目标是:

1.  稳定的工具:我写的东西必须是稳定的(我不想重写它！)
2.  保存:我想保存我的文章，这样我就可以迭代，引用回来，永远拥有它。
3.  校对:我想对我的文章进行拼写和语法检查。
4.  减价:我更喜欢用减价的方式写作
5.  离线:它必须离线工作

## [T1】我的工作流程](#my-workflow)

我的工作流程很短，并且符合我的主要目标。所以让我们直接进入工作流程。

### - 1 - VS 代码

我用 [VS 代码](https://code.visualstudio.com?wt.mc_id=devto-blog-jopapa)写文章。它有极好的降价支持，而且超级稳定，这满足了我的两个目标。此外，我整天都在用这个进行编码，所以我不必切换到另一个工具。

以下是我使用的 VS 代码扩展:

*   [降价皮棉](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint&wt.mc_id=devto-blog-jopapa)
*   [降价预览](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-preview-github-styles&wt.mc_id=devto-blog-jopapa)
*   [拼写正确](https://marketplace.visualstudio.com/items?itemName=ban.spellright&wt.mc_id=devto-blog-jopapa)
*   [孔雀](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa) -我用彩色编码我的 vs 代码编辑器写文章

### - 2 -打样

我目前在我的 markdown 中使用拼写权利(见上面的链接)进行拼写检查。然后，我将内容复制并粘贴到 Grammarly 中的一个本地文档中进行语法检查。你可以使用任何语法检查工具，包括微软 Word。

我发现这是一个很好的步骤，因为拼写检查只能捕捉到故事的一部分。语法检查有助于避免混淆句子。

### - 3 -推送至私有 GitHub 回购

通过为我的内容创建一个 GitHub 存储库，保存我的内容并确保我不会丢失它(我的两个目标)得到了加强。我更喜欢这是私人的，因为它包含了我的写作。我还以对我有意义的方式组织我的存储库。我可以快速找到我的文章，修改它们，迭代它们，然后继续前进。

VS 代码和 GitHub 集成的很好，我每天都用这个做源码控制。我发现呆在我熟悉的工具中写文章、提交、推动和前进很舒服。

下面是我在 dev.to 中的 meta 的一个例子:

```
--------
title: How I Write Online Articles
published: true
description: How you can write online articles
tags: devrel, vscode, markdown, discuss
cover_image: https://thepracticaldev.s3.amazonaws.com/i/n0ecqvxjrzh9qy6ekfgy.png
canonical_url: https://johnpapa.net/how-i-write-online-articles/
-------- 
```

Enter fullscreen mode Exit fullscreen mode

### - 4 -复制并粘贴到目标站点

我的文章的目标可以是 Ghost(我的博客所在的地方)、Medium、dev.to 或任何数量的平台。不幸的是，因为他们都是不同的，有些支持各种功能，很少有 API，我目前复制和粘贴。复制和粘贴只需要几分钟，所以没什么大不了的。

一旦我粘贴到目标平台，我一般会发现一些看起来有点尴尬的东西。我在目标平台中进行调整，在预览版中测试它(不是发布它)，并重复直到它变好。

然后，我将更改复制回我的本地 VS 代码编辑器，提交并推送。

### - 5 -在我的博客上发布，然后开发到

我首先将我的内容发布到我的博客上，因为这是我的博客，我喜欢有一个存放我所有内容的家。然后，我通过设置`canonical_url`将我的博客文章的规范 URL 复制到标题中的 dev.to 目标。然后我会在 dev.to 上发表同样的文章。

## 封面图片或素描笔记

我喜欢给我的文章增加一些视觉效果。我在文章中寻找视觉效果来帮助连接我脑海中的概念。还有，很好玩！有很多方法可以获得免版税的图片。Pexels 就是其中之一。

通常我想要一个非常具体的图像，这是很容易画一个。这是我使用素描笔记的地方。这篇文章顶部的草图是我用来规划文章的。

> 我将在以后的文章中分享更多关于我如何以及为什么使用 sketchnotes 的内容。

## 对未来的设想

我希望有一个面向所有编写平台的开放 API，尤其是 dev.to。想象一下，用您最喜欢的工具(如 VS Code)在本地编写，然后按下一个命令，发布 dev.to 上的文章！我目前正在讨论如何编写这样的扩展以发布到 dev.to。扩展需要来自目标平台的 API 和一系列我可以在扩展中编码的酷功能(例如，使用 liquid embeds 的 gist 预览)。但是...嘿...这会很有趣，也很有用。

dev.to 的优秀团队正在考虑一个 API。我想这是他们正在优先考虑的一长串伟大想法中的一个特征。

## 显示读取时间

我想看看降价需要多长时间的估计。这有助于我知道我是否达到了我的目标长度(无论是更长还是更短)。这是我写的一篇关于这个新扩展的文章和一个你可以安装它的链接。

[![john_papa image](img/e4bea158821130090634a4a56bffc625.png)](/john_papa) [## 在 VS 代码中显示读取你的降价的时间

### 约翰爸爸 1919 年 4 月 8 日 3 分钟阅读

#devrel #vscode #markdown #discuss](/azure/show-the-time-to-read-your-markdown-in-vs-code-2p9l)

> 你可以在这里从 VS 代码市场获得[读取时间](https://marketplace.visualstudio.com/itemdetails?itemName=johnpapa.read-time&wt.mc_id=devto-blog-jopapa)

## 你是干什么的？

我分享了我的写作过程。我很想听听你的故事！你的不一样吗？你喜欢它的什么？你希望你能改变什么？