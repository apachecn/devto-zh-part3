# “Our.Umbraco.*”包装名称的历史

> 原文：<https://dev.to/leekelleher/history-of-our-umbraco-package-names-34f2>

回到 2017 年初，[卡勒姆问了这个问题](https://twitter.com/callumbwhyte/status/820641163334053889) …

> 有人能解释一下“我们的”背后的历史吗？Umbraco.X "包的命名约定？现在似乎只有少数人使用#umbraco

我[在时间](https://twitter.com/leekelleher/status/820727834536767488)回复了——但是觉得一个比几条推文更长的解释会更好，(并且可以找到供将来参考)。我写了这篇文章的草稿已经有一段时间了，我认为最好还是发表它吧！

[![Umbraco community hacking](img/538eb64bfbd7ad0aab2fa1ebc7865a19.png)](https://www.flickr.com/photos/rikhelsen/6269167834/in/album-72157627950157764/)

早在 2007 年末，当我开始与 Umbraco 合作时，我的公司叫做 **Bodenko** 。当我们开发任何后台功能时，它们最终都被捆绑到一个名为**的项目中。这很快成为我们所有扩展的垃圾场。**

在更多地参与到社区中，在论坛上问了很多问题——并开始回答其中的一些问题后，我认为我应该真正开源我们在 Bodenko 开发的包。我们的第一个是 [**Robots.txt 编辑器**](https://our.umbraco.com/packages/developer-tools/robotstxt-editor/) (令人惊讶的是，它今天仍然很受欢迎——并且对原始代码库的更新很少)……我将该程序集命名为**“博登科。Umbraco.RobotsTxtEditor"** 。

2009 年，我与博登科分道扬镳，成为一名独立的自由职业者，我们解散了公司。大约在这个时候，我参加了我的第一个 code garden——他们在那里推出了新的论坛… **我们的 umbra co**——这是社区内一个非常令人兴奋的时刻。Robots.txt 编辑器项目应该发布一个补丁(错误修复的汇总)，但当我查看代码时，我觉得我以前的公司名称与软件包本身没有什么关系。使用这个软件包来“宣传”我们的老公司和服务没有多大意义。所以，我想改变它。

我的自由职业工作的公司名字叫做 **Vertino** ，我可以把它换成那个名字，但那感觉也不太对。大约在同一时间，我注意到有一些其他新发布的包包含了开发它们的公司的名称/参考，这有点儿感觉有点儿*反社区*。

我没有尝试为我的代码命名空间设计一个新的基础部分，而是将**“bo denko”**与**“我们的”**进行了交换。我仍然不能确定这是一个聪明的想法还是我在偷懒- *可能是后者*。

我从来没有设想这是试图开始一个约定，然后当我参与到其他已建立的 Umbraco 包中时，例如[配置树](https://our.umbraco.com/packages/developer-tools/config-tree/)和[谷歌地图数据类型](https://our.umbraco.com/packages/backoffice-extensions/google-maps-datatype/)，在他们的开发者的同意下，随着时间的推移，我们重新命名了名称空间和程序集。

具有讽刺意味的是，我们从未更改过 [uComponents](https://our.umbraco.com/packages/backoffice-extensions/ucomponents/) 名称空间或程序集——考虑到当时那个项目被认为是*事实上的* Umbraco 社区项目。

想法是这些包是由 Umbraco 社区为 Umbraco 社区制作的。

我没有推动这个命名约定，但是有趣的是我注意到其他的包开发者已经开始使用它了。这已经成了一条不言而喻的准则。

最近，我和 Matt Brailsford 合作的包一般都是在我的公司**Umbrella**(Matt 是我们的自由撰稿人)一起工作的结果。然而，当我们开始编码和设置名称空间时，如果我们使用 **"Umbrella.Umbraco.*"** ，感觉就不太对了。我们从一开始就知道某些后台扩展有可能作为成熟的 Umbraco 包发布——我们总是抱着这种心态开始… **“为了社区”**。

因此，如果你曾经在你的网站的`/bin`文件夹中看到一堆“Our.Umbraco.*”程序集，那么你现在知道它们是在考虑社区的情况下开发的，并且是深思熟虑的、精心制作的 Umbraco 包的标志。