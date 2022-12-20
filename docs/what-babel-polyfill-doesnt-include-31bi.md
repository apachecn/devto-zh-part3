# babel-polyfill 不包括的内容

> 原文：<https://dev.to/michi/what-babel-polyfill-doesnt-include-31bi>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/what-babel-polyfill-doesnt-include)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

[babel polyfill](https://babeljs.io/docs/en/babel-polyfill) 通常用于模拟完整的 ES2015+环境，使用起来极其简单。你只需通过 npm 或 yarn 安装，然后导入到你的应用程序中。现在，您已经准备好编写下一级 JavaScript，而不必担心浏览器支持，是吗...？

原来还有很多巴别塔-波利菲尔没有包括的东西。

要知道 babel-polyfill 是在引擎盖下使用 [core-js](https://github.com/zloirock/core-js) 的。

缺少的聚合填充列表:

*   `JSON`仅在 IE7 中缺失
*   `String#normalize`因其使用罕见且体积较大而缺失。替代聚合填充: [unorm](https://github.com/walling/unorm/)
*   `Proxy`不能多填
*   `window.fetch`不是 ECMAScript 的一部分，而是一个 web 标准。虽然 core-js 确实包含了一些 web 标准，但是`fetch`目前并不在其中。替代 polyfill: [github 获取](https://github.com/github/fetch)
*   `Intl`因体积大而缺失。替代 polyfill: [Intl.js](https://github.com/andyearnshaw/Intl.js/)
*   [DOM polyfills](https://github.com/zloirock/core-js/issues/317#issuecomment-314691446) 。比如`element.closest`。但是包含的是[可迭代的 DOM 集合](https://github.com/zloirock/core-js#iterable-dom-collections)
*   `<script type="module">`。相反，使用像`webpack`或`rollup`这样的捆扎机

还要注意

*   [使用符号 polyfill 时的注意事项](https://github.com/zloirock/core-js#caveats-when-using-symbol-polyfill)
*   [使用类型化数组 polyfill 时的注意事项](https://github.com/zloirock/core-js#caveats-when-using-symbol-polyfill)
*   [使用 URL 和 URLSearchParams 时的注意事项](https://github.com/zloirock/core-js#caveats-when-using-symbol-polyfill)

如果你知道其他没有包括的东西，请留下评论，我会把它添加到列表中。

关于支持的(测试的)JavaScript 引擎列表，请参考[https://github.com/zloirock/core-js#supported-engines](https://github.com/zloirock/core-js#supported-engines)