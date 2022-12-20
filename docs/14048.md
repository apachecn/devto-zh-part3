# 如何在模态中捕获焦点？

> 原文：<https://dev.to/islam/how-to-trap-focus-inside-a-modal-2791>

[![](img/88d3dff97af4731262e5cf5ee22ff366.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Lf0MYS5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/720/0%2APw1mWlzR9TtF4VvF)

捕获焦点是关于可访问性的重要问题之一。对于辅助技术(如屏幕阅读器)用户或键盘用户，捕获焦点是必须的。

我们将讨论如何在一个模态打开时捕获焦点，并在它关闭时将焦点转移到页面的其余部分。

* * *

让我们看看这里有什么。

*提示:如果您只想要代码片段，只需转到“陷阱聚焦方法”一节。*

**内容:**

1.  术语。
2.  tabindex 属性。
3.  元素.焦点()
4.  陷阱聚焦法
5.  资源。

* * *

#### *T3】术语:*

根据[ally . js](https://allyjs.io/what-is-focusable.html)库，HTML 元素可以是以下五个类别中的一个:

1.惰性:元素不交互，因此不可聚焦。并且不应该由脚本聚焦(除非在极少数情况下)。

2.**可聚焦:**元素可以被脚本`Element.focus()`聚焦，也可能被鼠标(或指针)聚焦，但不能被键盘聚焦。

3. **Tabbable:** 元素是键盘可聚焦的(“Tabbable”)，因为它是文档顺序聚焦导航顺序的一部分。该元素也可以通过脚本和鼠标(或指针)聚焦。需要注意的是，这种导航顺序与 DOM 顺序相同。

4. **Only Tabbable:** 元素只能通过键盘聚焦，可能是通过鼠标(或指针)，但不能通过脚本聚焦。

5.**转发焦点:**该元素会将焦点转发到另一个元素，而不是接收焦点本身。

这是一个浏览器兼容性的表格，包括焦点元素和标签元素。它显示了哪些元素是可制表的、可聚焦的或惰性的，以及它与不同浏览器的兼容性。

* * *

#### *T3】tabindex 属性:*

[![](img/bdfe23659e39d1282987fbbce34c6632.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nE6_DINJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/720/0%2AL8MSwMvmKl_QKQ9G)

属性 tabindex 是一个全局 HTML 属性。它决定哪个元素可以被聚焦，并把它放在连续的聚焦导航顺序中(或者简单地按 tab 键顺序)。

它的值可以是以下三个值之一:

1.**负值:** `tabindex="-1"`从 tab 键顺序中删除元素，但它可以被脚本聚焦。

2.**零值:** `tabindex="0"`把元素放在 tab 键中排序，其顺序受 to DOM 结构的影响。请注意，可视(CSS)结构不影响 tab 键顺序。因此，有一个相似的 viasual 和 DOM 顺序是一个很好的实践，不会让键盘用户感到困惑。

3.**正值:** `tabindex="1"`表示该元素在顺序键盘导航中应该是可聚焦的，其顺序由数字的值以升序方式定义。所以`tabindex="2"`将先于`tabindex="3"`。这是反模式的，应该避免，因为您最终会在元素之间跳转，这会导致混乱。

* * *

#### ***【元素】:***

[![](img/b0dd1bb9db16415f18ab0595fadcce51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BwogbE3c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/720/0%2A560i5u5CYDL89-7u)

它将焦点设置在指定的元素上。这并不意味着它在 tab 键顺序中退出元素，而是在某个事件中使用脚本将焦点拖到元素上。它可用于聚焦或非聚焦元素。

*语法:*

`Element.focus([option])`

可选的焦点选项是一个**布尔**值。

If `false`方法将元素滚动到浏览器窗口的可视区域。

If `true`(默认)方法不会将元素滚动到浏览器窗口的可见区域。

* * *

#### ***陷阱聚焦法:***

激动人心的部分来了。在这篇文章中，我们将使用 JavaScript 解决在一个模态中捕获焦点的问题，当然，这不是唯一的方法。

*这个简单的页面示例包含:*

***签到按钮*** 。事实上这不是一个按钮。它只是一个 div，就像一个按钮。这也是我给它添加 tabindex="0 "属性的原因。

***表单*** 由一个文本框和一个按钮组成。

***模态*** 当你点击签到按钮或者当它被聚焦时按回车键弹出。它包含:

***两个文本框*** 。一个用于用户名，另一个用于密码输入。最后是登录按钮。

[https://codepen.io/islam8/embed/BPvazR?height=600&default-tab=js,result&embed-version=2](https://codepen.io/islam8/embed/BPvazR?height=600&default-tab=js,result&embed-version=2)

所需的行为是，当模式打开时，焦点被捕获在其中，直到模式关闭时才到达其他元素。

我做了什么？

1.  当登录按钮被点击或按下“Enter”键时，我打开了 modal，从 tab 键顺序中删除了 modal 之外的元素，并开始关注 modal 的第一个可聚焦元素。

2.  在 modal I 中，我负责第一个和最后一个可聚焦的元素，以确保当用户到达最后一个元素时，例如，第一个元素将成为下一个标签的目标，当用户在第一个元素上按“shift+tab”时，它将把焦点带到最后一个元素。所以只要焦点是打开的，它就被困在模态内部。

3.  当用户在登录按钮上点击或按“Enter”时，模态消失，所有其他元素再次返回焦点。

* * *

#### *T3】资源:*

如何获取 DOM 中第一个和最后一个可聚焦的元素。
[可聚焦元素—浏览器兼容性表。](https://allyjs.io/data-tables/focusable.html)
[可聚焦是什么意思？](https://allyjs.io/what-is-focusable.html)
[html element . focus()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus)
[element . remove attribute()](https://developer.mozilla.org/en-US/docs/Web/API/Element/removeAttribute)
[NodeList。](https://developer.mozilla.org/en-US/docs/Web/API/NodeList)
[使用 JavaScript 创建键盘快捷键。](https://medium.com/@melwinalm/crcreating-keyboard-shortcuts-in-javascripteating-keyboard-shortcuts-in-javascript-763ca19beb9e)
[根据属性值查找元素。](https://stackoverflow.com/questions/2694640/find-an-element-in-dom-based-on-an-attribute-value#16775485)