# CSS 和 JS 开战了，下面是阻止的方法

> 原文：<https://dev.to/evilmartians/css-and-js-are-at-war-heres-how-to-stop-it-158a>

**TL；博士:**有很多人喜欢**JS 和**UX/CSS/等等。如果我们不再仅仅给人们贴上“JS 开发者”或“UX 开发者”的标签，我们就可以在当前的“JS vs. CSS”战争中实现停火，更接近和平。

## 这场战争是真实的

一些人称之为大分水岭:前线是真实的，一方是 JavaScript 的死硬派，另一方是提倡非 JS 接口方法的 UX/CSS 人员。

前端开发人员害怕失去他们的工作，如果他们避免整个 JavaScript 炒作。这是完全可以理解的:CSS 已经过时了。与 JS/React 和 friends 相比，CSS 会议和 meetups 要少得多。例如，在纽约有 6 个以上的 JS 聚会，没有定期的 CSS 聚会。

另一方面，我们看到简单的静态网站被过度设计成纯粹的 FOMO。

我们每天都看到前端社区中的杰出人物相互推卸责任，这至少可以说是不幸的。

## 目光超越

交战的派别经常被贴上这样的标签:

1.  **"JS-JS-JS"** :用 React、Vue.js、Angular 之类的客户端 JavaScript 框架创建 SPA 的开发者。他们是无数构建工具(Babel、webpack 等)的重度用户。)和 JS 库。
2.  **“UX 开发者”、“CSS 开发者”、“HTML-JS-CSS 开发者”**:用香草 JavaScript 和普通 CSS 创建静态网站的开发者。可访问性和性能是他们社区中最重要的话题。

但是我们真的需要这样的分裂吗？也许这种二元论仅仅是基于我们自己的偏见？

在我看来，这种偏见很大程度上是由两件事造成的。

首先，CSS 和 JavaScript 会议有分离的趋势。我认为它是由一个非常受欢迎和成功的 JSConf/CSSConf 系列活动以及 Put-Your-Own-City-here . js meetups 的趋势开始的。内容平台也支持这种划分:其中一些主要发布 React/JS 文章，另一些专注于 CSS 和 UX。

其次，社交网络擅长[分化社会](http://www.pewresearch.org/fact-tank/2016/01/27/the-demographic-trends-shaping-american-politics-in-2016-and-beyond/)。通过订阅他们的订阅，我们把自己置身于一个由志同道合的人组成的泡沫中，而通过转发来自另一方的最具攻击性的观点，事情变得更糟。

现代网络极其复杂。掌握驱动它的所有技术是极其困难的，没有人能真正称自己是 100%的“全栈”开发者。但是，由于 JS 和 CSS/UX 话语被如此(人为地)分开，具有不同但不一定对立的激情的人们被推入了一个非黑即白的“JS 对 CSS”的世界观。对 CSS 动画和 [a11y](https://en.wikipedia.org/wiki/Computer_accessibility) 充满热情的 React 开发者被简单的贴上了“JS 乡亲”的标签。而一个热爱 Babel 和 zero-runtime CSS-in-JS 的 CSS 开发者，还是会被画成“CSS guy/gal”。

## 爱他们俩的人

作为一个 PostCSS 的创造者，我从来没有真正选择过一方，即使我想。一方面，PostCSS 是 CSS 的工具(因此得名)。另一方面，PostCSS 是一个 **JavaScript** 构建工具，而构建工具在现代 CSS 社区中并没有被很好地接受。

我并不孤单，有很多人和我一样:一个令人惊叹的动画工具箱的创造者，或者一个 [CSS a11y linter](https://github.com/YozhikM/stylelint-a11y) 的创造者，等等。

说实话，我们每个人只知道现存技术的一小部分。一个人的激情也不一定来自一个单一的话题。既爱 React 又爱 CSS 是可以的。或者使用复杂的构建系统来确保你的一切都是正确的。或者你可以深入分布式系统，因为你想在糟糕的网络连接下创造伟大的 UX。

甚至技术本身也不能黑白分明。

“CSS 派”的支持者经常提到 BEM ,以此来避免 CSS-in-JS 可能出现的混乱。但是很少有人知道它不是由 [Yandex](https://yandex.com/company/) 作为一个纯粹的 CSS 技术设计的！它还包含一个 JavaScript 框架，最初有一套后来在 React 中使用的想法(比如嵌套小的隔离组件)。

在 React 社区流行的 ESLint 配置(像 [AirBnB 配置](https://www.npmjs.com/package/eslint-config-airbnb))包含[很多 a11y 规则](https://github.com/evcohen/eslint-plugin-jsx-a11y#supported-rules)。

## 解

我认为战争是真实的。我认为，如果我们不再把开发者分成非黑即白的类别，我们就可以停止这场战争。

1.  如果你喜欢两方面的技术:大声说出来！让它可见，这样人们就可以展开文明的讨论。你喜欢现代的 JS 框架，但也喜欢创建完全不涉及客户端渲染的静态网站吗？告诉全世界。如果开源作者认为有必要，他们会为静态网站创建更多的框架。
2.  让我们为 JS 和 CSS 世界之间的对话建立一个公共论坛。如果你正在组织一次 JavaScript 会议，请留出一天时间进行 CSS/UX 会谈。让我们举行“前端”会议，而不是“JS 会议”和“CSS 会议”，在那里来自不同阵营的人可以向他们的对手解释他们的日常问题和首选解决方案。
3.  让我们试试来自“另一边”的技术:
    *   如果你是 CSS/UX 开发者，从 linters 开始。从 Stylelint 开始就是一个很好的 CSS linter。它会警告您错误，并允许您在团队中分享最佳实践。你可以把它作为你最喜欢的文本编辑器的插件来运行，所以你甚至可以在没有任何捆绑器的情况下启动它。
    *   如果您是 React 开发人员，请在您的下一个登录页面或博客上尝试一些普通的 JS。这将让你更好地理解你的框架的内部。您的用户会感谢您使用更轻便的 JavaScript 包提高了性能。

## 进一步阅读

这是我在[火星编年史](https://evilmartians.com/chronicles/)上写的关于[post CSS、linters 和 CSS-in-JS](https://evilmartians.com/chronicles/five-years-of-postcss-state-of-the-union) 的文章。