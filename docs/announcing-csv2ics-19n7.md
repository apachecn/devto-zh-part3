# 发布 csv2ics

> 原文：<https://dev.to/dploeger/announcing-csv2ics-19n7>

不知何故，我总是[摆弄自动化日历事件](https://dennis.dieploegers.de/flying-high-on-ical-files/)，现在我甚至不得不处理 ICS *和* CSV 文件！

一些背景:我在一个业余戏剧团体中表演，也管理他们的内部通讯工具。在这个工具中，所有的排练、演出和会议都会呈现出来。

然而，我的演员同事和导演们并不精通技术来填充日历，也就是说，从他们的谷歌日历中导出 ical 文件，然后导入它们。

取而代之的是，他们将下一季的所有活动打包成一个 excel 表格。

这是一个 CSV 文件。

我搜索了各种 csv 至 ics 转换器，但没有找到满足我所有需求的转换器。我特别需要创建定制的供应商字段，我发现没有一个工具支持它。

所以我创造了我自己的。

叫 csv2ics(创意，嗯？)而且你可以在 [GitHub](https://github.com/dploeger/csv2ics) 和 [NPM](https://www.npmjs.com/package/csv2ics) 上找到。查看其 [README.md](https://github.com/dploeger/csv2ics/blob/master/README.md) 了解详情。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [德布洛格](https://github.com/dploeger) / [ csv2ics](https://github.com/dploeger/csv2ics)

### 将 CSV 文件转换为 ICS 日历

<article class="markdown-body entry-content container-lg" itemprop="text">

# 将 csv 文件转换成 ics 日历

[![NPM version](img/d1534feaea12f14f916818bd93895299.png)](https://www.npmjs.com/package/csv2ics)[![Build Status](img/517c9dc0a1b215c5aa52baea51085362.png)](https://travis-ci.org/dploeger/csv2ics)[![Coverage Status](img/9a8c2f263c3149c4bf8856e64641a8dd.png)](https://coveralls.io/github/dploeger/csv2ics?branch=master)

## 介绍

将 CSV 文件转换为 ICS 日历。

## 标题图

csv2ics 需要知道哪些列映射到哪些 ICAL 属性。这不容易通过 CSV 头文件来指定，所以我们依赖于头文件映射。

标题映射是以逗号分隔的字符串列表，用于指定每列的匹配 ICAL 属性。某些映射被正确地检测和处理(例如，日期字段的解析)。如果一个映射是未知的，它只是作为一个自定义属性被添加。

检测到这些映射:

*   摘要
*   DTSTART
*   数据结束
*   描述
*   位置

## 使用

使用 npm 安装 csv2ics:

```
npm install -g csv2ics 
```

使用运行它

```
csv2ics <input file.csv> <output file.ics&gt 
```

输入和输出文件是可选的。如果只指定了一个文件，它将被用作输入文件。如果没有指定文件，则从 STDIN 中获取 CSV 数据。

对于…

</article>

[View on GitHub](https://github.com/dploeger/csv2ics)