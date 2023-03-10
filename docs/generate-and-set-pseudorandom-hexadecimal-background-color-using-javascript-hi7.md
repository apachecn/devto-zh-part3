# 使用 JavaScript 生成和设置伪随机十六进制背景色

> 原文：<https://dev.to/nickfazzpdx/generate-and-set-pseudorandom-hexadecimal-background-color-using-javascript-hi7>

我正在做一个项目，处理伪随机事件驱动的颜色变化，需要一个解决方案来生成供 web 使用的十六进制代码。

# 程序设计与思考:

我知道十六进制颜色代码代表以 16 为基数的红绿蓝值。它们由以下模式定义:

[![Hexadecimal number explanation](img/372e495e394fbd6e55044a2b55584783.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U1gVXLZ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://btsource.net/dev_to_images/rgb.png)

我开始考虑在实现中使用什么类型和数据结构。我提出了一个总体思路，用一个集合来定义基本集合，用一个通用规则来构建一个六值十六进制颜色代码。

*   **其中 H 是十六进制值的集合** -这些将被定义为字符串。
*   **设置“十六进制颜色代码”** -使用来自 JavaScript 的操作符`+=`，连接来自 *H* 的一系列值

[![General outline of how to build hex codes](img/d313d5b71ab68a1c5c0cbba3f96e2341.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SColUi1n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://btsource.net/dev_to_images/hexset.png)

在这一点上，我能够开始写一些代码。我将让代码在下面的嵌入中自己说话。

# 讨论与洞察:

作为一名计算机科学学生和十足的书呆子，我对这段代码有一个疑问。

*   这些值可以完全用数字生成，然后在生成后解析为字符串吗？我知道在 C/C++ *(我的编码舒适区)*你可以这么做。以我目前对 JavaScript 的了解，我还没有找到一种方法。

任何其他见解或意见将不胜感激。

# 代码

[https://codepen.io/NickersF/embed/QzpYGK?height=600&default-tab=js,result&embed-version=2](https://codepen.io/NickersF/embed/QzpYGK?height=600&default-tab=js,result&embed-version=2)

# 更进一步:

在我实现了这个之后，我开始考虑用十六进制颜色做更复杂和潜在有用的事情。例如，一个人可以建立在这个想法的基础上，以这样一种方式实现代码，只有`R`、`G`或`B`被生成，或者是三者的某种组合。

无论如何，我希望由此引发一些讨论，这个帖子很有趣。

***如果你喜欢这个内容，并且想资助一个贫穷的自由职业学生，这样我就可以在未来吃东西和制作更多的内容，我接受通过[PayPal](https://paypal.me/btsourcepdx)**T5】的捐款*