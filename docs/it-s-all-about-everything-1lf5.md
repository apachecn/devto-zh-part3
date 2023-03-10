# 这是一切的一切

> 原文：<https://dev.to/dejavo/it-s-all-about-everything-1lf5>

[![Everything KeyArt from [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Everything_KeyArt.jpg)](img/508fc8cc6620cacb87e38f11a1849650.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aALrP1SS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2AgBfwfObWBRawUOkSnQLhNw.jpeg)

 *原载于[中](https://medium.com/@dvirsegal/its-all-about-everything-95043d1b0aa)于<time title="Tuesday, March 26, 2019, 07:46:15 PM">2019 年 3 月 26 日</time>* 
几年前一个队友改变了我对日常电脑使用体验的看法。在那之前，我曾经像微软打算的那样使用 Windows 资源管理器浏览文件夹和文件，但是它出了问题，太多的点击和开始菜单搜索需要一些改进。总的来说，这与我通常的互联网浏览体验大相径庭，在那种情况下，你打开你最喜欢的搜索引擎，开始输入你想要的任何东西。然后**突然发现**的时刻来了，他向我介绍了 [*一切*](https://www.voidtools.com) 。

#### 一个小工具，生产力的一大步

这个小巧的工具专注于性能。它有一个小的安装文件，干净和简单的用户界面。它依赖于快速索引，能够以最小的数据库占用空间快速获得结果，并实时更新文件系统。

用法很简单，你在一个搜索框中键入你要找的文件和文件夹，搜索结果就会立即显示出来，这要归功于索引文件。根据 ***voidtools*** ，Everything 只索引文件和文件夹的名称，这需要几秒钟来建立它的数据库。例如，全新安装的 Windows 10(大约 120，000 个文件)需要大约 1 秒的时间进行索引。100 万个文件大概需要 1 分钟。真快。

占用空间很小，全新安装的 Windows 10 使用大约 14 MB 的内存和不到 9 MB 的磁盘空间。而 1，000，000 个文件使用大约 75 MB 的 ram 和 45 MB 的磁盘空间。这意味着软件性能是 voidtools 的开发人员所关心的，看起来他们真的在努力解决这个问题。

#### 朴素简单的用法

双击托盘图标、桌面快捷方式或我个人的最爱、**热键**就可以打开一切。我为即时搜索定义了 *Ctrl+Alt+F* 。

在“选项”菜单的“索引”设置下，我建议定义从你选择的任何位置索引哪个文件夹(也可以是网络文件夹)和哪个 NTFS (C:\，D:\，等等。)要包含的驱动器。

[![](img/9f16e583c0c759f677efb7e6077471e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mzjOcdEi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2AaQsiYwMnHmfGRTkb.png)
[![Indexes: Folders and NTFS drives](img/15e3390e6e619fb057811f79b8540167.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--wiWdOzDs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AW10Yz8ZC-5RNT8xKmByxtg.png)

一切都包含基本和高级功能。您可以使用通配符。

例如，搜索 ***t*b*** 会得到以 ***t*** 开始并以 ***b*** 结束的所有文件和文件夹。

也支持布尔运算符。

> ![unknown tweet media content](img/819db00a84cf2b8489ec8bb138428dd9.png)![CodeChef profile image](img/32648b2380fa4d454ff98f11275d23d5.png)code chef@ code chef![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)布尔发逻辑[# code chef](https://twitter.com/hashtag/CodeChef)[# programmer](https://twitter.com/hashtag/ProgrammersHumor)
> via:[【bit.ly/2njqHS5】](https://t.co/f3LcAkWEiD)2018 年 1 月 28 日上午 09:30[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=957546335254798336)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=957546335254798336)

**和**是默认的布尔运算符。要搜索两个搜索词中的任何一个，只需在两个词之间加一个|。IMHO，排除运算符(！)是最有用的一个。

有了这些基本功能，你可以通过编写文件扩展名来过滤特定的文件类型(例如 ***.mp3** )，只搜索特定文件夹下的文件(例如 **\temp** )等等

[![_“Everything_” is a filename search engine for Windows.](img/dc82d053dd49969d2645c1104f48403b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kvyas9Wl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AbO3wt7PH2j7MmQli9wFjow.png)

#### **高级搜索**

您可以搜索文件的内容(没有索引，因此速度较慢)。

一个例子是，当你想查找本周修改过的包含文本“香蕉”的邮件时:

> **。eml dm:本周内容:香蕉*

ID3 标签和 FLAC 标签搜索是一个选项:

> 年份:2002 年..2005
> 
> 流派:电子
> 
> regex:专辑:<sup>【a-n】</sup>
> 
> 通配符:片名:红*
> 
> 曲目:> 10
> 
> 年份:> =2000

此外，您可以使用 regex 来覆盖搜索语法。必须从搜索菜单中启用正则表达式:

[![Regex operators](img/da27df4d2e0363fd34c270b1a1093b5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M6FDHyXu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A5bWRC4i5VCSKaWFwNhd-Mg.png) 
正则运算符

Voidtools 友好地给**添加了很多[的](http://voidtools.com/support/everything/searching/#advanced_search/)**例子，可以用来学习更多的搜索行为。

作为最后一个小提示，Everything.ini 存储了所有东西的所有设置，所以我建议将它备份到您最喜欢的云存储中，在新安装计算机时，只需指向。ini 本地副本。

一切都是免费的，可以从下面的链接下载。

[**一切**](//www.voidtools.com)