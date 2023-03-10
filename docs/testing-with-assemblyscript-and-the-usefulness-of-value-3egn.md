# 使用 AssemblyScript 进行测试，以及值的有用性

> 原文：<https://dev.to/jtenner/testing-with-assemblyscript-and-the-usefulness-of-value-3egn>

[AssemblyScript](https://twitter.com/AssemblyScript) 不是你普通的 TypeScript 编译器。它将 TypeScript 编译成 Web 程序集，这使它成为一项令人兴奋的新技术，将彻底改变开发人员在`node.js`和浏览器中解决 CPU 密集型任务的方式。它使得格式良好的类型脚本代码非常快。我已经成为这种语言的早期采用者，我自豪地把它放在我的简历上，就在我的打字经历旁边。当雇主问这种语言是什么意思时，我告诉他们，我有能力将二进制编译版本的 TypeScript 作为一种选择带到桌面上。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [组装脚本](https://github.com/AssemblyScript) / [组装脚本](https://github.com/AssemblyScript/assemblyscript)

### 肯定不是 WebAssembly 编译器的类型脚本🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

[![AssemblyScript logo](img/991b4653baee4f5000ba823d7265b53e.png)](https://assemblyscript.org)

[![Test status](img/76340ce00a0a10c745566947f91c0a1c.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3ATest)[![Publish status](img/699ea13a9ee0376aca5fde6fce52fcaf.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3APublish)[![npm compiler version](img/8145eb2c775dde2e06500dca701c0ad9.png)](https://www.npmjs.com/package/assemblyscript)[![npm loader version](img/9cea788c57f063697d742cfe1721459d.png)](https://www.npmjs.com/package/@assemblyscript/loader)[![Discord online](img/f55d42249a841f29fffb440efb3aa83e.png)](https://discord.gg/assemblyscript)

**AssemblyScript** 使用 [Binaryen](https://github.com/WebAssembly/binaryen) 将 [TypeScript](http://www.typescriptlang.org) (基本上是带类型的 JavaScript)的严格变体编译成 [WebAssembly](http://webassembly.org) 。它生成精益和平均的 WebAssembly 模块，而仅仅是一个`npm install`之遥。

### [关于](https://assemblyscript.org) [简介](https://assemblyscript.org/introduction.html) [快速入门](https://assemblyscript.org/quick-start.html) [开发说明](https://assemblyscript.org/development.html)

## 贡献者

[![Contributor logos](img/078a49b07397ff575b3d1e7b61be51a0.png)](https://assemblyscript.org/#contributors)

## 感谢我们的赞助商！

大多数核心团队成员和大多数贡献者都是在空闲时间做这项开源工作的。如果你使用 AssemblyScript 完成一项重要的任务或者打算这样做，并且你希望我们在它上面投入更多的时间，[请将](https://opencollective.com/assemblyscript/donate)捐赠给我们的[open collection](https://opencollective.com/assemblyscript)。通过赞助这个项目，你的标志将出现在下面。非常感谢你的支持！

[![Sponsor logos](img/229ffec963204f876b62fb4ed1efdd53.png)](https://assemblyscript.org/#sponsors)

</article>

[View on GitHub](https://github.com/AssemblyScript/assemblyscript)

这种语言目前面临的一个问题是获得社区的支持，这个社区目前正在发展，但速度很慢。它还缺乏标准化的工具。没有一个合适的工具链，一个可靠的测试框架，以及许多人的共同努力，新语言很难获得发展。谢天谢地， [@dcodeIO](https://twitter.com/dcodeIO) 和 [@MaxGraey](https://twitter.com/MaxGraey) 是两位非常敬业和充满激情的开发者。幸运的话，AssemblyScript 将在他们的领导下继续发展。

您可以在此处跟踪 AssemblyScript 的主要创建者:

[![dcode image](img/d259a05d161a348b529a08bd299908a8.png)](/dcode)

## [dcode](/dcode)

[Makes compilers with weird names. Citizen of the web.](/dcode)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)dcode io](https://twitter.com/dcodeIO)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)dcode io](https://github.com/dcodeIO)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)https://dcode . io](https://dcode.io)

我想开发的一个最近的贡献叫做`as-pect`，它位于[https://github.com/jtenner/as-pect](https://github.com/jtenner/as-pect)。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [詹纳](https://github.com/jtenner) / [至于](https://github.com/jtenner/as-pect)

### 🔥强烈的🔥用汇编脚本进行快速测试

<article class="markdown-body entry-content container-lg" itemprop="text">

# jtenner/as-pect

这个包是一个 monorepo，包含 cli 和`@as-pect`包的核心。

[![Greenkeeper badge](img/5cdc90a38632362af8046ca689938e38.png)](https://greenkeeper.io/)[![Build Status](img/5ba3bac52136a284cfa710e93562662c.png)](https://travis-ci.org/jtenner/as-pect)[![Coverage Status](img/31f04fb04873034e57430b913897975f.png)](https://coveralls.io/github/jtenner/as-pect?branch=master)[![lerna](img/fd8983decc3fc5bab4a2d10c5def50a9.png)T11】](https://lerna.js.org/)

用 AssemblyScript 编写你的模块，以 WebAssembly 的速度获得极快的引导测试！

## 证明文件

要查看文档，可以在 gitbook 上的[这里](https://tenner-joshua.gitbook.io/as-pect/)找到。如果文件有任何问题，请随时提出问题！

## 贡献者

若要投稿，请参阅 [CONTRIBUTING.md](https://raw.githubusercontent.com/jtenner/as-pect/master/./CONTRIBUTING.md) 。

感谢 [@willemneal](https://github.com/willemneal) 和 [@MaxGraey](https://github.com/maxgraey) 的支持，让`as-pect`成为最好的软件。

其他贡献者:

*   [@trusktr](https://github.com/trusktr) -文档变更
*   [@MaxGraey](https://github.com/maxgraey) -性能 API 建议
*   [@torch2424](https://github.com/torch2424) -文档变更
*   [@dcodeio](https://github.com/dcodeio) -自己做了 AssemblyScript！
*   [@9oelM](https://github.com/9oelM) -其他功能

## 特别感谢

特别感谢 [AssemblyScript](https://github.com/AssemblyScript/assemblyscript) 团队创建了 AssemblyScript 本身。

</article>

[View on GitHub](https://github.com/jtenner/as-pect)

它是一个可配置的命令行工具，使开发人员能够使用 Web Assembly 测试`AssemblyScript`模块，而不是要求开发人员在像`jest`和`Jasmine`这样的框架中模拟他们的模块。在这一点上，对像`jest`这样的测试工具的需求是不可估量的，很明显，在测试 JavaScript 时，这些工具都是不可替代的。然而，要求像`jest`这样的工具来验证 web 程序集应用程序状态会变得很麻烦，唯一可行的替代方法是自己编译一个单独的测试模块。即使您只想在项目中测试一个热 CPU 绑定的代码路径，也是如此。

相反，更明智的做法是探索一个框架来帮助解决这些问题。所以，不用多说，让我们探索如何使用`as-pect`来启动您的 Web 组件开发。

# aspect 入门

我向我周围的每个人推荐的第一件事是:**向你的项目添加一个测试套件！**首先花时间验证测试是否运行。为了你自己，也为了你的用户，你应该在使用你的工具来构建你的想法之前，设置并清理你的工具台。

至于`AssemblyScript`，下面的命令是引导您进入测试环境的好方法。