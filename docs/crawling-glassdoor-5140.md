# 爬行玻璃门

> 原文：<https://dev.to/sizief/crawling-glassdoor-5140>

[![header](img/f2e2325188db0f6dbee151269f596014.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ftMbrGm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/sizief/glassdoor-analyze/master/result/total-languages.png) 
所以一周前我在这里看了一个关于 Ruby 未来会杀死 Python 的帖子。一如既往，在那个帖子的评论区有很多争论。有人提到，谁杀谁并不重要，每种语言或工具都适用于某些东西；你必须为正确的工作选择正确的工具。

我认为这是正确的答案，但是，重要的是要考虑市场(或行业)对工具和语言的看法。他们想要一个 Ruby 程序员和想要一个 Python 开发者一样多吗？

为了找到答案，我创建了一个小的 Ruby 项目来完成这些任务:

*   为软件招聘广告抓取预定义城市的玻璃门页面
*   存储页面
*   创建一个字谜来计算每个关键字的出现次数(例如`Python`或`Ruby`
*   生成`yml`和`png`文件，以可视化行业对每种技能的需求。

这是全世界总共十个城市的输出。记住，这是包含每个关键词的广告数量。例如，如果广告包含`Java`，那么它会增加包含该特定关键字的广告总数。

[![Software Languages](img/24b6ddb783120af6278f8059140b34d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kRJyjC8S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sizief/glassdoor-analyze/blob/master/result/total-languages.png%3Fraw%3Dtrue)

这是提到的技术数量:

[![Software Technologies](img/2fd492e6af31fa8643b1e28812706ca3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dZjmpWPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sizief/glassdoor-analyze/blob/master/result/total-technologies.png%3Fraw%3Dtrue)

## 那么最终哪种语言比其他语言更受欢迎呢？

答案很简单:`Java`。但是如果你在寻找脚本语言，那么答案是`Python`、`Javascript`，然后是`Ruby`。然而，当你比较单个城市的结果时，会有一些有趣的发现。

例如，这是阿姆斯特丹的输出:
[![Amsterdam stats](img/8e78ae0c7c8d43e9dd88d322f691a40a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xo3G-GMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sizief/glassdoor-analyze/blob/master/result/amsterdam-languages.png%3Fraw%3Dtrue) 
注意到什么异常了吗？是的`Scala`比`Pyhton`或`Javascript`更受欢迎！

见这些报告的其余部分[这里](https://github.com/sizief/glassdoor-analyze/tree/master/result)。包括`New york`、`Berlin`、`London`、`Toronto`、`Singapore`、`Dubai`、`Tallinn`等图表。

## 关于项目

结构简单。首先有一个[配置文件](https://github.com/sizief/glassdoor-analyze/blob/master/config.yml)，你可以定义城市，关键字，类别等。

```
urls:
  - Tallinn;https://www.glassdoor.ca/Job/tallinn;jobs
job_types:  
  - software
  - back-end
  - front-end
category:
  - languages
  - technologies
languages:
  - java
  - javascript 
  - c 
```

然后，通过运行`client.rb`，它将从配置文件中获取第一个 URL，抓取网页，保存每个列表页面的所有 URL 特定参数，获取第二个页面，并重复它，直到最后一个页面。

之后，另一个类将再次抓取网站。这一次，它下载整个广告页面，并保存在磁盘上。

然后，第三个类创建所有预定义关键字的变位词，并扫描我们在上一步中保存的每个文档。然后，我们将结果保存为一个`yml`文件。

下面是输出的示例:

```
languages:
  java: 324
  javascript: 196
  c: 75
  c#: 140
  c++: 144
technologies:
  kafka: 41
  nosql: 60 
```

最后，在 Gruff Gem 的帮助下，我们从 YAML 文件中生成图像。

## 旁注

*   如果你正在调查你的下一个职业道路或下一门要学的语言，这可能会有帮助。没有比这更严重的了。
*   该项目几乎是可配置的。只需更新`config`文件:添加你想要的城市，第一个网址，你要找的关键词，什么类别。然后运行它(等待几分钟以获得所有数据)并检查结果文件夹的输出。[链接到项目](https://www.github.com/sizief/glassdoor-analyze)
*   玩得开心！