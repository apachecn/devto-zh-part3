# 为什么我不喜欢在 Excel 中粘贴特殊值

> 原文：<https://dev.to/coda/why-i-dislike-paste-special-values-in-excel-1511>

Excel 快捷方式对于提高您的工作效率非常重要。自从我学会了我的第一个键盘快捷键，我就试图尽可能多地使用 Excel 键盘快捷键来“自动化”我在 Excel 中的工作——有时对不常用的 Excel 操作进行研究，只是为了知道 X 操作的快捷键存在。

我认为[粘贴特殊值](https://support.office.com/en-us/article/keyboard-shortcuts-for-paste-special-options-c31b7c9e-69ce-4b60-8c3a-dc5ea10d872c)是最常用的 Excel 操作和键盘快捷键(ALT、E、S、V 为 Excel 2003 的粉丝)。在使用 Excel 10 多年后，我讨论了为什么我不再使用粘贴特殊值，以及为什么您也应该这样做。

### 反模式

> 反模式(Anti-Pattern)—一种文学形式，描述一个问题的常见解决方案，该方案会产生明显的负面后果

这是从 [SourceMaking](https://sourcemaking.com/antipatterns) 中提取的定义。虽然这个定义描述了一种文学形式，但是它也可以应用于其他领域，比如软件(或者在这个例子中，Excel)。我给[写了一篇帖子](https://blog.coda.io/make-your-data-accessible-or-youre-fired-c12c64c877f9)，提到在滚动行李箱出现之前拖着普通行李箱到处走是多么荒谬。我们完全可以握住行李箱的把手，因为，嗯，这正是我们被教导要做的(谢谢新秀丽！).在你开始使用滚动行李箱后，为什么你会因为怀旧的原因而回到旧的手柄行李箱呢？在我看来，抓住一个手提箱是一个*反模式*，因为一个新的解决方案出现了，带来了更好的结果。

<figure>[![](img/c2f7a4391d0194a6beb3236586225693.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fhOSQfuB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AgOcIesHzaOmMbSOP5Kap0g.jpeg) 

<figcaption>来源:[http://www.demeterclarc.com](http://www.demeterclarc.com)</figcaption>

</figure>

### 粘贴特殊值的诱惑

有大量的文章介绍了选择性粘贴的工作原理，在新版本的 Excel 中访问选择性粘贴的更简单的方法，还有 YouTube 视频介绍了如何进行各种形式的选择性粘贴。

有趣的是，如果你搜索为什么你应该使用粘贴特殊值，很少有文章出现在*为什么*你会首先使用粘贴特殊值。下面是一些文章的摘录，试图讨论粘贴特殊值背后的*为什么*:

> 假设我们已经为经理创建了一个计算电子表格，他要求按月列出 SnackWorld 的销售数据。我们已经使用了一系列复杂的公式来根据外部数据表计算这些值，现在我们想将我们的输出发送给我们的经理。但是，我们不想发送那些复杂的公式——我们的经理只想看到我们的最终数字，而不是关于我们如何实现的不必要信息。
> 
> 作为值粘贴是商业世界中极其常见的做法。一般来说，分析师在将关键数据发送给经理审阅之前，会将其作为值进行粘贴。这有助于确保 a)如果接收者意外修改源数据，发送的数据不会意外改变；b)电子表格的接收者不会被复杂的公式分心，而是会注意所提供的数字。(来源:[桌面明亮](https://www.deskbright.com/excel/paste-special/))

一个非常简单的解释:

> 复制公式值的原因有很多。有时候你不想让别人知道结果是怎么得出的。其他时候，你只是想让它对用户来说更简单、更快捷。如果您的电子表格调用外部资源或电子表格，情况尤其如此。(来源:[生产力组合](https://www.timeatlas.com/copy-excel-formula-values/))

粘贴特殊值的好处是显而易见的:您可以复制和粘贴一些不会“带来”任何其他东西的数据(例如，您正在复制的数据的公式和格式)。根据我的经验，以下是使用粘贴特殊值的实际原因:

1.  原始数据(可能是从数据库或 ERP 应用程序中导出的)格式不正确，您只希望这些值用于数据分析
2.  您正在构建一个仪表板，并且需要以一种让您的最终受众美观的方式快速格式化数字
3.  需要在工作簿之间复制/粘贴，并且不希望公式引用源工作簿，因为打开粘贴的工作簿时链接可能会断开

为了处理上述情况，我们学习了粘贴特殊值，因为这是使数据进入一种状态的最快方法，在这种状态下，您可以操纵数据以供进一步分析。今天，我仍然为用例 1 粘贴特殊的值，以处理在将最终输出复制并粘贴到 PowerPoint 或电子邮件之前，我可能需要在 Excel 或 Google Sheets 中进行的快速和肮脏的分析。

### 在 Excel 中粘贴值不利于透明度和自动化

基于对*为什么*使用粘贴特殊值的解释，出现了几个主题:

1.  你不希望某些观众(如你的经理)看到计算背后的公式
2.  对于最终用户来说，输出应该简单易懂
3.  你不希望不懂 Excel 的人篡改你的计算

<figure>[![](img/03387e2717bd9c37fbf912e2d3e7e281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wEAF7bYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2A7FGiQF7yZiJ4FA14MPu-Lw.gif) 

<figcaption>来源:Gifer</figcaption>

</figure>

这种行为导致一件事:*缺乏透明度*。你是“数字”的家伙，如果任何人对这些值是如何计算的有疑问，他们不能自己检查来源，他们必须去找你。即使计算在您的组织内部进行，并且从未离开您公司的墙壁，数据和计算也应该是透明的，任何人都可以看到，以便可以对内部流程进行健康的批评和辩论。这引出了我的第二个观点，粘贴特殊值的一个意想不到的后果:重复性任务。

顺便说一句，在一家大型上市公司工作意味着你有收入、季度报告和最后期限要赶。在这种环境下，很容易跳到捷径，帮助你更快地完成工作，赶上最后期限。在这种环境下，使用选择性粘贴值是以我上面讨论的格式获取所需数据的最快方法。这种方法的问题是，你永远不会后退一步来审视整个过程，因为你被束缚在月底或季度截止日期来完成一个可交付成果。有没有一种方法可以自动化或简化常见的任务？很可能，但是没有足够的时间或资源来构建该解决方案，因为您只需要完成您的工作。

<figure>[![](img/2959eebff9f7907d2534cfbdcfb54ec6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v89P7KIW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/316/1%2AasMgKMgAVHnugW6cMDbHLg.gif) 

<figcaption>来源:吉菲</figcaption>

</figure>

由于粘贴特殊值会导致在电子表格中插入硬编码的数字和文本，因此您必须每隔 X 分钟/周/天/月更新它们。但是你的论点是:"*进行选择性粘贴要快得多，因为我确切地知道在哪里以及如何进行选择性粘贴！*

我就在这里打断你，因为你，又一次，是瓶颈。如果有人必须继承电子表格或模型，您必须提供详细的解释，说明如何将选择性粘贴转换为您想要的格式。如果新的列/行被添加到您正在复制的数据中，这意味着您正在更新一些分析师要破译的指令。我不知道我见过多少次这样的电子表格，它有一个包含 50 条说明的文本框，看起来像这样:

> 选择“计算”页面上的所有单元格，确保一直选择到最后一行，并在预设格式的“封面”工作表中进行选择性粘贴。

更现代的例子是，假设您正在运行一个显示当前天气和温度的简单网站。您可能会从国家气象局获取数据，但是您并没有让数据自动显示在您的网站上，而是执行了以下操作:

1.  在一些显示国家气象局数据的后端应用程序中读取数据
2.  去你网站的后端 CMS
3.  手动更新天气和温度数据，然后点击发布

*简单地说，这就是粘贴特殊值*。你可能听过爱因斯坦对精神错乱的定义:“一遍又一遍地做同样的事情，却期待不同的结果。”我在重复粘贴特殊值的上下文中对精神错乱下了这样的定义:

> 一遍又一遍地做着同样的事情，却忽略了扩展的方式。

### 粘贴特殊值的替代方案不是您所期望的

替代粘贴特殊值的解决方案是什么？从战术上来说，Excel 和 Google Sheets 中有其他方法可以使您的数据更具可伸缩性:

1.  **复制粘贴公式** —一个丑陋的继子粘贴特殊值，但至少你得到了引用一些源数据的公式
2.  **GETPIVOTDATA** —稍微高级一点，但是现在您引用的是可以基于一些源数据更新的数据透视表。Excel 中 GETPIVOTDATA 的优点是输入可以是相对或绝对引用，因此如果列标题发生变化，最终输出也会发生变化。
3.  **表格** —我在 Excel 和 Coda 中写了大量关于[表格的内容。这种解决方案可能是最健壮的，但也有局限性(阅读博客文章了解更多)。](https://blog.coda.io/will-you-change-your-mind-about-tables-data-16ec0466269a?source=user_profile---------6------------------)

<figure>[![](img/b8b3b133feb35071a4a73b1daa48ea95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NGsWMk9m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2Ak6pCB8PWgGaDeL-8la7oiQ.jpeg) 

<figcaption>来源:Imgur 社区</figcaption>

</figure>

潜在的解决方案不是 Excel 或 Google Sheets 中的产品特性，而是关于如何引用和显示数据的思维方式的转变。当您从电子表格中退一步，考虑如何使其更具可伸缩性，以便查看您的数据的任何人都可以 1)了解它是如何制作的，2)快速进行更新时，您会开始问自己一些更重要的问题，首先是为什么要使用电子表格:

1.  我使用什么系统将数据*输入到*我的电子表格中？
2.  电子表格是存储我的数据的最好地方吗？
3.  谁需要查看我的报告，以易于理解的可视化方式向他们展示数据的最佳方式是什么？

这些问题并不容易回答，但它们暗示了 IT、信息安全、运营以及只需要查看数据的优秀业务用户的交集。

### 重新学习复制粘贴

<figure>[![](img/ea9da4a9e0be1866127f1615c439aba1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M9C7rDEh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2AHCHZ0zsFay43vDrn3YaEuQ.png) 

<figcaption>来源:WinWorld</figcaption>

</figure>

我不喜欢粘贴特殊值，这可能是我们作为知识工作者最初学习复制和粘贴的结果。你可能从 Word 文档中学到了复制和粘贴，你只需要复制和粘贴文本(对我来说，它是 Corel WordPerfect 7.0)。这种模式在很大程度上归功于 Microsoft Office，并延续到电子表格中，现在我们只剩下选择性粘贴来帮助我们移动和显示关键的业务数据。当我们灌输了 40 年从终端复制粘贴到文档和电子表格的经验时，很难质疑现状。

如果你不得不重新学习“复制和粘贴”意味着什么呢？你会有什么不同的做法？

<figure>[![](img/980b499f035ab5e193443d61a9ba4ea9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ffMNLEEb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/262/1%2A0fqcYUG3hb_uY5ZoQ6HHUA.jpeg) 

<figcaption>来源:[www.brianjgraf.com](http://www.brianjgraf.com)</figcaption>

</figure>

巧合的是，我们在 [Coda](http://www.coda.io) 的团队正在解决这些大问题，因为它们对如何正确组织和扩展数据有影响。你可能会认为像复制和粘贴这样简单的事情不应该受到如此多的关注，但是如果我们想让数据更加透明，让报告更加自动化，这是一个必须学习和重新学习的基础。

有时候，您只想在 Excel 中复制一个工作表，或者在 Coda 中复制一个部分，但是这对底层数据意味着什么呢？当您在 Excel 中复制工作表时，您正在创建与您复制的原始数据表无关的全新表格。如果这两个表没有通过公式相互关联，那么您可能会遇到数据重复的情况，调试文件会变得很麻烦。这个问题的一个潜在解决方案是，复制一个表实际上并不会创建一个全新的表，而是创建一个通过*连接到*原始表的表，这样更新就可以一直进行下去。

### 如何“教授”相关的表格

<figure>[![](img/f914f17e19ffb92e14098522ded94197.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jyLexR4V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/477/1%2AfKCBTZHnBnGH43ycheL9PQ.gif) 

<figcaption>来源:microsoft.com</figcaption>

</figure>

在文档中工作时，复制和粘贴是您首先要学习的事情之一。从简单的复制粘贴到在 Excel 中粘贴特殊值需要时间，当我向某人解释如何做时，我总是从 *why* 开始，并引用我上面提到的一个用例。从选择性粘贴到连接表以维护数据之间的关系是另一个重大进步，因为现在我们正在进入关系数据库领域。除非您是数据库管理员，否则这个图像可能看起来难以理解。

问题的关键在于，对于大多数组织来说，这是正确的方法来组织他们的数据，并允许分析师轻松操作和可视化。也许对于那些不熟悉电子表格和 Coda 的人来说，一个常规的复制和粘贴会做你期望它做的事情(创建一个与源无关的数据的复制表),你可能会被提示一条消息，询问你是否愿意*将你的数据*连接在一起。对于高级用户来说，复制和粘贴可以*自动*将你的表连接在一起。不管用户是谁，他们对数据库的熟悉程度如何，挑战在于以简单且不复杂的方式教授什么是关系数据库。

我对教授这些概念充满热情，我们正在 Coda 积极应对这一挑战。我希望你能一起来！

* * *