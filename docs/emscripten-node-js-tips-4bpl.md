# EMS 脚本节点. js 提示

> 原文：<https://dev.to/adam_cyclones/emscripten-node-js-tips-4bpl>

意在为《node》中提到的 WASM 这位 leser 收集一堆有用的东西。

故事时间:我曾经和一个非常喜欢 WASM 的开发人员聊过，但是当我问到 node 的时候，他笑了，“这是给浏览器用的”，一个通用的编译目标并不意味着这个目标是特定于平台的。这只是 JavaScript...和预计算字节码。

## 1。删除浏览器内容。

如果您只想编译到节点目标。将`-s ENVIRONMENT='node'`添加到您的编译器参数中。

这将减少 main.js 的输出，删除大部分——而不是所有浏览器相关的部分。-不幸的是 em script en _ run script(" console . log(' doh！')");还是会找一个窗口全局，doh！thisGlobal，赶快做个东西吧。

有关标志的列表，请参见[https://github . com/emscripten-core/emscripten/blob/incoming/src/settings . js](https://github.com/emscripten-core/emscripten/blob/incoming/src/settings.js)

## 1.a 制作一个 JavaScript 对象

好吧，我明白了，这不是特定于节点的，但是值得一提，因为文档中缺少关于这个主题的内容。Emscripten 提供了一个头 emscripten/val.h，val 有点特殊，它不仅是一个类型 val X = "some js value "，它还是创建 JavaScript 对象文字和访问...🤨全球范围。尽管被宣传为从 JavaScript 中获取值的方法，但它也可以创建值并将其放入 JavaScript 中，val X = val::Object()；创建一个真正的 JavaScript 对象！
X.set("key "，" value ")；然后你可以把它返回给 JavaScript。假设你用的是我强烈推荐的 embind。