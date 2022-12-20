# Dark (lang):用 OCaml/ReasonML/Bucklescript 构建，灵感来自 Elm

> 原文：<https://dev.to/nielsbom/dark-lang-built-with-ocaml-reasonml-bucklescript-inspired-by-elm-1o5e>

Dark 是一个尚未发布的语言/平台/工具链，我之前写过一篇关于它的文章。

Dark 的创始人 Paul Biggar 写了另一篇[帖子](https://medium.com/darklang/tablecloth-a-new-standard-library-for-ocaml-reasonml-d29a73a557b1)，其中他给出了一些细节并分享了他们使用的开源标准库。([桌布](https://github.com/darklang/tablecloth)

Dark 使用基于 OCaml 的技术栈，OCaml 在后端。OCaml 是来自 [ML 语系](https://en.wikipedia.org/wiki/ML_(programming_language))的一种不太知名的语言，有 Clojure、Elm、F#、F*、Haskell、Idris、Miranda、Erlang、Rust 和 Scala 等语言。这个语系非常[【功能性】](https://www.youtube.com/watch?v=e-5obm1G_FY)。

在前端，黑暗使用了由 [ReasonML](https://reasonml.github.io/) 编写的[榆树](https://en.wikipedia.org/wiki/Elm_(programming_language))启发的架构。ReasonML 是 OCaml(相同的抽象语法树 afaik)的某种形式，其中 [Bucklescript](https://bucklescript.github.io/) 是可以将 ReasonML 代码转换成 JavaScript 的工具。Bucklescript 也可以将 OCaml 转换成 JavaScript，但是 ReasonML 对于已经了解 JavaScript 的人来说更熟悉。

要点:

*   据我所知，OCaml 是一门伟大的语言
*   榆树架构是一个伟大的想法，React 的 Redux 部分复制了它
*   ReasonML 是开源的，但是受到了脸书的大力支持和鼓励，我认为这有一定的缺点

就我个人而言，我曾涉猎过 ReasonML 和 ReasonReact，但对我来说，围绕它的工具和 3 个抽象层的错误消息有点多。所以我把我的钱押在 pure Elm 上(它也不是没有缺点)。

我期待看到更多黑暗将分享的东西。我也想听听你的想法。