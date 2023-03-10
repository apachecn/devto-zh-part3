# 当我浏览脸书的位置数据时，我发现了什么

> 原文：<https://dev.to/haseebelaahi/what-i-found-when-i-went-through-my-facebook-location-data-89j>

我的 facebook 位置数据和结果的一些可视化和统计，你可以说不是很令人愉快。

[![](img/139d476e68a5664b97f847ca10d6afab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S2TwPhsP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AF3Qw3lQbqcTK5xP64F_dcw.jpeg) 

<figcaption>图片来源:[www.cpomagazine.com](http://www.cpomagazine.com)CPO 杂志</figcaption>

一段时间以来，脸书一直允许用户下载他们的数据，所以一个奇怪的晚上，我下载了脸书所有的我的位置数据。

[![](img/f3d35d33c98ece150e62f489a2f67bb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F_o_3Sc_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtkbyxMYY69-yfg3rVlc6bQ.png) 

<figcaption>截屏:脸书，下载你的信息页面</figcaption>

脸书允许你以浏览器可浏览的 HTML 格式下载数据，但这种浏览方式并不有趣，也没有透露太多信息(脸书可能希望如此)🤷‍♂️).HTML 视图只是给你一个无聊的坐标列表，就像这样。

[![](img/3f7974dcca497af843c6266979686a10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tD3vNwn2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsoQh7xPvDrB0k_uONdzFkA.png) 

<figcaption>截图:脸书用户位置数据</figcaption>

我下载了 JSON 格式的数据，这是另一种受支持的格式，并做了一些基本的统计和可视化。

*我从 2011 年开始使用脸书，但三四年后才开始在手机上使用脸书应用。这里的所有统计数据和可视化数据都来自 2015 年 1 月至 2019 年 4 月的位置数据。*

### **脸书存储我位置的次数**

从 2015 年*开始到 2019 年 4 月*期间，脸书访问和存储我的位置总共 **2818** 次，平均每年几乎 **650 次**，每月 **54 次**🙀或者**每天两次**😮。当你想一想这些数字的时候，感觉就像是一直被跟踪一样！

### 按年份存放地点

脸书的位置收集似乎与你的位置变化频率有关，显然打开手机定位是一个先决条件。此图显示了每年位置条目的趋势。

[![](img/3933fe15485264bc6079eb80d4e0408f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hOm_r0bu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAYEVAoQwzEWa583w2c7ODQ.png) 

<figcaption>我的位置数据按年份在脸书</figcaption>

2016 年至 2017 年数据收集量激增的原因可能是脸书的位置收集政策发生了一些变化，或者可能是我在 2017 年刚买了一部好的智能手机，并开始在我的安卓智能手机上一直打开位置。

### **最繁忙地点集合天数**

深入挖掘数据，我继续过滤出位置收集频率最高的十天。你能猜到我的位置在任何一天最多被存储在脸书上多少次吗？

50, 100?不，**二百三十八**次！

[![](img/d31de34f43957baee2a2bfa70447abea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Du0zZ1iy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeXbaCvvNeb6LhsaQuB6q8w.png) 

<figcaption>前 10 名最高位置收藏天数</figcaption>

原来那天我在旅行，一天的大部分时间都在路上，脸书像任何担心的父母一样，查看了我 **238** 次…

### **所有这些数据在地图上会是什么样子？**

很自然地，我想把所有这些纬度和经度数据放在一张实际的地图上，以直观地显示这些年来我一直在哪里走动！

[![](img/d153d5fcadb6a2fc2457792e5a002d9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8eYdAaFq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArrsvxcDsEtK1h0kK359tFA.png) 

<figcaption>我在家乡地图上的位置数据来自脸书</figcaption>

以上是我的位置数据的可视化表示，脸书几乎在我家乡(PK 的拉合尔)房子的 15 公里半径内。每个蓝点代表一个由脸书捕获和存储的位置实例。

对于这里标注的一些地点，我甚至不记得去过那个地方，但感谢脸书提供了确凿的证据，让我回忆起过去 4 年里我去过的所有地方🙃

有了位置数据、每个位置捕捉的时间和脸书的资源，很容易推断出任何脸书应用程序用户的家庭地址、工作场所、他们周末经常去哪里、他们在哪个餐馆吃饭等等。 ***所有这些无疑使定向广告变得更加容易，当然也使出于政治和社会目的对特定用户角色进行分类变得更加容易*** 。

考虑到最近关于脸书推出他们自己的约会服务的猜测，这些数据可能会在*中有更多的用途👫*把人们聚集在一起！**

 *所以，下次当你遇到某人，而下一件事是他开始出现在你的“*推荐好友*”列表中时，不要感到惊讶😆

你可以在 Kaggle 上找到我的脸书数据的详细分析

[https://www.kaggle.com/helahi/facebook-messages-analysis](https://www.kaggle.com/helahi/facebook-messages-analysis)

感谢您的参与！如果你认为我应该挖掘更多的数据来发现任何其他的统计数据或模式，请在评论中告诉我！*