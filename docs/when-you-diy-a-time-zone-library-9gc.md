# 当您 DIY 时区库时

> 原文：<https://dev.to/turnerj/when-you-diy-a-time-zone-library-9gc>

*[封面图片由乔恩·泰森在 Unsplash](https://unsplash.com/photos/FlHdnPO6dlw)*

您是一名经验丰富的开发人员，从事日历应用程序的开发，允许用户将事件添加到他们的日历中，包括时间和位置。一个新的 JIRA 问题来了，你让用户知道什么时候有 X 的时间量在事件开始之前。为了准确地处理可能在不同地区的事件，您需要考虑时区。

作为一名经验丰富的开发人员，你知道必须有一个按国家划分的时区列表，你所需要的就是这个列表，并计算时区之间的差异。当你找到[列表](https://en.wikipedia.org/wiki/List_of_time_zones_by_country)时，你会意识到这比许多国家有两个或更多的时区要复杂一些，但没有汗水，你有活动的位置信息和当前用户所在的时区。您找到了[更详细的时区列表](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)，并继续您的开发之旅。

大多数国家的时区通常以 30 分钟为增量，只有尼泊尔例外，该国自 1986 年以来一直处于+05:45 的时差。不过这不是问题，您不会将您的系统限制为只保存 30 分钟增量的数据——您可能已经让它能够支持单独的分钟增量。

你可能还记得一年发生两次的[怪事](https://en.wikipedia.org/wiki/Daylight_saving_time)，它根据一年中的时间和你所在的半球将时间向后或向前拨一小时。这开始使事情变得复杂，因为现在你需要有关于[所有遵循夏令时的国家的数据，以及他们在一年中不同时间的走向](https://en.wikipedia.org/wiki/Daylight_saving_time_by_country)。我有没有提到每个人的夏令时不是在同一天？

希望你现在已经意识到，将这些数据输入到你的代码或数据库中会花费太长的时间，所以你正在寻找一些可以导入到你的代码中的东西，你知道，*如果*数据发生了变化。你发现互联网号码分配机构(IANA)有这样一个[数据库，你可以下载](https://www.iana.org/time-zones)。最棒的是，它还包含 DST 信息！看起来你的运气变了，这仍然很容易。

不幸的是，你也意识到这个数据库最近才被修改，并且有一个[“公告”邮件列表](https://mm.icann.org/mailman/listinfo/tz-announce)，这对于一次性的导入来说不是一个好兆头。不过没问题，这是一个 CRON 作业，调用事物方案中的导入器脚本。

你要在这里调用它，说这已经足够好了。你不想担心政治时区问题，你不需要担心历史时区问题，你肯定不需要担心星际时区问题。你整理好剩下的代码，解决 JIRA 问题，然后继续你的快乐之路。

至少您的项目不需要根据时区计算两个时间点之间的秒数...

* * *

这篇文章的灵感来自于[汤姆·斯科特](https://twitter.com/tomscott)制作的一个关于时区的奇妙的[电脑爱好者视频。他的例子深入到一些更本质的问题，如历史时间或闰秒的问题，如果这篇文章有点意思，我强烈建议你看一看。](https://www.youtube.com/watch?v=-5wpm-gesOY)