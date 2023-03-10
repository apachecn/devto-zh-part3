# 哪些元素支持 shadow DOM？

> 原文：<https://dev.to/chromiumdev/which-elements-support-shadow-dom-lm9>

[![A dark building with several windows](img/e71cf7b29fef3e1491c154ff9a1c8f3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RmJRsPhc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/146bzbec2vu47jvvtf92.jpeg)

[奥利弗在推特上问](https://twitter.com/Oliver41618769/status/1084275850441355265):

> 是否有一个列表，列出了哪些 HTML 元素可以和不可以有一个影子 DOM？

事实证明，是有的！(非常感谢[安妮·范·卡斯特伦](https://annevankesteren.nl/)为[给我们指路](https://twitter.com/annevk/status/1084426928965238787?s=19)。

> *如果* [*上下文对象*](https://dom.spec.whatwg.org/#context-object)*[*局部名称*](https://dom.spec.whatwg.org/#concept-element-local-name) *是*不是*一个* [*有效的自定义元素名称*](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) *，* *条，* *抛开，* *blockquote，* *body，* *div，**段，或者* *跨度，那么* [*抛出*](https://heycam.github.io/webidl/#dfn-throw) *一个**notsupporterror**DOM exception。****

 *下面是一个使用 div 的快速示例:

[https://glitch.com/embed/#!/embed/shadow-dom-elements?path=script.js](https://glitch.com/embed/#!/embed/shadow-dom-elements?path=script.js)

### 异常情况

值得一提的是，button、input、select、img 和 a 不在这个列表中，如果您试图给它们附加一个影子根，它们会抛出一个错误。如果你需要使用它们，你可能需要考虑包装这些元素或者使用类型扩展。

*原载于 2019 年 1 月 13 日*[*robdodson . me*](https://robdodson.me/which-elements-support-shadow-dom/)*。**