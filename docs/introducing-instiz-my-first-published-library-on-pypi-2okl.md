# 介绍 instiz，这是我在 PyPI 上发布的第一个库

> 原文：<https://dev.to/chromadream/introducing-instiz-my-first-published-library-on-pypi-2okl>

你好，我想介绍一下`instiz`，这是 Python 3+的 [iChart 图表网站](http://ichart.instiz.net)的包装器。iChart 是韩国音乐的实时图表网站。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [色梦](https://github.com/Chromadream) / [灌输](https://github.com/Chromadream/instiz)

### 一个仅用于 Python3 的 iChart K-Pop 排行榜乐谱库。

<article class="markdown-body entry-content container-lg" itemprop="text">

# instiz

一个仅用于 Python3 的 iChart K-Pop 排行榜乐谱库。

[![Code style: black](img/52e0dcb045b4af25b34c9a30929debe5.png)](https://github.com/ambv/black)

## 装置

`pip install instiz`

## 入门指南

开始很容易。以下示例是获取 iChart 实时图表中的前 10 名。

```
from instiz import iChart
ichart = iChart()
top_10 = ichart.realtime_top_10()
```

## 类型提示

如果您正在使用 Python 3.7+的类型提示特性，用于图表返回类型的类型可以从`instiz.models`模块导入。

```
from instiz.models import Entry
def get_name(entry: Entry) -> str
    return entry.title
```

## 艺术家姓名

现在，除了 iChart 网站提供的原始艺术家姓名之外，还可以很容易地获得一位艺术家的韩文姓名和英文姓名。Nice title 属性将总是返回英文名称，除非不可用。如果其中一个名称不可用，该属性将返回一个空字符串。

```
from instiz
```

…</article>

[View on GitHub](https://github.com/Chromadream/instiz)

## 背景

我最近开始以技术版主的身份为 [/r/kpop](https://github.com/rkpop) 投稿。对组织的一瞥可能会显示，Reddit 社区使用这么多代码是很不寻常的，但它确实帮助了版主的工作，并为社区的用户提供了增值。

## instiz 为什么会存在？

subreddit 有一个歌曲的实时图表小部件，每小时从 iChart 中抓取一次。

[![old chart](img/c056522edda8171684c300843822041e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R-g5aI-o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uwno3xssdb4cwc8ndhys.png)

然而，当 Reddit 推动重新设计时，需要将小部件移植到重新设计，这样旧站点和新站点的用户之间就不会有功能差异。因此，我开始尝试移植。然而，浏览一下当前运行的小部件的源代码，就会发现一个可怕的嵌套混乱。不仅如此，scraper 和解析器与实际的更新功能紧密耦合。

`instiz`最初是为了分离抓取过程和小部件更新过程而创建的。它也可以用于任何类型的脚本、机器人或任何利用 iChart 数据的程序。

## 特性

尽管`instiz`的主要功能相当简单，即从 iChart 抓取并处理数据，但它还有一些值得注意的附加功能。

*   理智艺术家姓名处理。

在 iChart 的网站上，艺术家姓名的格式不一致。例子包括`제니 (JENNIE)`、`TWICE (트와이스)`、`아이유(IU)`和`Queen(퀸)`。instiz 确保韩语和英语名称经过预处理，图书馆用户无需额外处理即可轻松检索。

代码示例:

```
 from instiz import iChart

ichart = iChart()
first_place = ichart.get_next_entry()
nice_title = first_place.nice_title # "JENNIE - SOLO" raw_artist_name = first_place.artist.raw_name # "제니 (JENNIE)" english_artist_name = first_place.artist.english_name # "JENNIE" korean_artist_name = first_place.artist.korean_name # "제니" 
```

*   API 刷新。

提供了刷新功能，因此开发人员不必在每次需要一组新数据时初始化该类的新实例。

代码示例:

```
 from instiz import iChart

ichart = iChart()
top_10 = ichart.realtime_top_10()
ichart.refresh()
top_10 = ichart.realtime_top_10() 
```

*   它是完全开源的。

是由麻省理工学院出版的，对于想开始从事开源文化的人来说，有一些“好的第一期”。然而，我意识到`instiz`中缺少了一些通常在开源项目中的项目。请通过提出拉取请求来帮助我实现这一点。

## 谢谢。

谢谢你一路看完这个帖子。我真的很感激。祝你愉快。