# 错误预防:可用性启发之一

> 原文：<https://dev.to/izzet/error-prevention-one-of-the-usability-heuristics-2l4b>

# 什么是‘防错？’

ISO 25000 将“错误预防”描述为“系统防止用户犯错的程度”换句话说，设计一个系统，让用户很难犯错误。

# 如何衡量‘防错？’

防止用户犯错误使生产率更可靠。从某种意义上说，如果用户能够尽可能快速有效地完成任务而不犯任何错误，这将使他们更有效率。这也将增加系统的可用性。

因此，捕捉错误，并根据这些错误重新设计系统，使其不容易出错，然后重复运行可用性测试，这使得测量每种情况下错误之间的差异成为可能。如果在系统的每一次重新设计中错误都减少了，这意味着可用性同时也在逐渐提高。

# 如何防止‘错误？’

“防错”与可用性的其他因素直接相关。因此，增加系统的“可学性”和“可操作性”将最有可能防止错误。此外，提供“适当性可识别性”也会达到同样的效果。

当然，在“错误预防”框架中还有其他经过验证的方法。它们涵盖了从基本的预防措施，比如在执行重要任务之前向用户显示确认对话框，到复杂的可用性决策。

如上所述，这些预防措施最基本的例子可能是“确认对话框”它从软件系统的最开始就存在了，它承载着一项非常重要的工作:防止用户在使用系统时做不可逆的事情，比如无意中删除了一个文件。

[![A typical confirmation dialog in Windows operating system](img/eca176806485b43a90968c8728697e32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g2Gk-43P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/haoxpue41tptj937grhw.png)

使用标准设计惯例可能是另一个很好的例子。如果一个系统提供了与其他类似系统相矛盾的设计，用户可能会感到困惑，不明白他们可以采取什么行动。

下图显示了 2015 年环球小姐比赛中使用的著名揭示卡，它最终几乎毁了用户的生活。这张卡的使用者错误地将第一名宣布为比赛的获胜者。 <sup id="fnref2">[2](#fn2)</sup> 这完全是因为卡的设计不好。这是因为卡片的创造者可能没有想到把赢家放在底部会导致误导。这是一个错误，因为用户倾向于在顶部看到赢家。

[![The winner is misplaced on the card](img/00e8055c6ca948f1549328a1725aec5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f18a84nV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8tngf28r22xm3wp1qrq6.jpg)

与上面的情况类似，拥有“撤销”功能也可能有助于用户重新考虑动作，即使它已经发生了。提供这种能力不仅可以帮助用户在使用系统时感到更安全和更自信，而且对可用性也有积极的影响。

[![‘Undo’ option is provided for the user.](img/691a599118ba718f0e6435447a595374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xjudj-Ac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wztrvrkseedf2ruswtn.jpg)

如上图所示，Gmail 不仅为意外删除邮件等破坏性行为提供了这一功能，还为发送邮件提供了这一功能。 <sup id="fnref3">[3](#fn3)</sup> 由于 Gmail 是使用最多的电子邮件服务之一，这是该功能有用性的重要证明。

另一种类型的“错误预防”可能是为用户提供“好的默认”。当用户必须在选项之间做出选择时，系统可以通过做出预测或从用户过去的决策中学习来为这些选项建议合理的默认值。因此，“好的默认值”有助于减少错误。

“好的默认”的一个很好的例子是 iOS 上的邮件应用程序如何过滤电子邮件，并在用户想要将电子邮件移动到另一个文件夹时建议一个预测的文件夹。可以忽略该建议，用户仍然可以选择另一个文件夹。

[![Suggested folder name in iOS action sheet](img/85161c1de5d6e738ebae43a4b914c243.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CAtVsc6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/adhisfxs5q3yr577x2ul.jpg)

在这个例子中，Mail 应用程序通过考虑发件人和主题来学习用户在将电子邮件移动到另一个文件夹时的行为。

# 结论

尽管用户在使用系统时总会犯一些错误，但通过在设计系统时考虑用户的体验来减少错误是可能的。可以通过帮助用户在系统界面中找到他们的路来防止错误。

* * *

1.  ISO/4.2.4.4 IEC 25010:2011 用户错误保护。[https://www . iso . org/obp/ui/# iso:STD:iso-iec:25010:ed-1:v1:en](https://www.iso.org/obp/ui/#iso:std:iso-iec:25010:ed-1:v1:en)↩

2.  2015 年环球小姐加冕的选手错了。[https://edition . CNN . com/2015/12/20/us/miss-universe-错-参赛选手-加冕/index . html](https://edition.cnn.com/2015/12/20/us/miss-universe-wrong-contestant-crowned/index.html)↩

3.  发送或取消发送 Gmail 邮件。[https://support.google.com/mail/answer/2819488?hl=en](https://support.google.com/mail/answer/2819488?hl=en)↩