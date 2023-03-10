# 实际使用 PowerBI 的新数据连接器

> 原文：<https://dev.to/cskardon/actually-using-the-new-dataconnector-for-powerbi-2k1g>

写完之后，我意识到我的上一篇文章对于那些不关心如何使用它，但是想知道如何使用它的人来说可能不是最有用的。因此，这将遵循与上一篇文章相同的处理方式(一年半以前！！哇！).

视频版本如下，如果你想要的话:

## 设置步骤

首先——我们必须安装 power bi——现在，我没有注册账户，而是从 [PowerBI 网站](https://powerbi.microsoft.com/en-us/get-started/)下载的，安装简单快捷。

我们还需要运行 Neo4j，您可以使用 Community *或* Enterprise，这并不重要——我们希望将“电影”数据集放在那里，因此运行您的实例，并执行:

```
:play movies 
```

Enter fullscreen mode Exit fullscreen mode

## 给 Power BI 添加数据连接器

1.  首先——从 releases 页面下载连接器(或者在 VS 中自己构建)——您需要`Neo4j.mez`文件。

版本 1:[https://github . com/cskardon/neo 4 jdataconnectorforpowerbi/releases/tag/1 . 0 . 0](https://github.com/cskardon/Neo4jDataConnectorForPowerBi/releases/tag/1.0.0)

1.  PowerBI 在`<USER>\Documents\Power BI Desktop\Custom Connectors`文件夹中寻找定制连接器，如果它不存在，您需要创建一个。一旦你有了那个文件夹，复制那里的`Neo4j.mez`连接器。

[![image](img/91f9e2cec0903ecd6c8a8982eb19eacc.png)](https://i0.wp.com/www.xclave.co.uk/wp-content/uploads/2019/02/image-2.png)

1.  差不多了——我们现在只需要允许 PowerBI 加载连接器——因此，启动 PowerBI 并进入选项对话框:

[![image](img/71c4be3b66e639b0d5b8d73daa58801e.png)](https://i0.wp.com/www.xclave.co.uk/wp-content/uploads/2019/02/image-3.png)

在那里，选择“安全”选项，然后在数据扩展标题下选择允许您加载任何扩展而无需验证或警告的选项:

[![image](img/71054cc46ef469866ccc4223f335e350.png)](https://i1.wp.com/www.xclave.co.uk/wp-content/uploads/2019/02/image-4.png)

您必须重新启动 PowerBI 才能获得连接器——所以现在就开始吧！

## 让我们获取一些数据！

现在——我知道你们中的很多人会对上一篇文章中的饼状图感到兴奋——现在你们可以创建自己的饼状图了！

随着 PowerBI 新实例的运行，我们选择“获取数据”

[![image](img/4c2d8944b9ac5022ec282bdec73bf902.png)](https://i2.wp.com/www.xclave.co.uk/wp-content/uploads/2019/02/image-5.png)

我们现在可以搜索“Neo4j”或查看“数据库”类型:

[![](img/e410f4fd518cd30ec6f627f1ca57570a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJISHrye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-6.png)

选择`Neo4j`，然后按下“连接”–AAAA 和警告！

[![](img/d18a3410bea4b84a406b2bf4240d2517.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EWsGp8XF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-7.png)

阅读它，忽略它——由你决定——但这只是为了让你知道它仍处于测试阶段(我的意思是它迄今为止只有一个版本！)如果你乐意的话继续。

现在，您可以在框中输入您的密码和连接信息——密码字段的文本框只有一行(唉)——因此，如果这是一个复杂的查询——您可能最好用 Sublime 或类似的工具(甚至可能是*记事本)来编写它！？！*)。在这种情况下，我们可以简化:

匹配(m:电影)返回 m；

现在，其他设置，如果你运行的是默认设置，你可以保留这些设置，但显然如果你需要连接到`https`而不是`http`，在`scheme`设置中更改它。我已经用默认值填充了我的显示:

[![](img/701ee7a4ceafc24be1155b6100293876.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0EAQltyE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-8.png)

当您按“确定”时，您会看到登录对话框，如果您是匿名连接，则选择“匿名”,否则填写您的用户名/密码。

[![](img/773700c3edc881e21d7a47c2b487d387.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bmRQOTsa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-9.png)

按“连接”，PowerBI 将连接到您的数据库，并返回给您一个“记录”列表:

[![](img/d4e1a7ce8652f9bf41ced7307c99e865.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sSXf9chH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-10-1024x774.png)

我们想要“编辑”它，所以请按“编辑”！

当超级查询编辑器打开时，按“m”列顶部的展开列按钮:

[![](img/fb95f8118116a6fea7ec799370a7d2a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LML90EAw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-12-1024x515.png)

哦，“标语”、“标题”和“发布”——我们的电影属性！为此，我将关闭“使用原始列名作为前缀”复选框，将它们全部选中，然后按“确定”。

[![](img/c8b09ddb67608845e9ca998bc54764f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ua5cjon1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-13-1024x347.png)

**数据！！**

现在，让我们“关闭并应用”我们的查询–注意–如果您查看“应用的步骤”部分，您可以看到我们只有 2 个步骤，“源”和“扩展的 m”

[![](img/93c35cc7ea8f86358650d4af76cb88cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ptKei7MZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-14.png)

在 PowerBI 应用它的同时，想想摆在我们面前的饼状图:

[![](img/4cef2287971afea2272f65ddb536abb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3zbt1G_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-15.png)

当对话框消失时，我们就可以开始了！在右侧，您会看到一个“字段”部分，您应该会看到类似这样的内容:

[![](img/8ef12381535c9240fd4ae95f45f14cc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7i9oJUHs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-16.png)

所以，我们期待已久的时刻…

## 咱们来饼状图

从可视化部分选择“饼图”:

[![](img/1ae533ac98fb9ee91133c64b3e750053.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WY_LnjmN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-17.png)

一旦它出现在您的显示屏上，选择它并将“已发布”字段从`Query1`拖到“详细信息”字段，然后将标题拖到“值”字段:

[![](img/db0d99f67d81db5dec747957acdb91d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hk9lnEkC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-18.png)

您的图表现在应该看起来像这样:

[![](img/34e7cc7a4c0d1ae6a2b53319b5023d0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TfO1TF6y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-19.png)

让我们最大化它的大小，将鼠标放在它上面，现在我们可以看到:

[![](img/ba1ddde652ca8cd84871a5ff8bb0e2a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---KjAQugd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-20.png)

但是我们也可以将“标题”拖到工具提示字段上，这样就可以得到该组中的第一部(或最后一部)电影:

[![](img/313298155ef50d39bc71ff9ba4056741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vhnlebM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xclave.co.uk/wp-content/uploads/2019/02/image-21.png)

## 一个查询看起来像什么？

我们有些人喜欢可视化，有些人喜欢代码，上次我们尝试这样做时，我们的查询有 20 行长，但我们的新查询只有 5 行！

```
let Source = Neo4j.ExecuteCypher("MATCH (m:Movie) RETURN m;", "http", "localhost", 7474), #"Expanded m" = Table.ExpandRecordColumn(Source, "m", {"tagline", "title", "released"}, {"tagline", "title", "released"})in #"Expanded m" 
```

Enter fullscreen mode Exit fullscreen mode

这样更好。

## 嘿嘿！是测试版！

数据连接器方法提供了一种更好的查询数据库的方式，它去掉了我们必须编写的大量代码，并有望使查询更容易。

但是——我不是 PowerBI 专家——这是正确的做法吗？有改进吗？我们应该有一些硬编码的查询？让我知道——做一个公关——一切都很好！