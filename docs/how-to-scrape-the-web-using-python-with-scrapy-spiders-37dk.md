# 如何使用 Python 和 ScraPy 蜘蛛抓取网页

> 原文：<https://dev.to/strikingloo/how-to-scrape-the-web-using-python-with-scrapy-spiders-37dk>

有时候 Kaggle 还不够，还需要自己生成数据集。

也许你需要你正在训练的这个疯狂的卷积神经网络的蜘蛛图片，或者也许你为了，嗯，科学的目的想要刮 NSFW 子网格。

无论你的理由是什么，抓取网页都能给你非常有趣的数据，帮你汇编出牛逼的数据集。

在本文中，我们将使用 ScraPy 来抓取 Reddit 子编辑并获取图片。

有些人会告诉我[使用 Reddit 的 API](https://github.com/praw-dev/praw) 是一种更实用的获取数据的方法，这绝对是真的。确实如此，我可能很快会写一篇关于它的文章。

但是只要我们在很小的剂量内完成，并且不要让 Reddit 繁忙的服务器超负荷工作，应该没问题。所以请记住，本教程仅用于教育目的，如果你需要 Reddit 的数据，你应该使用官方渠道，比如他们的[牛逼 API](https://github.com/praw-dev/praw) 。

那么，我们如何着手清理网站呢？让我们从头开始。

### 检查机器人. txt

首先我们将走进 reddit.com/robots.txt。按照惯例，一个网站会让他们的 *robots.txt* 文件可以从他们的主域访问。它遵循以下格式:

```
User-agent: \<pattern\>
Disallow: \<patterns\> 
```

其中 U_ser-agent_ 描述了一种设备类型(我们属于*，通配符模式)，而 *Disallow* 指向我们无法抓取的 url 模式列表。

我在里面没看到/r/*，所以我*觉得*刮一个 subreddit 的主页面也可以。出于礼节，我仍然建议你在任何严肃的项目中使用 API。

不尊重一个网站的 *robots.txt* 文件可能会有法律后果，但这主要只是让你看起来像一个卑鄙的人，我们不希望这样。

### 设置我们的项目。

为了用 Python 抓取网站，我们将使用 ScraPy，它是主要的抓取框架。有些人喜欢美丽的声音，但我觉得 ScraPy 更有活力。

ScraPy 抓取的基本单元被称为 _ spiders，我们将从创建一个空单元开始这个程序。

所以，首先我们会安装 ScraPy:

```
pip install --user scrapy 
```

然后我们将开始一个零碎的项目:

```
scrapy startproject project\_name 
```

您可以在此输入任何内容，而不是项目名称。该命令将创建一个目录，其中包含许多文件和 python 脚本。

现在，对于我们的最后一个初始化命令，我们将创建我们的第一个蜘蛛。为此，我们将运行 scrapy 的 *genspider* 命令，该命令将一个蜘蛛的*名称*和一个 _ 域 url_ 作为其参数。

我会把我的命名为 *kitten-getter _(当心:剧透)和 crawl _reddit.com/r/cats* 。

```
scrapy genspider kitten\_getter reddit.com/r/cats 
```

现在我们只进入/*spider*目录，不关注其余部分。和往常一样，我在[GitHub 项目](https://github.com/StrikingLoo/kitten-getter)中发布了我的代码。

### 设置我们的第一个蜘蛛

在 *spiders* 目录中，我们将打开名为 _ kitten _ getter.py _ 的文件并粘贴以下代码: