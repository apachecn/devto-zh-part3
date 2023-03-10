# 星期五前端:新的反应模式

> 原文：<https://dev.to/kball/friday-frontend-new-react-patterns-1j68>

希望你度过了愉快的一周！另一周吸引我眼球的是 JavaScript 部分——两篇非常有趣的文章探讨了我以前在 React 中没有见过的模式。第一篇关于新的钩子相关模式，第二篇关于递归组件(！).

还有一些很棒的其他材料——一定要看看 CSS transforms visualizer，阅读他们如何进行 css-only-chat 真的是一个脑筋急转弯。我也很喜欢这篇关于作为一名前端工程师弥合设计和开发之间差距的文章。总之，这是一周的好文章。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [一种更好的利用顺风的方法](https://www.viget.com/articles/a-better-approach-for-using-purgecss-with-tailwind/)

虽然这篇文章专门引用了 Tailwind，但是它也研究了我在其他场景中看到的一个问题。当使用 CSS 库或框架时，使用 Purgecss 清除掉你不需要的类可能会很好…但是你如何考虑动态计算和应用的类(无论是从你的模板语言还是通过 JavaScript 直接应用)。没有完美的解决方案，但是本文讨论了几种解决方案及其优缺点。他们的最终方法简单得令人愉快，尽管可能有些冗长。我喜欢。

##### [你的 z 索引不起作用的 4 个原因(以及如何修复)](https://medium.freecodecamp.org/4-reasons-your-z-index-isnt-working-and-how-to-fix-it-coder-coder-6bc05f103e6c)

Z-index 是那些看起来应该如此简单的东西之一，然而它有一些细微的差别，有时会让人感到非常沮丧。当您遇到 z 索引问题时，这是一个很好的 1 2 3 4 清单。

##### [CSS-only-chat——一个真正可怕的异步网络聊天，在前端不使用任何 JS](https://github.com/kkuchta/css-only-chat)

不要这样！但是请仔细阅读他们是如何做到的。我的天啊。谈论利用技术开发可能性的边缘…在浏览器中进行网上聊天，没有一行 JavaScript 代码。

##### [调试 CSS 网格第一部分:理解隐式轨迹](https://css-irl.info/debugging-css-grid-part-1-understanding-implicit-tracks/)

深入 CSS 网格中更令人困惑的领域之一——隐式跟踪。解释这种情况，展示一些例子，并提供一些避免麻烦的启发。非常好！

##### [CSS3 变换 2D & 3D CSS 变换函数可视化器](https://css-transform.moro.es/)

这是一个超级酷的工具，可以可视化不同的 CSS 转换函数将会做什么。修补变换，应用随机值，通常探索并实时观察它做什么。然后，当你有一个你想应用到你自己的网站，立即看到相关的 CSS。

#### JavaScript

##### [用 React 钩子发现图案](https://ponyfoo.com/articles/discovering-patterns-with-react-hooks)

像 hooks 这样真正的新方法的一个超级酷的事情是，我们开始看到新的模式和可能性。这是作者发现的一个这样的模式，以及它是如何在几个不同的例子中发生的。

##### [递归 React 组件](https://www.bitovi.com/blog/recursive-react-components)

这是一个有趣的思维弯曲。React 组件“只是 JavaScript”，事实上经常表示为简单的函数……所以它们当然可以像其他函数一样递归，对吧？我从来没有想过这个问题，但是有很多有趣的可能性。

##### [UI 动画在反应](https://cssanimation.rocks/ui-animation-react/)

在 React 应用程序中添加动画的详细步骤指南。查看您可能想要制作动画的几种类型的过渡，并演示如何处理它们。包括关于相同内容的谈话的嵌入视频。

##### [写干净可伸缩 JavaScript 的 12 个技巧](https://dev.to/bnevilleoneill/12-tips-for-writing-clean-and-scalable-javascript-287l-temp-slug-3380958)

一份可靠的建议清单。如果你写 JS 已经有一段时间了，你可能知道所有这些，但是随着语言的快速发展，几乎每个人都会有一些新的想法。我仍然没有在析构中充分利用缺省值。

##### [你在 Web 组件中做错的 10 件事](https://javascriptkicks.com/r/187442?url=/articles/187442/10-things-you-are-doing-wrong-in-your-web-components)

我最近对 web 组件有点兴趣…很想知道人们在用它们做什么。这是快速浏览一些东西来思考你在创建 web 组件时可能遇到的挑战。对于每一个问题，它要么直接告诉你如何解决，要么链接到资源。

#### 提升链接

##### [一个月训练营](https://mbsy.co/sGmNV)

如果你和我一样，你有一大堆 Udemy 课程，你已经注册了，但还没有完全完成。没有一群同学&有人设定责任和期限，你很难保持动力。一个月就是这么做的——安排你和其他学生、后勤人员以及乐于助人的教练一起参加为期一个月的训练营，设定截止日期和安排办公时间。课程的种类可能比你在 Udemy 找到的少，但是如果他们有你想要的东西，你完成课程的可能性&实际上学习材料的可能性要高得多。如果你通过这个链接，你会得到 10%的折扣。去看看吧！

#### 其他牛气

##### [前端开发人员如何帮助设计人员和开发人员沟通](https://www.smashingmagazine.com/2019/05/frontend-developers-designers/)

很好地审视了设计/开发关系中可能出现的一些挑战，以及如何弥合这些差距。我也喜欢对[不能不看](https://cantunsee.space/)的标注——我以前没见过，这是锻炼你视觉的好方法！

##### [谷歌准备好赶超 JavaScript 了吗？](https://www.onely.com/blog/is-google-ready-to-catch-up-with-javascript/)

Google [最近宣布](https://webmasters.googleblog.com/2019/05/the-new-evergreen-googlebot.html)他们的爬虫将运行最新版本的 Chromium，这引起了各种开发者的庆祝。也许所有大量使用 JavaScript 的网站都不再需要索引了？没那么快…仍然有很多原因让你想用 HTML 来呈现重要的内容。阅读这篇文章来了解其中的一些。

##### [涉足网络平台](https://rachelandrew.co.uk/archives/2019/05/07/getting-involved-with-the-web-platform/)

一系列简单的方法可以让你开始超越仅仅是网络平台的消费者，开始参与它的创造。喜欢抱怨 HTML、CSS 或 JavaScript 吗？把你的抱怨变成问题，参与到关于网络应该如何工作的讨论中来，让它们变得更好！

##### [图标:也许是最不为人所知的网络功能](https://dougsillars.com/2019/05/07/favicons-perhaps-the-least-understood-web-feature/)

有趣的是，看看人们是如何在网上使用(和误用)图标的。网站的图标大小超过 1MB？我甚至很难理解他们可能想做什么。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】