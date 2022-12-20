# TypeScript 入门

> 原文：<https://dev.to/newswim/getting-started-with-typescript-25l9>

每个人都在谈论微软支持的 JavaScript 超集。许多新库在编写时都考虑到了类型，许多库仍在添加类型，以提高安全性和开发人员的体验。

开始可能有点挑战，尤其是如果你的背景是更动态的语言(对我来说是这样)。下面是一些帮助我熟悉语法和生态系统的资源。尽情享受吧！

### 设置

有几种方法可以试用 TypeScript。

如果你已经在使用巴别塔，巴别塔 7 附带了一个新的预置。查看这篇文章了解更多:[https://iamturns.com/typescript-babel](https://iamturns.com/typescript-babel/)

如果您想使用 CRA (create-react-app)，在从 CLI 生成新项目时使用`--typescript`标志。[链接](https://facebook.github.io/create-react-app/docs/adding-typescript)

想要将 Typescript 添加到现有的 CRA 吗？有一个[迁移路径](https://vincenttunru.com/migrate-create-react-app-typescript-to-create-react-app/)。

如果你只是想在浏览器中尝试这种语言，那就试试官方平台

最后，如果您想在本地试用编译器，请使用[入门指南](https://www.typescriptlang.org/docs/tutorial.html)。

### 学习打字稿

有点矛盾的是，TS 已经用静态类型语言向许多来自更传统背景的开发人员开放了前端世界。

最重要的大概是*。*

 *有很多博客和文章致力于开发、风格、OOP、常见陷阱、高级类型等。—这里有几个亮点。

*   [温柔地介绍打字稿(互动视频)](https://scrimba.com/g/gintrototypescript) ⭐
*   [理解 TypeScript 的类型符号-2 的真实性](http://2ality.com/2018/04/type-notation-typescript.html) ⭐
*   [打字稿深潜- Basarat](https://basarat.gitbooks.io/typescript)
*   [打字稿实用介绍](https://auth0.com/blog/typescript-practical-introduction/)
*   [打字稿演变](https://blog.mariusschulz.com/series/typescript-evolution)(涵盖 2.0)

### 打字+反应

***入门***

*   [https://fettblog.eu/typescript-react](https://fettblog.eu/typescript-react/)
*   [https://github.com/piotrwitek/react-redux-typescript-guide](https://github.com/piotrwitek/react-redux-typescript-guide)
*   [https://github.com/sw-yx/react-typescript-cheatsheet](https://github.com/sw-yx/react-typescript-cheatsheet)

***用例***

*   [https://tinkerylabs.com/react-context-api-with-hooks](https://tinkerylabs.com/react-context-api-with-hooks/)

## 功能性打字稿

像 Lodash 和 Ramda 这样的库已经赋予函数式风格很长时间了，现在开发人员可以直接从 Haskell 和 Scala 这样的语言中实现想法。

### 几个非常引人注目的项目:

*   [FP-TS](https://github.com/gcanti/fp-ts)
*   [净化](https://github.com/gigobyte/purify)
*   [piotr witek/公用事业类型](https://github.com/piotrwitek/utility-types)
*   [FunFix](https://github.com/funfix/funfix)

#### FP 上的一些文章

*   [http://vindum.io/blog/lets-reinvent-frp](http://vindum.io/blog/lets-reinvent-frp/)
*   [https://www . synthesis . co . za/functional-fun-typescript-generics](https://www.synthesis.co.za/functional-fun-typescript-generics/)
*   [https://www.reaktor.com/blog/fear-trust-and-javascript](https://www.reaktor.com/blog/fear-trust-and-javascript/)

最后一个是对 JavaScript 和 FP 与类型不兼容的一种反驳。它不完全是敌对的，我从阅读它中学到了一些东西，所以我将把它留在(为了平衡！)*