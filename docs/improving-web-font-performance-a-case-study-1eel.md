# 提高 web 字体性能:案例研究

> 原文：<https://dev.to/armelpingault/improving-web-font-performance-a-case-study-1eel>

2018 年 11 月 8 日和 9 日，我有机会参加了第一期 [performance.now()大会](https://perfnow.nl/)，聚焦当今最重要的 web 性能洞察。在[的十六个伟大演讲](https://perfnow.nl/speakers)中，我从扎克·莱泽曼关于网络字体性能的演讲中吸取了很多，我鼓励你们观看[的完整演讲](https://www.youtube.com/watch?v=FbguhX3n3Uc)。

在 Netcentric，我是一名前端软件工程师和 web 性能监护者，我可以立即看到我可以开始尝试改进 web 字体实现的一些技巧的领域。我将举例说明对优化 web 字体性能有用的几点分析。

# 洞察优化网页字体性能

**设置**

我主要使用 macOS 10 和 Chrome 70 进行分析，并总是将网络节流到快速 3G 和 CPU 4x 减速。

## 相似的字体

让我们先简单地加载网站的主页，看看哪些文件正在被加载:

[![Network tab](img/8390bd182b51c6065eecb85591439d8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--StmqlblM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/abeotpfrvfiwr537fsu9.png)

我们正在加载 5 个字体文件。ttf 和 CAC-Regular 不能碰，因为它们非常具体，但我注意到我们有 3 种字体的名称非常相似:

*   CS-Light.woff2
*   CS-Regular.woff2
*   CS-Demi.woff2

我想知道它们到底有多大的不同，以及我们是否真的需要所有的字体，所以我决定通过将一种字体放在另一种字体上来直观地比较它们。我选择使用 CS-Light 字体作为基础字体，因为它似乎更容易与其他字体匹配。在我们的例子中，给 CS-Regular 字体设置一个较轻的字体粗细没有任何区别，所以我只能应用一个较粗的字体粗细。

所以，我有绿色的 CS-常规字体，我想替换的那个，和红色的 CS-Light，它们并不真正匹配。

<figure>

[![We obviously have 2 different fonts](img/8ae6a1b3c6da11ad6259883cb8cbe061.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPuHwwvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogd4t59etq43b4c4gdrg.png)

<figcaption>We obviously have 2 different fonts</figcaption>

</figure>

这里的第一个词看起来还可以接受，但接下来就变得相当疯狂了。然而，我可以看到一些相似之处，所以我试图通过使用一些 CSS 来获得更接近的匹配: