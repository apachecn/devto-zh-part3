# 停止限制你的开源库的潜力

> 原文：<https://dev.to/jerodsanto/stop-limiting-your-open-source-library-s-potential-9bn>

今天早上，我偶然发现了格里菲斯，这是一款基于 T2 React 的网络视频播放器。这很酷，但你知道什么更酷吗？我们都可以使用的网络视频播放器。

* * *

开源中有一个反复出现的主题，即`TECHNOLOGY_X`变得流行，然后一堆库和工具被专门为`TECHNOLOGY_X`创建(或移植到)`TECHNOLOGY_X`，但它们不能与`TECHNOLOGY_Y`或`TECHNOLOGY_Z`一起使用。

这在前端 javascript 领域尤其常见，这些年来有如此多的框架来来去去。例子！

1.  你想要一个搜索框，但你发现一个[角搜索框](https://github.com/dnauck/angular-advanced-searchbox)
2.  您想要一个地理编码库，但是您找到了一个 [jQuery 地理编码库](https://ubilabs.github.io/geocomplete/)
3.  你想要一个日期选择器，但是你找到了一个[余烬日期选择器](https://github.com/wesm87/ember-datepicker)

不一定非要那样！

稍微提前考虑一下，你可以构建一个通用的库，然后提供小的包装库，这些包装库使通用库适应特定的框架用例。这被称为[适配器模式](https://en.wikipedia.org/wiki/Adapter_pattern)，我发誓它没有我们在这条推文中描述的那么糟糕。

> ![unknown tweet media content](img/77e8c8aca224752ae0c30768c51e3623.png)![Changelog profile image](img/9cc1b78e4325590eaf7604dfd6c28d8c.png)Changelog@ Changelog![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)适配模式:10/10 推荐！2019 年 3 月 25 日下午 16:59[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1110224694093987842)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1110224694093987842)27[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1110224694093987842)76

Nicolás Bevacqua 多年来一直将这一原则运用于实践，并取得了巨大成功。如果你需要一些帮助来弄清楚细节，他是一个很好的榜样！

例证:他创建了[Dragula](https://github.com/bevacqua/dragula)——一个通用的拖放库，你可以直接和 vanilla JS 一起使用。然后他又用适配器跟进(他称之为*桥*——相同的区别):

1.  官方[角桥](https://github.com/bevacqua/angularjs-dragula)
2.  官方[角 2 桥](https://github.com/valor-software/ng2-dragula)
3.  官方[反应桥](https://github.com/bevacqua/react-dragula)

这种方法更酷的是，尼古拉斯为其他人的到来铺平了道路，为德拉古拉设计了一个 Ember 适配器，为德拉古拉设计了一个 T2 Aurelia 适配器，等等。

明年`TECHNOLOGY_X++`什么时候到来？你可以猜猜要把它变成 Dragula 的适配器需要什么....

* * *

当您将您的库硬连接到特定的技术或框架时，您限制了它的潜在影响。通过提前思考并投入*一点点*更多的努力，<mark>你的图书馆可以让更多的人受益</mark>。甚至可能是*未来的你*，一旦你进入`TECHNOLOGY_X++`...

> 注:*阅读原文[关于 changelog.com](https://changelog.com/posts/stop-limiting-your-open-source-librarys-potential)T3】*