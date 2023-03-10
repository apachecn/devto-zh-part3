# 您不知道可以从开发人员控制台完成的 5 项任务

> 原文：<https://dev.to/256kilobytes/5-tasks-you-didn-t-know-could-be-done-from-the-developer-console-oo6>

*注意:这篇文章的[原始/规范版本包括一些通过脚本标签嵌入网页【控制台】的演示。](https://www.256kilobytes.com/content/show/10378/5-tasks-you-didnt-know-could-be-done-from-the-developer-console)*

正如他们所说的，没有人会使用任何软件功能的 10%以上，但是每个人都使用不同的 10%。

类似于 [HTML 和 CSS](https://www.256kilobytes.com/content/show/4399/get-these-dependencies-off-my-lawn-5-tasks-you-didnt-know-could-be-done-with-pure-html-and-css) ，以及 [Google Sheets](https://www.256kilobytes.com/content/show/4429/spit-in-the-face-of-god-five-tasks-you-didn-t-know-could-be-done-with-google-sheets) ，浏览器的开发者控制台有很多你可能不知道的技巧可以使用。

## [T1】简介](#intro)

对于不熟悉控制台的人来说，它是一个内置于各种浏览器开发工具中的调试工具。这里有一个速成班:

*   右键单击->检查元素
*   从刚刚打开的窗口中，找到标签为“控制台”的选项卡；不同浏览器的布局有所不同
*   在控制台中键入以下内容，然后按 enter 键:
    *   `console.log("Some Text");`

恭喜你。您是一名黑客，可以跟随本文的其余部分。

## 自定义 console.log 的 CSS()

标准的 console.log()语句如下所示:

*   `console.log("asdf")`

用%c 启动 console.log()命令允许将任意 CSS 作为第二个参数注入，就像下面这个命令所做的那样:

```
console.log("%c adsf", "color:blue;font-size:600%;")

// This command also works (space not required):
console.log("%cadsf", "color:blue;font-size:600%;")

// Which allows you to do shit like:
console.log("%cGOOD JOB OPENING THE CONSOLE", "color:brown;font-weight:bold;font-size:600%;"); 
```

## 编辑网页上的任何内容

有时候，你必须编辑网页以进行调试，或者制作假截图。虽然您可以从开发人员控制台很容易地做到这一点，但可以使用[使内容可编辑的](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Editable_content)[content editable 属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable)使这变得更加容易。在开发人员控制台中输入以下代码行，使整个页面可编辑:

*   document . body . content editable = true

## 控制台.表()

类似于 console.log()，[console . table()函数](https://developer.mozilla.org/en-US/docs/Web/API/Console/table)可用于在开发人员控制台中显示任意数据。该函数接受数组、对象、JSON 和其他值，可用于显示数据表。例如，这个:

```
console.table({"Idaho":1751000, "Texas":28700000, "Maine":1338000}) 
```

将显示作为 JSON 数据传递给函数的键值对的表。

类似地， [console.group()](https://developer.mozilla.org/en-US/docs/Web/API/Console/group) 、 [console.groupEnd()](https://developer.mozilla.org/en-US/docs/Web/API/Console/groupEnd) 和[console . group collapsed()](https://developer.mozilla.org/en-US/docs/Web/API/Console/groupCollapsed)可以用来更好地组织输出到控制台的调试消息。

## 数据提取

例如，如果您想要从网页中提取所有 URL，您可以使用以下命令:

```
var t = document.querySelectorAll("a");
var output = {};
for (var iii = 0; iii < t.length; ++iii) {
output[ iii ] = t[iii].getAttribute('href');
} console.table(output); 
```

或者，如果您想更进一步，可以使用这个命令获得一个包含 innerHTML 和 rel 数据的表:

```
var t = document.querySelectorAll("a");
var output = {};
for (var iii = 0; iii < t.length; ++iii) {
output[iii] = {"href":t[iii].getAttribute('href'), "rel":t[iii].getAttribute('rel'), "innerHTML":t[iii].innerHTML};
} console.table(output); 
```

### 速记数据提取

由于您可以从控制台获取的内容基本上有无限的可能性，所以了解 querySelector 和 querySelectorAll 方法的简写是很有用的。具体来说:

*   $('a ')与 document.querySelector('a ')相同
*   $$('a ')与 document.querySelectorAll('a ')相同，除了 [$$返回数组而不是节点列表](https://gomakethings.com/dev-console-trick-selector-helpers/)

## 删除 CSS 和其他头文件

*head* 标签可以用来包含各种有用的信息，也可以用来注入垃圾。如果您在其他一些网站上，并且想要删除它们的样式表、JavaScript 和其他标题，您可以通过从控制台执行以下命令来实现:

```
document.head.parentNode.removeChild(document.head); 
```

在其他事情中，这可以用来防止那些“请每月支付 1 美元阅读 D 层绒毛故事”的消息在各种小报上的初始页面加载后的几秒钟内加载。

## 优秀奖

*   类似于 console.log()， [console.info()](https://developer.mozilla.org/en-US/docs/Web/API/Console/info) ， [console.warn()](https://developer.mozilla.org/en-US/docs/Web/API/Console/warn) 和 [console.error()](https://developer.mozilla.org/en-US/docs/Web/API/Console/error) 可用于向控制台显示不同重要性级别的数据。
*   愚蠢的狗屎，就像在饼干点击器里自动舔饼干一样。
*   [console.time()](https://developer.mozilla.org/en-US/docs/Web/API/Console/time) 和 [console.timeEnd()](https://developer.mozilla.org/en-US/docs/Web/API/Console/timeEnd) 可以用来计时性能。
*   用`$_`参考上次输入的结果。例如:
    *   在控制台中输入“`10*12`
    *   然后在提交该命令后输入`$_`作为引用该结果的快捷方式。
*   你可以(至少在 FireFox 和 Chrome 中)通过设置菜单使用深色主题代替浅色主题。[哈哈 LMAO DAE 用黑暗主题？！](https://www.clipartmax.com/middle/m2H7K9G6m2N4N4H7_gun-in-mouth-emoji/)

## 总之

恭喜你。您现在知道了一些以前不知道的调试技巧，除非您知道。现在你可以忽略它们继续使用 alert("asdfjla ")。

欢迎在下面评论更多关于 muh Safari 的技巧和/或投诉。