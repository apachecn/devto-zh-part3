# Browsersupport 博士:或者说我是如何学会不再担心并瞄准常青树浏览器的

> 原文：<https://dev.to/invot/dr-browsersupport-or-how-i-learned-to-stop-worrying-and-target-evergreen-browsers-1n4m>

作为一个开发者，你的作品就是你的艺术，你想和全世界分享。经过无数个小时，你打磨和完善你的作品。真是*美*。*准备好了*。

这就是为什么没有比当客户打开你项目的链接时，随着快速的*噗的一声，*他们正盯着你的*巨著*的混乱再现更糟糕的感觉了。你们两个都想知道为什么会发生这种情况，但是你们很快就明白了:不管是什么原因，你的客户都没有切断与互联网浏览器*的可怕联系。他们后来提到，这个网站在他们侄子的电子鸡上看起来也“关闭”了。*

作为开发人员，我们走的是一条微妙的钢丝:**我们希望构建丰富、前沿的体验，同时邀请所有人参加聚会**。在一个消费者不再确定[电脑是什么](https://www.theatlantic.com/technology/archive/2015/07/what-is-a-computer-anymore/399014)的世界里，人们可以从他们的[镜子](https://www.twowaymirrors.com/smart-mirror)，手表，甚至他们的[鞋子](https://www.fastcompany.com/3056820/this-comfy-shoe-doubles-as-a-footstep-powered-wi-fi-hotspot)访问网络，我们如何确保我们为每个人提供最好的体验？我们怎样才能礼貌地告诉那些坚守在布满灰尘的 386 塔楼上的人，我们需要继续前进？

对于大多数开发者来说，[跨浏览器测试](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing)的概念并不新鲜。最近几年，随着网页渲染引擎在如何操作上基本达成共识，避免这一任务变得更加容易。在 Chrome 中正确渲染的内容在 Firefox、Edge 和 Safari 中可能会呈现相同的效果(无论如何在 OSX 上)。这是因为所有这些浏览器都有一个共同的特点:它们是 T2 的常青树。

> 术语“常青树”指的是发布策略。Evergreen 浏览器在后台频繁更新，**不断调整它们与最新的** [**网络标准**](https://en.wikipedia.org/wiki/Web_standards) 的兼容性，还增加了专有功能。evergreen 浏览器的版本失去了它的重要性，因为 evergreen 浏览器应该在最新版本上运行。
> 
> [*什么是常青树浏览器？*](http://www.adam-bien.com/roller/abien/entry/what_is_an_evergreen_browser)*——亚当·比恩*

让你的浏览器[保持最新](https://www.whatismybrowser.com/guides/how-to-be-safe-online/why-should-i-update-my-web-browser)的原因是[广泛](https://computer.howstuffworks.com/update-internet-browser.htm)。然而，有些人可能浏览 IE9 或 Netscape 的怀旧镜头有几个原因:他们要么不知道他们*需要*更新，要么他们不知道*如何更新。他们访问的大多数网站可能仍在运行，并且可能没有提供任何出错的线索，尽管控制台在幕后悄悄地充满了错误。没有人告诉他们如何或者为什么应该使用更好的浏览器！*

开发与旧浏览器兼容的现代 web 应用程序令人钦佩，但它最终会让您的项目停留在被遗忘已久的 MySpace 时代。**新技术的存在让我们可以利用它们**，而不仅仅是用 polyfills 来补充它们的功能。

## 提示更新

网络使用统计显示，绝大多数网络用户已经采用了常青树浏览器。对于台式机用户来说，所有新机都预装了 Evergreen 浏览器。对于移动设备，它们是唯一的选择。事实上，截至 2019 年 1 月，不自动更新的浏览器占 web 流量的[不到 2%。](https://www.w3schools.com/browsers/browsers_explorer.asp)

但是，这 2%应该不会这么快被刷掉。重要的是我们要为每个人建立一个更好的网络。因此，对于任何运行不受支持的浏览器的人来说，通知用户升级是很重要的。

通过*提示用户你的应用程序在不受支持的浏览器中不能正常工作*，或者通过直接*用提示*阻止他们访问，你在需要存在的沙子上画了一条硬线:世界在不断变化。万维网的变化甚至更快。要么更新，要么落后。不这样很危险。

## 浏览器嗅探

[浏览器嗅探](https://developer.mozilla.org/en-US/docs/Web/HTTP/Browser_detection_using_the_user_agent)(也称为浏览器检测)是一套[技术](https://dev.to/_elmahdim/safe-reliable-browser-sniffing-39bp)，用于确定访问者正在使用的网络浏览器，并向访问者提供适合浏览器的内容。尽管这种做法仍然有些普遍，**它的有用性是非常青树浏览器独有的**。

年长的开发人员可能还记得最常用的浏览器检测方法。IE 允许开发者使用[条件注释](https://www.quirksmode.org/css/condcom.html)来定位旧版本。当时，这是一个很好的解决版本之间存在的大量不一致的方法。

```
<!DOCTYPE html>
<!--[if IE 8 ]> <html class="ie8"> <![endif]-->
<!--[if IE 9 ]> <html class="ie9"> <![endif]--> 
```

上面的例子将一个条件类应用于`HTML`元素，允许您加载样式表、脚本和其他针对特定版本浏览器的资源。

## 特征检测

谢天谢地，网络现在是一个非常不同的地方。在较新的浏览器中，版本控制的意义要小得多。开发人员不会为特定的浏览器编写应用程序。相反，他们采用特定的技术或功能。只有支持这些功能的浏览器才能正常运行它们的软件。

[功能检测](https://www.html5rocks.com/en/tutorials/detection/)(也称功能测试)是一种用于处理运行时环境(通常是 web 浏览器或用户代理)之间差异的技术，通过[有计划地测试环境是否提供某些功能的线索](https://modernizr.com/)。与浏览器嗅探和特定于浏览器的 CSS 攻击等其他技术相比，功能检测无疑更可靠，更经得起未来考验。

```
if ("geolocation" in navigator) {
  navigator.geolocation.getCurrentPosition(function(position) {
    // show the location on a map, perhaps using the Google Maps API
  });
} else {
  // Give the user a choice of static maps instead 
  // Or prompt them to upgarde their browser
} 
```

功能检测允许软件清点其使用的技术，允许适度降级，并可以提供用户当前环境中不支持某些东西的详细原因，以及如何解决该问题的详细步骤。

## 最后的话

很难把你的作品从特定的观众那里拉出来，促使他们转而坚持你的标准。强迫用户对他们的系统做些什么感觉非常……*不成熟*。但这一点的重要性是至关重要的:聚合填充只能做到这一步，而安全问题在较老的浏览器中比比皆是。给用户一张使用危险过时技术的免费通行证，对我们所有人来说都是一个沉重的代价。在非现代浏览器中运行一个现代网站所需的额外代码量会使其占用空间加倍，并降低每个人的体验——即使是那些使用现代浏览器的人。让我们利用传统上为 IE 的[奇怪的网络技术实现](https://github.com/philipwalton/flexbugs)而哭泣的时间，取而代之的是*开发*。

## 资源

*   Modernizr 是一个流行的特性检测库，它告诉你用户的浏览器提供了哪些 HTML、CSS 和 JavaScript 特性。

*   [Feature.js](http://featurejs.com/) 是另一个用普通 JavaScript 编写的快速、简单、轻量级的浏览器特征检测库。

*   [has.js](https://github.com/phiggins42/has.js/) 是另一种流行的特征检测替代方案。

*   [MDN 的](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection) Web Docs 提供了一个关于如何在您的项目中实现特性检测的很好的教程。

*   [feature-detect](https://www.npmjs.com/package/feature-detect) 是一个节点包，为 Node.js 或 Io.js 兼容环境提供特性检测支持。它并不打算在浏览器环境中提供功能检测支持。