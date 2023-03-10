# 分数之外的可访问性

> 原文：<https://dev.to/adrianengine/accessibility-beyond-scores-369b>

可访问性在开发社区中已经获得了很多关注。我们的工具正在变得越来越好，以报告可能的陷阱，这个主题已经成为公司促进包容和防止歧视诉讼的一个重要问题。

最近开发者对网页可访问性大肆宣传的原因之一来自谷歌 Chrome 的团队 [Lighthouse tool](https://developers.google.com/web/tools/lighthouse/) 。

Lighthouse 在开发者大会上积极推广。它也成为 Chrome 网络浏览器上的官方审计工具。它得到了很多关注，主要是因为它使用起来很简单，并且有关于如何修复我们的 web 项目中的几个问题的有用文档。

因此，越来越多的开发人员自豪地在 Twitter 上分享他们的 Lighthouse 分数和他们的主题演讲:

> ![unknown tweet media content](img/cbef9cc0f23c1e0f086c996f420042d6.png)![Aristide Benoist profile image](img/b8af27a4732af89945ddcb34d7cd5703.png)阿里斯蒂德·贝诺伊斯特@阿里贝诺伊斯特![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)“桌面”这个分数本可以更好，但我们选择减慢加载程序的速度以使其更具相关性。介绍信与音乐会举办地的州相对应。2019 年 3 月 06 日下午 18:30[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1103362305075621888)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1103362305075621888)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1103362305075621888)16

我想鼓励这种做法，因为它表明 web 开发人员意识到并致力于提供快速可靠的 web 体验。

## 这比看起来要复杂得多

Chrome 团队已经使可访问性测试变得尽可能容易，这对大多数开发者来说还是一个新概念。

在可访问性审计中获得 100 分可能会导致认为这等同于拥有一个 100%可访问的网站，这**可能不是真的**。

Lighthouse [可访问性审计由 Deque 的 Axe 核心工具](https://www.deque.com/blog/google-selects-deques-axe-chrome-devtools/)提供支持。因为他们使用 Axe 作为他们的规则引擎，他们的评论只返回完全可以自动确定的问题——没有误报。结果是清楚地报告了最常见和最容易修复的可访问性问题。

正如德克所说:

> DevTools 可访问性审计的缺点是这只是故事的一半。根据 Lighthouse 的数据，你的页面在可访问性上可能得了 94 分，但这只是告诉你自动化测试的结果。您还必须执行手动可访问性测试，以真正了解您所处的位置。

Lighthouse 还在审计结果的*“手动检查的附加项目”*部分提出了这一建议。

<figure>

[![A screen capture of "Additional items to manual check" from Lighthouse Audit results](img/e938fa9632364891b269daadf6d6d645.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XU0tmV3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncqaftp543vpw56rp4fv.png)

<figcaption>"Additional items to manual check" section from Lighthouse Accessibility Audit results</figcaption>

</figure>

但是因为它不影响分数，开发者可能倾向于忽略这些推荐并继续使用它。

如果我们真的致力于交付可访问的产品/体验，手动可访问性测试仍然是必须的。

## 灯塔无障碍获得 100 分后怎么办？

开始您的手动辅助功能检查，打开您的网页并开始使用键盘。一个优秀的键盘输入体验的目标是有一个逻辑的 tab 键顺序和容易识别的焦点风格。

继续用屏幕阅读器测试。如果你用的是装有 Mac 或 iOS 的苹果设备，你可以使用内置的屏幕阅读器:VoiceOver。

> 由 [@robdodson](https://dev.to/robdodson) 撰写的关于[如何进行可访问性审查](https://developers.google.com/web/fundamentals/accessibility/how-to-review)的更多见解

### 尝试其他工具

考虑使用其他工具运行审计，看看您还能得到什么建议。以下是您可以尝试的其他工具列表:

#### 书签

*   [A11Y.css](http://ffoodd.github.io/a11y.css/)
*   [aslint.org](https://www.aslint.org/)
*   [pa11y](http://www.pa11y.org/)
*   [tota11y —可访问性可视化工具包](http://khan.github.io/tota11y/)

#### 镀铬扩展

*   [aXe 浏览器扩展](https://www.deque.com/axe/)
*   [Chrome 辅助开发工具](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb?hl=en)
*   [WAVE 网页无障碍评估工具](https://wave.webaim.org/extension/)
*   [WCAG 可访问性审计开发人员界面](https://chrome.google.com/webstore/detail/wcag-accessibility-audit/kpfleokokmllclahndmochhenmhncoej?hl=en)
*   [site improve Accessibility Checker](https://chrome.google.com/webstore/detail/siteimprove-accessibility/efcfolpjihicnikpmhnmphjhhpiclljc)

#### 其他工具

*   [Tenon.io](https://tenon.io/) (提供免费和付费计划)

> 更多工具和资源:[可访问性思维](http://lab.adrianquevedo.com/a11y-mindset/)

## 结论

作为网页开发者，我们需要继续教育和倡导网页可访问性。自动化工具使得测试变得非常容易，但是这并不是避免手工测试的借口。我们已经做好准备，让无障碍网站成为常规，而不是例外。

> *如果你开始接触网页可访问性，请考虑阅读这篇介绍:*
> 
> [![maxwell_dev image](img/14673be315f69583cf2f2dbaaa4fa9a1.png)](/maxwell_dev) [## 我希望我有网页可访问性介绍
> 
> ### max Anton ucci 10 月 23 日 188 分钟读取
> 
> #a11y #beginners #explainlikeimfive](/maxwell_dev/the-web-accessibility-introduction-i-wish-i-had-4ope)

感谢阅读！请在评论中提出你已经尝试过的其他工具或想法。