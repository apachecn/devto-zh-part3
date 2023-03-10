# 你不知道的 8 个 DOM 特性

> 原文：<https://dev.to/bnevilleoneill/8-dom-features-you-didn-t-know-existed-2jl3>

[![](img/adc47d1107d671e3d3a29569bbf1991b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dMNS9DB5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARhIzOR1ybtL46CLAICssOg.png)

随着最近对工具的关注越来越多，从所有的 React 和 npm-install-everything 帖子中休息一下，仔细看看一些在现代浏览器中工作的纯 DOM 和 Web API 特性是很好的。

这篇文章将考虑八个鲜为人知的 DOM 特性，它们具有强大的浏览器支持。为了帮助解释每一个是如何工作的，我将包含许多交互式演示，供您自己尝试代码。

这些方法和属性没有陡峭的学习曲线，并且将很好地与您碰巧引入到项目中的任何工具集一起工作。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

您肯定使用过 addEventListener()来处理将事件附加到 web 文档中的元素。通常，addEventListener()调用看起来像这样:

```
element.addEventListener('click', doSomething, false); 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数是我要监听的事件。第二个参数是事件发生时将执行的回调函数。第三个参数是一个名为 useCapture 的布尔值，它指示您是否希望使用[事件冒泡或捕获](https://www.sitepoint.com/event-bubbling-javascript/)。

这些都是众所周知的(尤其是前两个)。但是您可能不知道 addEventListener()也接受替换最终布尔值的参数。这个新参数是一个选项对象，看起来像这样:

```
element.addEventListener('click', doSomething, {
  capture: false,
  once: true,
  passive: false
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，该语法允许定义三种不同的属性。下面是每个词的意思的简要概述:

*   **捕获** —一个布尔值，与前面提到的[使用捕获参数](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)相同
*   **once** —一个布尔值，如果设置为 true，则表示事件只应在目标元素上运行一次，然后被删除
*   **passive** —一个最终布尔值，如果设置为 true，则表示该函数永远不会调用 preventDefault()，即使它包含在函数体中

这三个选项中最有趣的是 once 选项。这在许多情况下肯定会派上用场，并使您不必使用 removeEventListener()或使用其他复杂的技术来强制单个事件触发。如果您使用过 jQuery，您可能对该库中的一个类似特性很熟悉。one()方法。

您可以在下面的 CodePen 中尝试一些使用 options 对象的代码:
[https://codepen.io/impressivewebs/embed/GeJZYz?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/GeJZYz?height=600&default-tab=result&embed-version=2)
注意演示页面上的按钮只会追加一次文本。如果将 once 值更改为 false，然后多次单击按钮，则每次单击按钮时都会追加文本。

浏览器对 options 对象的支持非常好:除了 IE11 和更早的版本，所有的浏览器都支持它[,所以如果你不担心微软的浏览器，使用它是非常安全的。](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Browser_compatibility)

### scroll to()方法用于在窗口或元素中平滑滚动

平滑滚动一直是一种需要。当一个本地页面链接立即跳转到一个指定的位置时，这是不和谐的(如果你眨眼，你甚至可能错过跳转)。平滑滚动不仅好看，还能提高页面的 UX。

虽然过去已经使用 jQuery 插件完成了这项工作，但现在只需一行 JavaScript 代码就可以使用 window.scrollTo()方法。

scrollTo()方法应用于 Window 对象，告诉浏览器滚动到页面上的指定位置。例如，这里有一个语法最简单的例子:

```
window.scrollTo(0, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

这将使窗口向右滚动 0px(代表 x 坐标，或水平滚动),并在页面上向下滚动 1000px(垂直，这通常是您想要的)。但那样的话，滚动就不会是流畅的动画效果；页面会突然滚动，就像你使用一个指向指定散列 URL 的本地链接一样。

有时候这就是你想要的。但是为了获得平滑的滚动，你必须加入不太为人所知的 ScrollToOptions 对象，就像这样:

```
window.scrollTo({
  top: 0,
  left: 1000,
  behavior: 'smooth'
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码相当于前面的示例，但是在 options 对象中添加了 behavior 属性的平滑值。
[https://codepen.io/impressivewebs/embed/pGYXgj?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/pGYXgj?height=600&default-tab=result&embed-version=2)
尝试在框中输入一个数字(最好是 4000 这样的大数字)，并将“行为”选择框更改为“平滑”或“自动”(这是行为属性仅有的两个选项)。

关于此功能的一些注意事项:

*   对 scrollTo()的基本支持是全面的，但是并不是所有的浏览器都支持选项对象
*   当应用于一个元素而不是窗口时，这个方法也可以工作
*   这些选项也适用于 scroll()和 scrollBy()方法

### 带可选参数的 setTimeout()和 setInterval()

在许多情况下，使用 window.setTimeout()和 window.setInterval()制作基于时间的动画现在已经被性能更友好的 window.requestAnimationFrame()所取代。但是在某些情况下，setTimeout()或 setInterval()是正确的选择，所以了解这些方法的一个鲜为人知的特性是很好的。

通常你会看到这些方法的语法如下:

```
let timer = window.setInterval(doSomething, 3000);
function doSomething () {
  // Something happens here...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，setInterval()调用传入两个参数:回调函数和时间间隔。使用 setTimeout()时，这将运行一次，而在本例中，它将无限期运行，直到我在传入 timer 变量时调用 window.clearTimeout()为止。

很简单。但是如果我想让我的回调函数接受参数呢？嗯，这些定时器方法最近增加了以下功能:

```
let timer = window.setInterval(doSomething, 3000, 10, 20);
function doSomething (a, b) {
  // Something happens here…
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在 setInterval()调用中又添加了两个参数。然后，我的 doSomething()函数接受这些参数，并可以根据需要操作它们。

下面是一个 CodePen 演示程序，演示了如何使用 setTimeout():
[https://codepen.io/impressivewebs/embed/PgoNEj?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/PgoNEj?height=600&default-tab=result&embed-version=2)
当您单击按钮时，将会发生一个带有两个传入值的计算。这些值可以通过页面上的数字输入进行更改。

至于浏览器支持，这方面似乎有不一致的信息，但似乎所有正在使用的浏览器都支持可选参数功能，包括 IE10。

### [T1】单选按钮和复选框的 defaultChecked 属性](#the-defaultchecked-property-for-radio-buttons-and-checkboxes)

您可能知道，对于单选按钮和复选框，如果您想要获取或设置 checked 属性，可以使用 checked 属性，就像这样(假设 radioButton 是对特定表单输入的引用):

```
console.log(radioButton.checked); // true
radioButton.checked = false;
console.log(radioButton.checked); // false 
```

Enter fullscreen mode Exit fullscreen mode

但是还有一个名为 defaultChecked 的属性，它可以应用于单选按钮或复选框组，以找出组中的哪一个最初被设置为选中。

这里有一些 HTML 的例子:

```
<form id="form">
  <input type="radio" value="one" name="setOne"> One
  <input type="radio" value="two" name="setOne" checked> Two<br />
  <input type="radio" value="three" name="setOne"> Three
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这样，即使选中的单选按钮已经被更改，我也可以循环输入，找出最初选中的是哪一个，就像这样:

```
for (i of myForm.setOne) {
  if (i.defaultChecked === true) {
    console.log(‘i.value’);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个 CodePen 演示程序，它将显示当前选中的单选按钮或默认选中的单选按钮，这取决于您使用哪个按钮:
[https://codepen.io/impressivewebs/embed/qwWoOr?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/qwWoOr?height=600&default-tab=result&embed-version=2)
该示例中的默认选中选项将始终是“两个”单选按钮。如上所述，这也可以通过复选框组来实现。尝试更改 HTML 中的默认选中选项，然后再次尝试该按钮。

这是另一个对一组复选框做同样事情的演示:
[https://codepen.io/impressivewebs/embed/wZMJYQ?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/wZMJYQ?height=600&default-tab=result&embed-version=2)
在这种情况下，你会注意到其中两个复选框是默认选中的，所以当使用 defaultChecked 查询时，它们都将返回 true。

### 用 normalize()和 wholeText 操作文本节点

HTML 文档中的文本节点可能很挑剔，尤其是在动态插入或创建节点时。例如，如果我有下面的 HTML:

```
<p id="el">This is the initial text.</p> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我可以向段落元素添加一个文本节点:

```
let el = document.getElementById('el');
el.appendChild(document.createTextNode(' Some more text.'));
console.log(el.childNodes.length); // 2 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在追加了文本节点之后，我记录了段落中子节点的长度，它显示有两个节点。这些节点是单个文本字符串，但是因为文本是动态追加的，所以它们被视为单独的节点。

在某些情况下，如果将文本视为单个文本节点会更有帮助，这使得文本更容易操作。这就是 normalize()和 wholeText()的用武之地。

normalize()方法可以用来合并单独的文本节点:

```
el.normalize();
console.log(el.childNodes.length); // 1 
```

Enter fullscreen mode Exit fullscreen mode

对元素调用 normalize()将合并该元素中任何相邻的文本节点。如果碰巧有一些 HTML 散布在相邻的文本节点中，HTML 将保持原样，而所有相邻的文本节点将被合并。

但是，如果出于某种原因，我希望保持文本节点是独立的，但我仍然希望能够将文本作为一个单一的单元，那么这就是 wholeText 的用处所在。因此，我可以在相邻的文本节点上这样做，而不是调用 normalize()

```
console.log(el.childNodes[0].wholeText);
// This is the initial text. Some more text.
console.log(el.childNodes.length); // 2 
```

Enter fullscreen mode Exit fullscreen mode

只要我没有调用 normalize()，文本节点的长度将保持为 2，我可以使用 wholeText 记录整个文本。但是请注意一些事情:

*   我必须在一个文本节点上调用 wholeText，而不是元素(因此代码中的 el.childNodes[0];el.childNodes[1]也可以工作)
*   文本节点必须是相邻的，没有 HTML 分隔它们

在这个 CodePen 演示中，您可以看到这两个特性，以及正在使用的 splitText()方法[。打开 CodePen 控制台或浏览器的开发人员工具控制台，查看生成的日志。](https://codepen.io/impressivewebs/pen/ZZzrBV?editors=0011)

### insertAdjacentElement()和 insertAdjacentText()

许多人可能都熟悉 insertAdjacentHTML()方法，该方法允许您轻松地将文本或 HTML 字符串添加到页面中与其他元素相关的特定位置。

但是您可能没有意识到该规范还包括两个以相似方式工作的相关方法:insertAdjacentElement()和 insertAdjacentText()。

insertAdjacentHTML()的缺陷之一是插入的内容必须是字符串形式。所以如果包含 HTML，就得这样声明:

```
el.insertAdjacentHTML('beforebegin', '<p><b>Some example</b> text goes here.</p>'); 
```

Enter fullscreen mode Exit fullscreen mode

但是，对于 insertAdjacentElement()，第二个参数可以是元素引用:

```
let el = document.getElementById('example'),
addEl = document.getElementById('other');
el.insertAdjacentElement('beforebegin', addEl); 
```

Enter fullscreen mode Exit fullscreen mode

这个方法的有趣之处在于，它不仅会将引用的元素添加到指定的位置，还会将元素从文档中的原始位置移除。所以这是一种将元素从 DOM 中的一个位置转移到另一个位置的简单方法。

下面是一个使用 insertAdjacentElement()的 CodePen 演示。按钮 click 有效地“移动”了目标元素:
[https://codepen.io/impressivewebs/embed/MRjOpj?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/MRjOpj?height=600&default-tab=result&embed-version=2)T3】insertAdjacentText()方法的工作方式类似，但是所提供的文本字符串将被专门作为文本插入，即使它包含 HTML。注意下面的演示:
[https://codepen.io/impressivewebs/embed/MRymba?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/MRymba?height=600&default-tab=result&embed-version=2)
你可以将自己的文本添加到输入字段，然后使用按钮将其添加到文档中。请注意，任何特殊字符(如 HTML 标记)都将作为 HTML 实体插入，与 insertAdjacentHTML()相比，该方法的行为有所不同。

所有三个方法(insertAdjacentHTML()、insertAdjacentElement()和 insertAdjacentText())对第一个参数采用相同的值。这些论点是:

*   beforebegin:插入在调用该方法的元素之前
*   afterbegin:插入元素内部，第一个子元素之前
*   beforeend:插入到元素内部，在其最后一个子元素之后
*   afterend:插入在元素之后

### 事件.细节属性

正如已经讨论过的，使用熟悉的 addEventListener()方法将事件附加到 web 页面上的元素。例如:

```
btn.addEventListener('click', function () {
  // do something here...
}, false); 
```

Enter fullscreen mode Exit fullscreen mode

当使用 addEventListener()时，您可能需要防止函数调用中的默认浏览器行为。例如，也许您想拦截对 

```
btn.addEventListener('click', function (e) {
  // do something here...
  e.preventDefault();
}, false); 
```

Enter fullscreen mode Exit fullscreen mode

这使用 preventDefault()，它是老式 return false 语句的现代等价物。这需要将事件对象传递到函数中，因为 preventDefault()方法是在该对象上调用的。

但是您还可以对事件对象做更多事情。事实上，当使用某些事件时(例如 click、dbclick、mouseup、mousedown)，这些事件会公开一个叫做 [UIEvent 接口](https://developer.mozilla.org/en-US/docs/Web/API/UIEvent)的东西。正如 MDN 所指出的，这个界面上的许多特性都被否决或者没有标准化。但是最有趣和有用的是细节属性，这是官方规范的[部分。](https://w3c.github.io/uievents/#dom-uievent-detail)

下面是它在同一个事件监听器示例中的样子:

```
btn.addEventListener('click', function (e) {
  // do something here...
  console.log(e.detail);
}, false); 
```

Enter fullscreen mode Exit fullscreen mode

我已经设置了一个 CodePen 演示程序，它使用许多不同的事件来演示结果:
[https://codepen.io/impressivewebs/embed/QoZoQe?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/QoZoQe?height=600&default-tab=result&embed-version=2)
演示程序中的每个按钮都会以按钮文本描述的方式做出响应，并且会显示一条显示当前点击次数的消息。一些需要注意的事项:

*   WebKit 浏览器允许无限制的点击次数，除了 dbclick，它总是两次。Firefox 最多只允许点击三次，然后重新开始计数
*   我已经包括了模糊和聚焦，以证明这些不符合条件，并将始终返回 0(即没有点击)
*   IE11 等老浏览器的行为非常不一致

注意这个演示包括了一个很好的用例——模拟三次点击事件的能力:

```
btnT.addEventListener('click', function (e) {
  if (e.detail === 3) {
    trpl.value = 'Triple Click Successful!';
  }
}, false); 
```

Enter fullscreen mode Exit fullscreen mode

如果所有浏览器的点击次数都超过了三次，那么您也可以检测到更高的点击次数，但我认为对于大多数目的来说，三次点击事件就足够了。

### scroll height 和 scrollWidth 属性

scrollHeight 和 scrollWidth 属性可能听起来很熟悉，因为您可能会将它们与其他与宽度和高度相关的 DOM 特性相混淆。例如，offsetWidth 和 offsetHeight 属性将返回元素的高度或宽度，而不考虑溢出。

例如，注意下面的演示:
[https://codepen.io/impressivewebs/embed/WWxEpX?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/WWxEpX?height=600&default-tab=result&embed-version=2)
演示中的列有相同的内容。左边的列将溢出设置为自动，而右边的列将溢出设置为隐藏。offsetHeight 属性为每个属性返回相同的值，因为它不考虑可滚动或隐藏区域；它只测量元素的实际高度，包括任何垂直填充和边框。

另一方面，名副其实的 scrollHeight 属性将计算元素的完整高度，包括可滚动(或隐藏)区域:
[https://codepen.io/impressivewebs/embed/EJyvoB?height=600&default-tab=result&embed-version=2](https://codepen.io/impressivewebs/embed/EJyvoB?height=600&default-tab=result&embed-version=2)
上面的演示与上一个相同，只是它使用 scrollHeight 来获取每一列的高度。再次注意，两列的值是相同的。但这一次是一个高得多的数字，因为溢出区也被算作高度的一部分。

上面的例子关注的是元素高度，这是最常见的用例，但是您也可以使用 offsetWidth 和 scrollWidth，它们可以以同样的方式应用于水平滚动。

### 结论

这就是 DOM 特性的列表，这些可能是我在过去几年中遇到的最有趣的特性，所以我希望在不久的将来，这些特性中至少有一个是你能够在项目中使用的。

请在评论中告诉我你以前是否使用过其中的一个，或者你是否能为其中的任何一个想到一些有趣的用例。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子 [8 个你不知道存在的 DOM 特性](https://blog.logrocket.com/8-dom-features-you-didnt-know-existed-ec2a0a28fd89/)首先出现在[的 LogRocket 博客](https://blog.logrocket.com)上。