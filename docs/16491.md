# dev.to 上最喜欢的作者是谁？-前 500 个职位的分析

> 原文：<https://dev.to/trentyang/who-is-the-most-liked-author-on-devto---analysis-of-top-500-posts-2kg>

dev.to 是一个蓬勃发展的程序员社区。2016 年以来，有很多好的内容被创造、分享和喜欢。

作为一个新人，我很好奇人们在 dev.to 上关心什么，因为我希望我的帖子是相关的。

所以我决定分析 dev.to 上使用 javascript 创建的前 500 篇帖子。

## 数据采集

dev.to 没有提供 API。但他们已经在这个页面上有排名的帖子[https://dev.to/top/infinity](https://dev.to/top/infinity)。我需要做的就是向下滚动，直到我得到 500 个帖子，然后丢弃这些数据。这里是[剧本](https://gist.github.com/Chun-Yang/735cd1ef320b6baf313eebdfd210f463)和[数据](https://gist.github.com/Chun-Yang/c19fd8c3209212fd820831a9e816a9d7)。

我们可以从数据中提取很多东西。我对以下问题感兴趣:

*   最喜欢哪位作者？
*   哪个作者得到的评论最多？
*   哪个标签使用最频繁？
*   热门帖子标题里有数字吗？
*   vim vs emacs？

我写了一个[脚本](https://gist.github.com/Chun-Yang/6acd00cf7f9a0f1dd29393175b2c25d8)来回答他们。你也好奇吗？请击鼓！

[![drumroll](img/8de7b134b2d15612005de64c9bc60e3e.png)](https://i.giphy.com/media/ogGmxeqA8L3sA/giphy.gif)

## 最喜欢哪位作者？

拥有 23 篇 top 500 帖子的 Ali Spittel 绝对是 dev.to 的宠儿！我个人认为她在博客主题上的帖子信息丰富，具有可操作性。

以下是 dev.to 上最受欢迎的 10 位作者！

| 名字 | 喜欢 | 等级 |
| --- | --- | --- |
| 阿里·斯皮特尔 | Nine thousand five hundred and seventy | one |
| 本·哈尔彭 | Eight thousand eight hundred and seventy-six | Two |
| Alex 👨🏼‍💻FullStack.Cafe | Three thousand and ninety-nine | three |
| 萨尔塔克·夏尔马 | Two thousand two hundred and eighty-eight | four |
| 最大核数 | Two thousand one hundred and fifty-six | five |
| 尼基塔·索博列夫 | One thousand nine hundred and eighty-seven | six |
| 艾玛·韦德金德·✨ | One thousand nine hundred and forty-one | seven |
| 布莱恩·奥斯普丘克 | One thousand six hundred and ninety-eight | eight |
| 金·阿内特· | One thousand six hundred and ninety-one | nine |
| 艾萨克·莱曼 | One thousand five hundred and ninety-four | Ten |

## 哪位作者获得的评论最多？

作为 dev.to 的创始人， [Ben Halpern](https://dev.to/ben) 得到的评论最多。谢谢你，本，谢谢你为创建这样一个令人惊奇的社区所做的努力！

不足为奇的是，前 10 名中有 5 名与前 10 名最受欢迎的作者重叠。

| 名字 | 评论 | 等级 |
| --- | --- | --- |
| 本·哈尔彭 | One thousand three hundred and fifty-five | one |
| 阿里·斯皮特尔 | One thousand one hundred and fourteen | Two |
| 开发到员工 | Eight hundred and ninety-five | three |
| 金·阿内特· | Four hundred and fifty-two | four |
| 安德鲁·戴维斯 | Three hundred and one | five |
| 绍拉布·夏尔马 | Three hundred and one | six |
| 大卫·威克斯 | Two hundred and seventy-one | seven |
| 萨尔塔克·夏尔马 | Two hundred and sixty-nine | eight |
| Dan Abramov | Two hundred and forty-four | nine |
| 最大核数 | Two hundred and fourteen | Ten |

## 哪个标签使用频率最高？

原来，前 500 个帖子中最受欢迎的标签与[https://dev.to/tags](https://dev.to/tags)上的标签非常相似。

前端工程在这里很受欢迎#javascript，#react。社区似乎对#初学者非常友好。

啊哈！我想我可以为初学者创建一些 react 相关的帖子，以获得更多的喜欢。😜

[![lightbulb](img/78df7fbeeae5ee114435bb0c7ed20762.png)](https://i.giphy.com/media/MJW59l6PiMfew/giphy.gif)

| 标签 | 二手的 | 等级 |
| --- | --- | --- |
| #javascript | One hundred and twenty-eight | one |
| #初学者 | One hundred and twenty-six | Two |
| #职业 | One hundred and sixteen | three |
| #webdev | One hundred and six | four |
| #生产力 | Seventy-five | five |
| #编程 | forty-nine | six |
| #讨论 | Thirty-nine | seven |
| #学习 | Twenty-nine | eight |
| #做出反应 | Twenty-six | nine |
| #git | Two | Ten |

## 热门帖子标题里有数字吗？

在排名前 500 的帖子中，大约有 100 个在标题中有数字。文章标题中的数字和受欢迎程度之间似乎没有任何强有力的关联。

## 我是来对付 emacs 的吗？

500 强中有 6 #vim 和 0 #emacs 岗位。对于所有 vim 用户，我想说**我们是 dev.to 的冠军**！

[![we are the champion](img/f3ecc87309f5282a1eb4ff669018a9bb.png)](https://i.giphy.com/media/3o7TKC2QtRON2JhUfS/giphy.gif)

开玩笑的。dev.to 是一个非常包容的社区。似乎 vim 和 emacs 都不是社区中非常受欢迎的话题。我觉得 VC 代码之类的现代编辑器流行多了。

就是这样！愿最好的帖子胜出！

如果你感兴趣的话，这里有我的博客 https://trent yang . com/who-is-the-most-liked-on-dev-to-analysis-of-top-500-posts/。