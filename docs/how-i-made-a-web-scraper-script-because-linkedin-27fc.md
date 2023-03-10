# 我如何因为 LinkedIn 做了一个网页抓取器

> 原文：<https://dev.to/futoricky/how-i-made-a-web-scraper-script-because-linkedin-27fc>

对许多人来说，拥有大量的 LinkedIn 联系会很方便。您和您的联系人同意通过该平台建立联系，从而共享一些公共信息，包括您的电子邮件(在大多数情况下，您可以选择不共享)。这一切都很美好，直到你真的想使用你从你的关系中得到的所有数据...这取决于您想要什么数据...

## 问题

比方说，你想从 LinkedIn 导出你所有的关系数据，你可以按照他们的指示来做。它从每个连接生成一个包含以下信息的 CSV 文件:
`First Name, Last Name, Email Address, Company, Position, Connected On`

那么这里的问题是什么？尽管它在 csv 上给了你一个电子邮件地址栏，但它并没有真正提供你的任何联系人的电子邮件！我猜他们曾经提供它，但从未更新导出 csv 来删除该列。我也检查了他们的公共 API，没有发现任何与你的联系相关的邮件，但是我确实发现了这个 [StackOverflow 讨论](https://stackoverflow.com/questions/20970016/linkedin-api-get-information-about-my-all-connections)表明他们实际上曾经提供过这些信息，但是现在他们没有了。WTF LinkedIn？所以我决定把所有联系人的邮件都清理掉。我的意思是，我可以手动访问它们，但要获得我所有的 2000 多封连接电子邮件需要大量的时间。

## 解

我需要脚本做些什么来实现这一点？首先，我需要它登录，然后搜索连接的名称，进入它的个人资料页面，并获得电子邮件。简单的...对吗？

### 第一次尝试

通过使用 LinkedIn 的搜索输入获取电子邮件是可行的，直到由于太多的搜索请求，他们以可疑行为半阻止了我的帐户。这是大约 500 个连接。

### 第二次尝试

也许我应该更加小心 x 时间内的搜索量。所以我添加了设置间隔(默认为 1 小时)和设置每个间隔之间搜索的电子邮件数量(默认为 50)的选项。

LinkedIn 是一些卑鄙的混蛋，他们又半屏蔽我了！我搜索了关于这种半屏蔽的信息，发现它是专门为避免自动机器人在网站上做事情而设计的。伟大的....

### 第三次尝试

我想，也许搜索限制只适用于一般搜索，所以让我们尝试直接点击连接，当它出现在建议框中时，在输入连接的名称后出现。

[![](img/c43f63bb9a3d97eba54b3b057ee30353.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GaDRf2aE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/20invw1pwwf9u91fb85g.png)

事实证明，我用来抓取页面的库( [NightmareJS](https://github.com/segmentio/nightmare) )没有检测到 DOM 元素，所以我不能对它做任何事情。叹气....

### 第四次也是最后一次尝试

[![](img/531358315010a3287d3f68ca01e66535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---cfFl5KE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpmhsa1wap0upccvpj7q.png) 
经过一番摸不着头脑和一番深思熟虑才退出这个小项目的我终于想出了另一个办法...直接进入我的连接部分，并使用连接搜索输入，它只搜索我的连接。这终于没有搜索限制了！！

在所有的电子邮件被收集后，我只需创建一个包含所有电子邮件的`email.txt`文件。就这样了！

TL；我想得到我所有 LinkedIn 联系人的电子邮件。LinkedIn 不允许通过导出您的连接数据来检索它们，所以我创建了一个 web scraper 来获取它们。

对于任何对这个脚本感兴趣的人，你可以在这里访问它[。](https://github.com/FutoRicky/linkedin-email-extractor)

### 注

如果 LinkedIn 更新他们的页面并改变脚本中使用的元素的类，它将停止工作。您可以检查源代码，验证 LinkedIn 上是否有任何类发生了更改，并更新脚本以使其再次工作。

感谢阅读！