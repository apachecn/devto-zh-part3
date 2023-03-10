# HTTP 简介(特别是针对数据人员和管理人员！)

> 原文：<https://dev.to/katiekodes/intro-to-http-for-data-folks-managers-especially-24n6>

HTTP 很容易在五分钟内学会，如果你想使用“API”在互联网上集成系统间的数据，熟悉是必不可少的。

本帖会给你一个 HTTP 的入门基础。

对于某些工作*(例如，了解到所有酷孩子都说“URI”，而不是“网址”，以及原因)*，你需要更深入地了解，但如果你是业务分析师、数据集成商或经理，这应该是恰当的详细程度。

* * *

[![](img/1563a49a4a69cbcbd01daea6f975bb8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jrk_z5eh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/dictionary-wide.jpg)

## 定义

HTTP(s) 代表“超文本传输协议”
*(我加了“(s)”来提醒你使用好**安全**的做法。)*

**协议**是关键词:我们立刻知道我们在谈论一个*标准*，当*与*互动时*如何表现*。

在这种情况下，这是两台*计算机*通过“网络”相互通信时应该如何相互通信的标准

* * *

[![](img/43e9fe7611a0c784b9a69d31744ea4d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WB16km78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/clientserver-wide.png)

## HTTP(s)是不平衡的

关于这个协议最重要的一点是，尽管两台计算机之间可以双向通信，但每次交互都是从其中一台计算机发起的。

这台计算机被称为“web 客户端”，它使用 HTTP(s)协议发送的消息被称为“**请求**”

另一台计算机称为“web 服务器”当它回复 HTTP(s)请求时，它使用 HTTP(s)协议发送的消息被称为“**响应**”

就像[通过“API”](https://dev.to/katiekodes/salesforce-rest-apis-a-high-level-primer-2i9h)沟通一样，这是一种“敲开设防城堡的门”式的沟通。网络**服务器**是城堡围墙内的*电脑。*

* * *

[![](img/a48328862f68eb12d8d368988b0fbd5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8AeH3NBs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/cashregister-wide.jpg)

## 你是当事人

虽然学习构建一个 web 服务器并对其进行编程以在收到 HTTP(s)请求时发出 HTTP(s)响应很有趣，但本教程并不涉及这一点。本教程是关于使用你的电脑作为网络 ***客户端*** 。

这意味着我们将关注**发送请求**和**接收响应**。

像所有的计算机到计算机的通信一样，实际的信息是一堆 0 和 1。幸运的是，你不需要写那些！

对于我们人类来说，编写一个好的 HTTP(s)“请求”意味着**指定简单的细节**，比如“地址”、“报头”等等。

实际上，通过互联网发送请求可以委托给专门的软件，如:

*   你的网络浏览器*(火狐、Safari、Chrome 等。)*
*   专门的点击式桌面软件，如 [Postman](https://www.getpostman.com/)
*   专门的命令行桌面软件，如 [Curl](https://en.wikipedia.org/wiki/CURL)
*   像 Python 的[请求](https://www.edureka.co/blog/python-requests-tutorial/)或 Salesforce Apex 中的 [HTTPRequest](https://trailhead.salesforce.com/en/content/learn/modules/apex_integration_services/apex_integration_rest_callouts) 这样的编程语言及其库。

* * *

[![](img/b98e470919bdda75743c66e179971b4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LEbOFKjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/catbegging-wide.jpg)

## 请求

那么，是什么使得 HTTP(s)请求“在正确的协议内？”

很高兴你问了！

### 1 -网址

一个请求必须以明文的形式指定一个地址，指明它想要敲哪个城堡的门。示例:

*   `https://www.google.com`
*   `https://google.com/webhp`

注意:当讨论托管在 web 服务器上的“API”时，您经常会听到 URL 被称为 API 的给定“端点”，对于 URL 中问号 *( `?` )* 之后的任何部分，URL 被称为“传递”给 API 的“参数”。

对于 HTTP(s)本身来说，这并不是什么真正需要了解的东西，但是您将会听到人们谈论它，就好像它是一样。

一个很好的经验法则:**当你听到**、**端点**或**参数**、**时，认为**是 **URL** 的一部分

### 2 -法

一个请求必须用一个简单的文本关键字，简要地说明它在敲 web 服务器的城堡门时的目的是什么。

我建议[阅读更多关于允许的关键词](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)的信息，但是目前最常见的“方法”是关键词`GET`和`POST`

### 3 -表头

允许请求以结构良好的纯文本形式提供关于其自身的一些摘要细节，如:

*   代表什么样的网络客户端 *( `user-agent` )*
*   如果在请求“正文” *( `content-type` )* 中包含任何内容，它将使用什么“格式标准”

**例 1:** `content-type: application/json;`的意思是:

> "正文的内容是明文——更确切地说，是使用 [JSON 标准](https://dev.to/katiekodes/intro-to-xml-and-json-6-json-30h3)格式化的."

**例 2:** `content-type: image/gif`的意思是:

> "正文的内容是一个 GIF 图像."

### 4 -体

在 URL、方法和头之后，请求可以说它想说的任何事情。这被认为是 HTTP(s)请求的“主体”。

显然，如果 web 客户端希望 web 服务器理解主体的内容，它应该在这里放置有用的、*、结构良好的*文本或[二进制数据。](https://en.wikipedia.org/wiki/Binary_file)

如果在请求头中提供类似于“`content-type`”的有用信息，以确保服务器理解如何解释请求体的内容，这也有助于促进通信。

* * *

[![](img/6cae7ccfc71d81427e1d28346d72c995.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PCGbb3kV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/cateating-wide.jpg)

## 回应

HTTP(s)响应*也*遵循特定的协议。

下面是我们可以期待它包含的内容:

### 1 -状态

响应必须指定一个 3 位数的数字和相应的英语短语，简要说明它对收到的请求的看法。

您可以在此处[读取所有选项，但常见的状态代码有:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

*   200 好吧
*   401 未经授权
*   404 未找到

### 2 -表头

允许响应以结构良好的纯文本形式提供一些它认为 web 客户端可能会喜欢的关于它自身的总结性细节，例如:

*   当响应被发送时 *( `date` )*
*   如果它在响应“正文” *( `content-type` )* 中包含任何内容，它将使用什么“格式标准”

### 3 -体

在 status 和 header 之后，一个响应可以说任何它想说的东西。这被认为是 HTTP(s)响应的“主体”。

**说服 web 服务器发送一个包含有用内容的响应通常被认为是发出 HTTP(s)请求的*目的*:**

*   如果你向[https://google.com](https://google.com)发出请求，你可能*希望*响应体将充满 HTML、CSS 和 JavaScript 代码，你的网络浏览器可以用它们在你的屏幕上绘制搜索框和[涂鸦](https://en.wikipedia.org/wiki/Google_Doodle)。
*   如果您向一个以[https://pi.pardot.com/api](http://developer.pardot.com/kb/api-version-3/prospects/)开头的 URL 发出请求，您可能*希望*响应主体将填充来自您控制的 Salesforce Pardot 数据库的记录*(例如“潜在客户”)*的信息。

有时“有用的信息”意味着“详细阐述响应状态的附加细节”当状态代码表明服务器不喜欢您的请求时，这非常有用——例如，“`401`”或“`404`”状态。

* * *

[![](img/9347cbca9ec2e4d7d00b580c3e6a5566.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--orTprtYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/devildetails-wide.jpg)

## 细节中的魔鬼

关于 HTTP(s)本身就没什么好说的了。在不到 5 分钟的时间里，你已经学会了开始使用它所需要知道的一切。

[![Celebration GIF](img/e5939d19e23b1eea9f690d6f63612f87.png)](https://i.giphy.com/media/QpOZPQQ2wbjOM/giphy.gif)

这确实是一个简单的协议。

**然而，你*如何处理*HTTP(s)请求*关系到*。**

> 这就像现实世界中有不同层次的“协议”一样。
> 
> 当你遇到一家公司的首席执行官时，礼仪是和她握手并打招呼。
> 
> 如果你在问候时用侮辱性的名字称呼她，你肯定违反了*附加*礼节，首席执行官的回应可能会有相应的结构！
> 
> 但是从技术上来说，你并没有违反“握手并说点什么”的协议。

让 ***有效*** 使用 HTTP(s)请求需要研究*附加的*协议，这些协议由您可能想要与之通信的每个服务器定义。

当这些协议告诉你*在你的请求中放入什么*来引发服务器的特定行为时，它们被称为“应用编程接口”或“ **API**

[![mercier_remi image](img/d1aebfa0daeeb744f862f8d1be6fa264.png)](/mercier_remi) [## 什么是 API？

### 雷米梅塞尔 5 月 23 日 195 分钟阅读

#api #beginners #explainlikeimfive](/mercier_remi/what-is-an-api-4ao9)

请继续关注动手实践的无代码教程系列，它将让您将新学到的 HTTP(s)知识应用于各种 API。

* * *

### 请求 URL:一个令人困惑的细节

现在可能值得一提的是，有时，URL 中的细微变化会被 web 服务器处理为*，就好像它们都指向同一个地址，而您指定的 URL 的结尾会被处理为“您想要告诉城堡守卫的额外数据”，而不是“城堡位置”的一部分*

示例:

*   对于[https://google.com/search?q=abc](https://google.com/search?q=abc)，你可以争辩说`https://google.com`的警卫认为“细节”是`/search?q=abc`在问，“请在互联网上搜索`abc`”
*   对于[https://google.com/search?q=XYZ](https://google.com/search?q=XYZ)，你可以争辩说`https://google.com`的警卫认为“细节”是`/search?q=XYZ`在问，“请在互联网上搜索`XYZ`”

一些 web 服务器有意提供这种对请求 URL 的“分割处理”,作为其 API 的一部分。

> *(这正是可能使 API 文档引用“**端点**”和“**参数**”的情况。))*

当一个 API 不需要来自最终用户的大量信息时，让用户将他们所有的**数据**作为 URL 的**变体进行交流，比强迫用户在请求中添加一个**主体**更“方便”。**

* * *

### 网页 vs. API 端点

也许你已经注意到，我轻率地对待以下两类 URL，好像它们是可以互换的:

1.  URL 通常被认为是“网页”，例如[https://google.com](https://google.com)
2.  URL 通常被认为是“数据集成 API 端点”，比如[https://yesno.wtf/api](https://yesno.wtf/api)

那不是意外。

这可能让你现在有点难以接受，但是把它藏在某个地方以备将来参考:

我认为“网页”只是一种特殊的“API 端点”
*(不过，我们通常用“API 端点”来表示“除了网页以外的一切”)*

听我说完。我早些时候说过:

> *   A single web server's rules about *what* to put into the HTTP(s) request and *what to expect* in the HTTP(s) response of a given URL can be regarded as an "API". Web servers usually regard its valid URL as an "API endpoint"

**如果**谷歌的网络服务器对`https://google.com`的规则如下...

> “每当你用一个方法请求这个 URL 时，我保证这个响应体将会是 HTML，你的浏览器可以用它在你的屏幕上绘制我们的‘主页’

...**那么**那不就是像其他“API”规则一样的“如何请求以及期望得到什么响应”的*规则*吗？

[![Unsure GIF](img/0d6ea7bd158d4cbcf2ae02f29e8bd7ee.png)](https://i.giphy.com/media/fnmo7jLamKpqno5wuK/giphy.gif)

在我看来，被视为“网页”的 URL 和被视为“API 端点”的 URL 之间的主要区别在于:

*   “网页”有简单和一致的“API”规则，关于什么属于 HTTP(s)请求*(只需提供 URL 你的网络浏览器将填写所有其他内容)*和回复*(这将是“网络浏览器可以美化的东西”)*。
*   由 web 服务器托管的所有其他东西——所有规则复杂到足以让还没有像您现在这样“从头开始”学习 HTTP(s)请求的人感到困惑的东西——就是人们喜欢称之为“API 端点”的东西。

[![Shrugging GIF](img/29b281966d42cce5282994803935fbd0.png)](https://i.giphy.com/media/l0IsHb1T9agseRdgQ/giphy.gif)

* * *

[![](img/d69bb13b45df9613ae3e60bcf9b8b335.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V78VIA4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/keys_jumbled-wide.jpg)

## 安全

我之前提到过 HTTP(s)中的“S”代表 **S** ecure。

### 规则 1

如果 URL 以`http://`而不是`https://`开头，千万不要在 HTTP 请求中将*重要的*数据发送到 web 服务器。

这就像邮寄你的护照号码在明信片上，而不是在信封里。

每个帮助递送明信片的人都可以阅读和/或重写其内容。

类似地，如果你希望从*返回*的数据很重要，千万不要向 URL 以`http://`而不是`https://`开头的 web 服务器发送 HTTP 响应，因为响应也会以“明信片”的形式到达

### 规则二

不要因为你自己在网址上加了一个“s”就相信这个网址。

您应该总是对任何以`https://`开头的 URL 进行测试请求，而不发送任何重要的数据，这样您就可以检查 web 服务器的响应，并确保“安全的”通信确实在工作。

*   在你的日常生活中，使用网络浏览器，这意味着，例如，访问一个网站的主页，并确保你的浏览器在 URL 旁边显示一个挂锁。
*   在像 Postman 或 Curl 这样的工具或编程语言中，要使您的计算机表现得像“web 客户端”，您必须使用该工具和/或阅读其文档，以找出等效的适当验证步骤。
    *   通常，您的工具会向您显示一个错误，说它甚至没有收到来自 web 服务器的响应，或者它认为“安全证书”有问题，因此验证“`https://`”是否按预期工作可能是非常明显的，甚至在代码中也是如此。

### 规则三

不要发送重要的数据，即使是“装在信封里的”*(通过 HTTP* *S* *)* ，也不要将这些数据发送给你不信任其所有者的网络服务器。

您不会将您的纳税申报表邮寄给我，并且您不应该将您的 Salesforce 密码包含在对任何 URL 的 HTTPS 请求中，除非您知道该 URL 属于 Salesforce。

* * *

[![](img/15b3518feb8181bb6cc92f483aaee8cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X-Ig0rYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/booksmany.jpg)

## 进一步阅读

*   不太赞同我解释 HTTP(s)的方式？尝试代码学院中的[概述](https://www.codecademy.com/articles/http-requests)
*   Mozilla Foundation 的[常见的有效 HTTP(s)请求方法以及使用每种方法的原因](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
*   [常见的有效 HTTP(s)响应状态以及服务器选择它们的原因](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)Mozilla Foundation
*   [在 HTTP(s)请求和响应头中包含“概要细节”的常用关键字](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)由 Mozilla 基金会*(警告-很长)*

* * *

[![](img/9e9a42350b141ae6e94235b07ed69cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdD4DMbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/shoppingbags.jpg)

## 外卖

1.  HTTP(s)是一个非常简单的协议，它让一台充当 *web 客户端*的计算机向 *web 服务器*发出一个格式良好的**信息请求**，后者将依次发送一个格式良好的**响应**。
2.  一个格式良好的 HTTP(s)请求有一个 **URL** ，一个**方法**，可能有一个**头**，也可能有一个**体**。
3.  一个格式良好的 HTTP(s)响应有一个**状态**，可能有一个**头**，也可能有一个**体**。
4.  " S "代表"安全"遵循良好的 HTTP(s)卫生。
5.  通过与通过“API”提供数据的 web 服务器对话，练习“从头”编写自己的 HTTP(s)请求是最简单的方法敬请关注弄脏你的手！