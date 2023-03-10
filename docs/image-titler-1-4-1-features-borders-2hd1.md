# 图像标题 1.4.1 具有边框

> 原文：<https://dev.to/renegadecoder94/image-titler-1-4-1-features-borders-2hd1>

正如你们中的许多人可能知道的那样，我使用我为这项工作编写的 Python 脚本来生成我所有的特色图像。这个脚本叫做 Image Titler，我刚刚发布了带有边框的 1.4.1 版本。

## 灵感的变化

事实证明，我的会员网站在过去的几个月里并没有爆炸，但我一直在稳步增加会员。在这一点上，我接近 60 个成员——其中大部分是免费的。

每隔一段时间，我的一个成员就会评论这个网站。例如，我的一个成员最近分享了以下反馈:

> “成员”选项卡显示“帐户”和“登录”，无论您是否登录(没有“注销”)。如果你看不到一个页面，它只会显示“你无权查看此页面。”在内容所在的小文本中，不告诉你是否因为你没有登录，或者没有订阅。

从那以后，我解决了所有这些问题。但是，还有更多:

> 也没有简单的方法来判断一篇博客文章是否免费。如果我去会员。帐户>订阅>基本它将我发送到/tag/free，让我过滤免费的，但我看不到从博客本身到达那里的方法。博客文章列表中也看不到标签。

最后一点反馈促使我想出一个办法，让我更清楚哪些帖子是免费的，哪些不是。不幸的是，我真的不想玩 PHP 和主题文件，所以我选择了我能控制的东西:特色图片。

## 图像边框

现在，当你去[博客存档](https://therenegadecoder.com/category/blog/)的时候，你应该会看到特色图片和平时有点不一样。取代典型的两个红色矩形和白色标题，现在您将看到每个矩形周围有一个彩色边框。

彩色边框有两种形式:金色和银色。金色边框表示[高级内容](https://therenegadecoder.com/tag/premium/)，而银色边框表示[免费内容](https://therenegadecoder.com/tag/free/)。没有边框的特色图片只是表示公共内容。

这些边界是使用 pillow 6.0.0 功能完成的，该功能允许您在具有一定宽度的矩形上设置轮廓。我使用的代码如下所示:

```
draw.rectangle( 
    ( 
        (IMAGE_WIDTH - width - X_OFFSET * 2, position), 
        (IMAGE_WIDTH, position + RECTANGLE_HEIGHT) 
    ), 
    fill=RECTANGLE_FILL, 
    outline=TIER_MAP.get(tier.lower(), None), 
    width=7 
) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想尝试这个新功能，[获取最新版本的图像标题器](https://pypi.org/project/image-titler/)并运行以下命令:

```
image_titler --tier free # Adds a silver border 
image_titler --tier premium # Adds a golder border 
```

Enter fullscreen mode Exit fullscreen mode

当然，我需要时间来为我所有的博客文章推出这一改变。在写这篇文章的时候，我有 73 个，所以改变可能需要一段时间。现在，我将从最新的帖子开始，然后往回看。希望在这篇文章发表的时候，所有的文章都已经更新了。

## 反馈

除了边框的变化，我还添加了更多免费和优质内容的链接，所以希望你能更轻松地浏览这个网站。如果没有，请告诉我！我很高兴得到反馈。如果一切看起来都很好，那就太好了！我喜欢工作轻松的时候。