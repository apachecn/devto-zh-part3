# 将日期保存在数据库中可以节省您的时间

> 原文：<https://dev.to/saulsilver/saving-dates-on-database-saves-your-day-5cb5>

前阵子要给一个 web app 加打折系统。折扣期为一周，开始时间因用户而异。稍后，我们要过滤那些有折扣的用户和那些没有折扣的用户。

这个任务看起来很简单，很快就决定使用下面的方法。

1.  在 DB (MongoDb)上的用户文档中创建一个折扣`date`字段
2.  一周后，将折扣日期字段替换为设置为`false`的`boolean`。

将一个`date`字段替换为`boolean`的原因是为了给自己节省一些代码行，避免日期比较，日期比较有时很难比较，尤其是在多个时区的情况下。所以我开始实施，完成，测试，然后部署到生产中。

几天后，我发现从折扣系统中获取更多的数据会很有用。想象一下，只要知道用户的折扣是何时产生和结束的，所有关于折扣随着时间的推移对用户数量的影响有多好/多坏的信息。问题是我只有开始日期，直到折扣结束，然后开始和结束日期都不再可用。多么悲伤..

所以我很快就换了一个更好的设计，划掉第二个数字，加上
开始和结束日期。让代码长一点，但是现在我得到了
更可靠更具体的数据。

## 吸取教训

1.  数据库中的事件日期让您对数据有了更多的了解
2.  如果没有足够的信息，生产级别的故障排除可能会很棘手
3.  给你足够的时间来设计你的数据库字段和数据如何流动。