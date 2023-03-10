# Firefox DevTools - JavaScript 调试器的 5 个提示和技巧

> 原文：<https://dev.to/lakatos88/5-tips-and-tricks-for-firefox-devtools-javascript-debugger-3mc6>

*这是[系列](http://alexlakatos.com/devtricks/)的第三篇文章，这是一系列帮助你用 Firefox Devtools 调试 web 应用程序的技巧和诀窍。它向你展示了在 Firefox 开发工具中使用 JavaScript 调试器的 5 个技巧&。*

## 设置条件断点

您可以在 Firefox Developer Tools JavaScript 调试器中通过右键单击行号来设置条件断点。只有满足条件时，断点才会激活并暂停执行。这对于调试间歇性的错误和暂停特定值和范围的执行非常方便。

[![Set Conditional Breakpoints](img/a9fefdabd992fe2cee7e79237ea8ef5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JW9iBMQl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://alexlakatos.com/img/posts/devtricks/debugger/debugger-conditional-breakpoint.gif)

## 按函数名或行号搜索

在 Mac 上按`CMD` + `P`(在 Windows 和 Linux 上按`CTRL` + `P`，可以通过 JavaScript 调试器搜索文件名。如果你想在那个文件里面搜索一个函数或者直接去一个行号，还有其他的快捷键，但是我总是忘记它们。相反，你可以在同一个搜索框中输入`@`,它会寻找一个函数定义。如果您用`:`开始搜索，将会转到特定行。

[![Search by Function Name or Line Number](img/44883b908ee98b387cb2e561729ad658.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3aKYZUHL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://alexlakatos.com/img/posts/devtricks/debugger/debugger-search.gif)

## 漂亮的打印缩小码

你可以通过点击`{}`图标在 JavaScript 调试器中打印出缩小的代码。我大多是在制作或者别人的网站上尝试做一点调试的时候用，没有源码图可用。

[![Pretty Print Minified Code](img/6b2301c044b8ca10c6f9b06d032b7962.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z-hzvLTq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://alexlakatos.com/img/posts/devtricks/debugger/debugger-pretty-print.gif)

## 设置 URL 断点

您可以在 Firefox 开发工具 JavaScript 调试器中设置 URL 断点。当页面尝试访问与您设置的规则匹配的 URL 时，断点将变为活动状态并暂停执行。当网络请求发生时，这对于评估应用程序的状态非常方便，并且可以精确定位触发网络请求的代码行。

[![Set URL Breakpoints](img/c15d9ccab2d722ad2ae909d3fb083d0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XO8INGl---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://alexlakatos.com/img/posts/devtricks/debugger/debugger-url-breakpoint.gif)

## 禁用断点

您可以在 Firefox Developer Tools JavaScript 调试器中禁用和重新启用断点。它们将保持设置，但变为非活动状态，并且不会暂停代码的执行。在调试 if/else 子句时，这确实非常方便，因为您不必记得上次将调试点放在了哪里。

[![](img/bf0f9b8e803cd33f7a39f42f887a893c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lns_AhRw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://alexlakatos.com/img/posts/devtricks/debugger/debugger-disable-breakpoint.gif)

## 这是每周的事

这是一个每周一次的系列，这些年来我已经收集了很多技巧。如果你喜欢这个或者想看更多，我是推特上的[@ Lakatos 88](https://twitter.com/lakatos88)，跟我去吧，那里是[魔法发生的地方](https://twitter.com/lakatos88/status/1122092752542621696)。