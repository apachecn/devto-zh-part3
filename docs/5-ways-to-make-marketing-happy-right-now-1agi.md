# 让营销快乐的 5 种方法！

> 原文：<https://dev.to/tammalee/5-ways-to-make-marketing-happy-right-now-1agi>

我职业生涯的将近一半时间都在为数字营销机构工作。在这种环境中，你会学到一些在软件或开发商店里学不到的东西。

以下是我列出的开发者现在可以做的 5 件让数字营销人员开心的事情:

1.  电话链接格式正确！
2.  没有 JavaScript 错误。
3.  语义 HTML。
4.  每个“页面”都有一个可以直接链接的唯一 URL
5.  表单提交触发提交事件或重定向到感谢页面。

### 电话联系

如果你正在构建一个应用程序、网站或 web 应用程序，电话链接应该是默认的。给你，[直接来自谷歌](https://developers.google.com/web/fundamentals/native-hardware/click-to-call/):

> 1.  Wrap all phone numbers in hyperlinks with tel: schema.
> 2.  Always use the international dialing format.

这是一个电话链接应该有的样子:
`<a href="tel:+1-303-499-7111">+1 (303) 499-7111</a>`

*   它是一个锚标记，而不是一个按钮。
*   `href`属性值以`tel:`为前缀，没有空格或括号。
*   这是直接的语义 HTML，这里没有带有`e.preventDefault()`的`onClick`事件。

### 语义 HTML

说到语义 HTML...用它！[这是一个很好的介绍](https://internetingishard.com/html-and-css/semantic-html/)为什么你会想使用语义 HTML，这里有一个关键的报价:

> 要理解为什么这很重要，我们真的必须与阅读我们内容的机器产生共鸣。

像网络爬虫这样的机器？蜘蛛？不管谷歌、必应和雅虎等公司如何称呼他们从网页中读取内容并试图找出如何对其进行排序的过程。是啊。那些机器。

通过制作机器更容易阅读的网页，你正在为你的客户的数字营销成功做出贡献。

### 没有 JavaScript 错误

这是一个大的！如今，精明的数字营销人员将使用谷歌标签管理器(GTM)来跟踪网页或应用程序上的东西。GTM 是基于 JS 的，并被注入到您的页面中。如果页面上的 JS 中有一个错误阻止了其他 JS 的执行，那么 GTM 事件和触发器就会消失。

对于任何通过 GTM 注入的 JS 也是如此！这只是开发和营销沟通如此重要的众多原因之一。

### 唯一网址

大多数网页都有自己的 URL，但是随着 JS 构建的网站和单页面应用程序的流行，我们有时会看到页面内容被替换，而 URL 却没有相应的变化。这是淘气——从数字营销的角度来看是淘气！

例如，数字营销人员如何从活动广告链接到登录页面？

这是一篇旧文章，但当思考这些事情时，这是一个很好的起点。可能有点过时了(什么时候出版的我一点线索都没有。)但是它有丰富的知识，包括如何测试你的基于 JS 的网站/应用是否可以被 Google 抓取。
[JavaScript 框架 SEO 基础知识](https://builtvisible.com/javascript-framework-seo/)

### 表单提交

我把最令人沮丧和最困难的跟踪工作留到了最后；跟踪表单提交。

我花了几个小时试图让 GTM 检测表单提交，但没有用，但不要让我 lob 轶事证据在你身上，学习大师 Simo Ahava:
[跟踪与谷歌标签管理器](https://www.simoahava.com/analytics/track-form-engagement-with-google-tag-manager/#2-why-the-form-trigger-doesn-t-always-work)的表单参与

他的优秀文章的 TLDR 版本是只需要两件事就可以让 GTM 检测到表单提交:

> 1.  The form must schedule a valid submit browser event.
> 2.  The event must not be prevented from propagating to the document node.

简单的柠檬榨汁机，对吗？不对！这是一个困难的柠檬！很明显，柑橘的挫败感太严重了，我不得不使用全大写字母！

(我的白头发中至少有 23 根是因为试图跟踪表格提交。)

当我们试图将数字营销牢记在心时，我们可以创造出很酷的东西，他们可以跟踪用户对这些很酷的东西的参与度！我们都赢了，每个人都很开心！

*米在 Unsplash 上的照片*