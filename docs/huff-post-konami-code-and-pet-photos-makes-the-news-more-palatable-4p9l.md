# 赫夫邮报，Konami 代码和宠物照片使新闻更可口

> 原文：<https://dev.to/georgemandis/huff-post-konami-code-and-pet-photos-makes-the-news-more-palatable-4p9l>

我总是在网上关注我的公众形象。有时候你会发现令人惊讶的东西。你可能会因为你 18 岁时写的一篇大学论文而拥有自己的谷歌学术主页，或者在组织者联系你之前很久就发现你被安排在另一个国家的会议上发言。

今天我发现我的一个老项目正在被《赫芬顿邮报》使用，或者我应该说，是《垃圾邮报》？

[![FluffPost](img/6b04e520a61e591b7e4c06b7b346b07f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--agxsvUWk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonbase.georgemandis.com/huffpost-konami/fluffpost-5.jpg)

去看看！如果你进入[赫芬顿邮报的主页](https://huffpost.com)并输入 [Konami 代码](https://en.wikipedia.org/wiki/Konami_Code)，所有的图片都会变成人们宠物的可爱照片。

这几年对于快乐、振奋人心的新闻来说并不是最好的几年，但是看到这些头条新闻伴随着可爱的狗和猫让它变得更加可口。相关案例:

[![Feisty Mnuchin](img/24af4369d75f9ef83b118eea273940a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FAbkMXi_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonbase.georgemandis.com/huffpost-konami/fluffpost-3.jpg)

姆努钦看起来很活跃。巴尔正在组建的团队看起来很重要。我相信他们会把事情查个水落石出。

## 我是怎么找到这个的？

我无意中发现了这一点，不是通过谷歌搜索，而是通过在 GitHub 上其他人的代码库中搜索我的名字。这可能看起来有点奇怪，但是如果你已经创建或贡献了许多开源项目，这将是一个有趣的视角，人们可能会如何实际使用你所做的东西。

这也是一个很好的方法来查看你交给另一个开发机构的私有存储库是否意外地进入了公共领域...但那是另一回事了！

在寻找我的名字时，我看到了我的一个项目的参考资料，看起来像是从 Huff Post 主页上刮下来的代码。代码包含关于许可、版本和署名信息的评论，这是帮助我找到它的原因。

我决定去哈夫后，并尝试代码。当我输入 Konami 代码并看到宠物时，我笑了，当我去查看页面的源代码时，我很高兴看到我的名字和代码就在那里。

## 关于项目

Konami-JS 是我在 2009 年做的一个开源复活节彩蛋项目。当访问者输入 Konami 代码时，你可以在任何网站上添加一个复活节彩蛋。当时，与众不同的是，我的项目是在移动设备上进行的。

我发现的大多数是人们把 Konami Code 复活节彩蛋放在他们的 GitHub 页面、博客和其他个人项目上。偶尔它会出现在更大的地方，如 Marvel.com 或新闻周刊。我最近发现，早在 2012 年，它甚至被用于特斯拉的在线设计工具上——我在 archive.org[这里](https://web.archive.org/web/20130408184419js_/http://www.teslamotors.com/sites/all/themes/tesla/configurator/js/libs/konami.1.3.3.pack.js?c)找到了它的残余。我想现在我可以把 Huff Post 添加到列表中了！

按照现代 JavaScript 标准，这种脚本有点过时，但它生活在一种灰色地带，我不确定对它进行现代化会有什么好处。代码占用的空间很小，但是很容易理解，很容易通过快速的复制和粘贴就可以放到任何现有的项目中。初学者可以并且确实一直使用我的代码。

由于进入 web 开发的障碍似乎每年都在增加，我很高兴贡献了一个被新手和高流量网站使用的项目。我也很高兴，这是一个快乐的，琐碎的贡献，主要用于使网络成为一个有趣和愚蠢的地方。

观看我在 2017 年 [OdessaJS](https://odessajs.org) 上讲述我维护这个“琐碎但受欢迎”的项目的经历:

[https://www.youtube.com/watch?v=F3xI3ps7syI](https://www.youtube.com/watch?v=F3xI3ps7syI)

最后，我*确实*在 GitHub 上有关于 Konami-JS 2.0 十年后可能/应该是什么样子的公开问题和讨论[。欢迎您的贡献。](https://github.com/snaptortoise/konami-js)