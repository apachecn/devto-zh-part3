# ⚙️解释硒和网络驱动程序自动化(像我五岁)

> 原文：<https://dev.to/uilicious/explain-selenium-webdrivers-like-i-m-five-16ng>

这最初是作为对这个问题的回答而起草的...

[![edwinthinks image](img/f95c7425a96a237d50294432ed895abb.png)](/edwinthinks) [## 解释 Selenium 和 Webdrivers，就像我五岁时一样

### 埃德温·马克 1911 年 5 月 12 日，阅读

#explainlikeimfive #selenium #e2e](/edwinthinks/explain-selenium-webdrivers-like-i-m-five-24me)

它自己成长为一个完整的帖子😅(我说的太多了)

因为我是[Uilicious.com](https://uilicious.com/)的联合创始人——一个基于 webdriver 服务器的完整的端到端测试平台。这篇文章是一系列我希望在“网络驱动者”学习之旅开始时就知道的关键事实。

* * *

**解释 Selenium / Webdriver 的一条线**

自动化标准，通过一个 API 控制你所有的网络浏览器。

* * *

**Webdriver 规范与 Selenium 实现**

*   沿着 HTML5 和 Chrome 的思路思考
*   Webdriver 参考规范
*   Selenium 指的是该规范的开源实现。

Selenium 目前是 webdriver 最突出的实现，selenium 中有几个附加命令不是 webdriver 规范的一部分。造成了两者之间的混淆。

对于每个浏览器，这对于调试两个文档中的差异尤其重要。

然而，由于它们经常互换使用，这在指南和教程中会令人难以置信的混乱。这部分是由于他们混合在一起的历史。

理解这种差异可能会为您节省大量的文档工作时间。

* * *

<figure>

[![Every infant is a miracle](img/c425f5aabfe0add2bd56dc999aff73d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0JIbSKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ew9n9yw9l63bkk3xbd2g.jpg)

<figcaption>Every child is a miracle</figcaption>

</figure>

**Webdriver 规范本身就是一个小奇迹**

对于那些跨多个浏览器工作的人，尤其是在早些年(10 多年前)。你会知道，让一群互为竞争对手的浏览器供应商在某件事情上达成一致是多么的不可能。

对于 webdriver 来说，问题在于它是一个相对“低优先级”的项目，有资源限制，这使它变得更糟。这是终端用户永远不会直接体验到的特性。

每个浏览器只有一个或几个开发人员在开发他们的 webdriver 实现。

事实上，在 w3c 中，我们有一个可以单独工作的协议，这是 Selenium 开发团队多年辛勤工作的一个小小的奇迹。

它可能有时不完整，并且在浏览器之间不一致，但是，这是一个我们至少可以在某种程度上达成一致的规范，并且自 2011 年开始正式化以来一直引以为豪。

所以给他们一点掌声👏

> 我可能很挑剔，但我确实非常尊重他们迄今为止所做的一切。

* * *

<figure>

[![Choose a flavour](img/3c39e983f69101935b2a6e84ddb9b82b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hJAvxeXP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/74vt3vwrj5kiqpqvszlq.jpg)

<figcaption>Choose any flavour</figcaption>

</figure>

**Webdriver 是一个 HTTP API，有许多客户端库实现**

你可以在这里选择你的语言/毒药

*   HTTP API: [W3C 规范](https://w3c.github.io/webdriver/)
*   Javascript: [webdriver.io](https://webdriver.io/) (这很容易混淆，因为它们使用了相同的名称)
*   PHP: [php-webdriver](https://github.com/facebook/php-webdriver)
*   ruby:[Selenium::Remote::Driver](https://metacpan.org/pod/Selenium::Remote::Driver)

一方面的建议，就像早期的 CSS/JS 在多种浏览器上一样，学会怀疑不同浏览器/客户端之间的规范兼容性。文档不一致，到处都是。

虽然 MDN mozilla.org 会很高兴地告诉你 CSS/JS 在哪些浏览器上有效(或无效)。

到目前为止，Webdriver 还没有相应的在线资源。在内部，这是我们可能计划在 Uilicious 中自己开发的东西，考虑到我们已经开始堆积如山的随机便利贴，说明每个浏览器的协议是如何不同的。

* * *

**Webdriver 是一个“远程控制接口”**

> 短语“远程控制接口”是从规范中引用的。

Webdriver 是作为自动化协议构建的，这使得它对于测试自动化来说不够理想，因为它们的目标非常不同。例如，webdriver 的断言命令非常有限。

为了使用 webdriver 进行测试，大多数用户会使用一个测试解决方案，它扩展了端到端测试的命令。

例如，要检查 webdriver 中的屏幕上是否存在文本，您需要运行以下命令。

*   找到您希望文本出现的元素
*   获取元素及其内部 HTML 文本字符串
*   检查结果文本字符串中是否存在文本

对于 uilicious(以及其他一些测试解决方案),我们将其简化为一个命令

*   `I.see("Text You Expect to see on screen")`

* * *

<figure>

[![Mac mini farm](img/9281cbc394bfecbaaebc6842328c6f35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUBrdLmk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1idfrhdqzixqm9hi3luv.jpg)

<figcaption>Need to run an army of safari tests? You may need one of these</figcaption>

</figure>

**网络驱动服务器**

示例: [Selenium-standalone](https://www.npmjs.com/package/selenium-standalone)

通常，webdriver 服务器是指安装了“webdriver server”程序的所需操作系统的单个虚拟机/物理实例。

或者，这可以包括通常通过 USB 电缆连接到 webdriver 服务器的移动设备。

这通常会在单个浏览器上一次运行多达 1 个会话。自动化将从哪里开始。

**网络驱动网格/路由器**

示例:[硒栅](https://www.seleniumhq.org/docs/07_selenium_grid.jsp)

一个专用的服务器程序，它将多个 webdriver 服务器连接在一起，以便在单个 API 端点上使用。这是用来绕过每台服务器的限制，并在更大范围内运行多个会话。

以及用户认证和对网格访问的限制(防止任何一个用户一次运行太多的测试)

webdriver、Saucelab 和 browserstack 的云提供商为这类终端提供了和黑莓一样古老的设备。以及最新的 chrome 夜间版本。

* * *

[![Can you hear me from the other side?](img/f8b6f4c55cbbd4c406634a1664303ea1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U1mm0Gg8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c268hibnopp6hwd3a4pw.jpg)

**测试联网**

我们在办公室里常用的一个类比是，把荒谬的命令想象成我们通过电话发出的指令。另一边的运营商是 webdriver 服务器。执行`I.goTo`、`I.see`、`I.click`等命令

因此，从网络端点来测试任何网站，另一端的 webdriver 服务器必须能够加载给定的测试 URL。

同样，就像电话另一端的人无法直接连接到本地主机上的网站一样。webdriver 服务器也不能。

然而，在类似的情况下，您可以通过使用其他工具来提供访问，如 [ngrok](https://ngrok.com/) 或端口转发。

* * *

> 希望这对于那些希望深入了解 webdriver 的新手来说已经足够了🙂

# 那么关于基于 webdriver 构建的 uilicious 呢？

我在 uilicious 中所做的，是运行类似这些
的测试脚本

```
// Lets go to dev.to
I.goTo("https://dev.to")

// Fill up search
I.fill("Search", "uilicious")
I.pressEnter()

// I should see myself or my co-founder
I.see("Shi Ling")
I.see("Eugene Cheah") 
```

并生成[可共享的测试结果](https://snippet.uilicious.com/test/public/1cUHCW368zsHrByzHCkzLE)

[![Uilicious Snippet dev.to test](img/ddd4b0b6d1cbb7ddda303d99a1b421fa.png)](https://snippet.uilicious.com/test/public/1cUHCW368zsHrByzHCkzLE)

像上面的。所有这一切都通过完全托管的云平台实现。[https://uilicious.com](https://uilicious.com)
(去试试看😉)

* * *

# 快乐航运🖖🏼🚀