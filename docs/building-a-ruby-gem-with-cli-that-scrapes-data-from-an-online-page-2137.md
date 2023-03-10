# 使用 CLI 构建 Ruby gem，从在线页面抓取数据

> 原文：<https://dev.to/andreanasuto/building-a-ruby-gem-with-cli-that-scrapes-data-from-an-online-page-2137>

我一直在 Flatiron School(现在是 WeWork galaxy 的一部分)完成我的全栈 web 开发软件开发训练营。

作为第一个投资组合项目，我需要开发一个命令行应用程序。

CLI 项目的任务是构建一个 Ruby gem，为外部数据源提供命令行界面(CLI)。CLI 将由面向对象的 Ruby 应用程序组成。我可以从任何我喜欢的网页上搜集信息:天气预报页面、新闻页面、任何与体育相关的内容。基于这些信息，我创建了自己的程序。我选择了 Transfermkt 网站([https://www.transfermarkt.com/](https://www.transfermarkt.com/))，我搜集了我最喜欢的足球联赛(或者足球，这取决于你住在大洋的哪一边！)并玩弄这些信息。我创建了一个程序，可以向用户提供我所选择的联赛中某个特定球队的详细信息:意甲，意大利顶级足球联赛。
我一直在用 Ruby 中神奇的“Nokogiri gem”从网站上收集数据。

Transfermarkt 是一个很棒的德国足球网站。它有很多数据，包括几个详细的财务价值。我过去曾经对欧洲足球做过一些有趣的计算和分析。

我的节目首先展示了联盟目前的排名:

```
Welcome to Serie A CLI
1\. Juventus
2\. SSC Napoli
3\. Inter
4\. AC Milan
5\. Atalanta
6\. AS Roma
7\. Torino
8\. Lazio
9\. Sampdoria
10\. Cagliari Calcio
11\. Fiorentina
12\. Sassuolo
13\. SPAL
14\. Parma
15\. Genoa
16\. Bologna
17\. Udinese Calcio
18\. FC Empoli
19\. Frosinone
20\. Chievo Verona 
```

之后，可以根据他们的排名选择一个团队，并查看如下信息:

(我选择了我最喜欢的球队作为例子:国际米兰)

```
"Inter is currently valued $635.44mn"
Total team market value in millions of $

"Inter players average age is 28,8"
The average age of the team

"They play at Stadio San Siro - Giuseppe Meazza"
This line shows the stadium where they play 
```