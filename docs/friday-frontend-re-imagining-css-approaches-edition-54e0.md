# 星期五前端:重新想象 CSS 方法版

> 原文：<https://dev.to/kball/friday-frontend-re-imagining-css-approaches-edition-54e0>

希望你这一周过得愉快！本周让我印象深刻的是几篇文章，它们从不同的角度审视了 CSS 是什么以及我们如何使用它。一篇是关于结合 SASS 和 CSS 变量的，它让我看到了我最喜欢的工具(SASS)和强大的 CSS 之间的新的协同作用。另一个是将算法概念应用于 CSS。然后是一些令人兴奋的图形合成和渐变。

本周也有一些非常优秀的 JavaScript 文章——我很高兴找到了一篇关于大型和长期应用程序的最佳实践的文章，并且非常喜欢对 React hooks 的深入研究。

最后，在 other awesome 中，请确保您看一看无 javascript 的前端部分，因为它挑战了许多现代假设，同时仍然创建了一个强大的现代结果。

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [用集装箱单元建造坚固的布局](https://www.smashingmagazine.com/2019/03/robust-layouts-container-units-css/)

这是一篇非常好的文章，展示了使用 CSS 变量构建健壮的、内部一致的布局的技术。

##### [如何结合 SASS 颜色函数和 CSS 变量](https://codyhouse.co/blog/post/how-to-combine-sass-color-functions-and-css-variables)

我最近看到了一些文章，提出了用现代 CSS 替换 SASS 的问题，但我喜欢这篇文章的原因是它非常关注两者之间的协同作用。将 sass 函数用于人机工程学，将 CSS 变量用于最终 CSS 的实时更新和整洁，这是一个非常非常好的方法。

##### [编写 CSS 算法](https://notlaura.com/writing-css-algorithms/)

我喜欢这个作者思考 CSS 的方式。他们主张重用“算法”这个词来描述一个定义良好的声明或声明集，目的是实现特定的结果，而不是试图为 CSS 使用与其他编程语言完全不同的语言。考虑到算法的[广义定义为“解决问题或完成某些目标的一步一步的过程”，这完全可行。作者进一步阐述了这一点&描述了一种开发“CSS 算法”的方法。](https://www.merriam-webster.com/dictionary/algorithm)

##### [面具合成:速成班](https://css-tricks.com/mask-compositing-the-crash-course/)

这有点出乎我的意料。我不能说我完全理解了它，它真的很长，最终的代码需要各种各样的浏览器黑客式的解决方法才能跨浏览器，但 OMG 我被基于 css 的面具的力量震惊了。这些例子大部分是图像，但是没有理由这些技术不能掩盖 DOM 中的任何东西，让您创建超级酷的效果。

##### [用 CSS 重现 Facebook Messenger 渐变效果](https://css-tricks.com/recreating-the-facebook-messenger-gradient-effect-with-css/)

有趣的探索 CSS 渐变和它们的局限性，所有这些都在重现 Facebook Messenger 最近推出的“渐变气泡”效果的框架内。

#### JavaScript

##### [可达性不是“反应问题”](https://www.netlify.com/blog/2019/02/25/accessibility-is-not-a-react-problem/)

关于如何开始编写可访问的 React 应用程序的一组可靠的建议。最后是一个很棒的额外推荐资源列表。

##### [JavaScript 命名惯例:该做的和不该做的](https://medium.freecodecamp.org/javascript-naming-conventions-dos-and-don-ts-99c0e2fdd78a)

对于 JavaScript 初学者或新手来说更有用，但它以一种易于消化和吸收的方式封装了许多常见/最佳实践。

##### [维护大型 JavaScript 应用](https://9elements.com/io/maintaining-large-javascript-projects/)

我们的许多学习资源都是“从零开始”的教程。从头开始构建应用程序。从头开始尝试这个东西。从头开始重写你的应用程序。很少看到有人写关于多年来维护现有(大型)应用程序的经验教训。这篇文章确实如此，我认为这些确实是非常有价值的经验。

##### [深潜:React 钩子到底是怎么工作的？](https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/)

这是一篇很棒的文章，不仅仅是为了理解 React 钩子，也是为了真正理解闭包。它不短，也不超级简单，但我很喜欢它，并且从头到尾都在学习。

##### [理解 JavaScript 中的闭包](https://medium.com/javascript-in-plain-english/understanding-closures-in-javascript-in-3-minutes-557ebb8a215b)

如果前一篇文章感觉有点难以理解，这篇文章可能是一个更好的尝试。它的范围很窄，相对较短，为您提供了关于闭包及其工作原理的很好的解释。

#### 提升链接

##### [Udemy 三月储蓄狂欢](http://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.9900&type=3&subid=0)

在接下来的一周——直到 3 月 20 日——Udemy 正在进行一场 3 月份的促销活动，课程折扣低至 12.99 美元。如果你不确定要找什么课程，你可以看看[我关于 2019 年要学的前端主题的帖子](https://dev.to/kball/top-5-frontend-development-topics-to-learn-in-2019-4m0m)。不管怎样，如果你已经得到了你一直想要学习的东西，现在就利用它，并且便宜地得到它！

#### 其他牛逼

##### [一个无 JavaScript 的前端](https://dev.to/winduptoy/a-javascript-free-frontend-2d3e)

这是一个构建“现代”前端的非常酷的外观，同时避免了(大部分)JavaScript。我玩了一下这个应用程序——它很简单，很“苗条”,但是运行很好，感觉很快，虽然从视觉/设计的角度来看，极简主义者并没有感到非常不合适。

##### [2019 年科技报告中的设计](https://designintech.report/2019/03/09/design-in-tech-report-2019)

对技术生态系统以及设计在其中所扮演的角色的有趣而广泛的观察。多媒体取决于你想要什么-只是通过桌面幻灯片版本阅读是迷人的，但感觉有点支离破碎。作者在 SXSW 展示它的视频给出了更多的背景。

##### [平民缓存-控制](https://csswizardry.com/2019/03/cache-control-for-civilians/)

关于提高网络性能的最简单的方法之一——更好的缓存的精彩文章。如果你使用 CDN，它会变得更加有用。如果你在一家有专门运营人员的大公司，这可能超出了你作为前端工程师的工作范围，但不管你是否关心前端性能，你都应该理解这一点，并检查以确保你的资产被适当缓存。

##### [GraphQL 资源:顶级工具、扩展&初学者教程](https://dev.to/tomekponiat/graphql-resources-top-tools-extensions--resources-for-beginners-2f13)

GraphQL 是我今年要学习的[热门话题之一，而且似乎每周都有更多的资源出现。如果你正在为初学者寻找一些最好的，这是一个超级有用的综述文章。](https://dev.to/kball/top-5-frontend-development-topics-to-learn-in-2019-4m0m)

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】