# 为什么我从 Visual Studio 代码转向 JetBrains WebStorm

> 原文：<https://dev.to/mokkapps/why-i-switched-from-visual-studio-code-to-jetbrains-webstorm-939>

当我在 2015 年开始我的第一个 JavaScript 项目时，我使用了 [JetBrains WebStorm](https://www.jetbrains.com/webstorm/) ，这是一个用于 JavaScript 开发的 IDE(集成开发环境)。我已经习惯了 JetBrains IDEs，因为我以前在基于 [IntelliJ IDEA](https://www.jetbrains.com/idea/) 的 Android Studio 工作过。此外，我从公司获得了 WebStorm 许可证，因此可以不受任何限制地使用它。

随着 Visual Studio 代码越来越受欢迎，我在未来的网络项目中使用它。我真的很喜欢它，因为它更快，高度可定制和免费，所以我也可以用它来做我的私人项目。

在我目前的项目中，我遇到了一个开发人员，他对我使用编辑器而不是 IDE 来开发大型商业应用程序感到非常困惑。首先，我没有真正考虑他的顾虑，但同时，我理解他。

在这篇博文中，我想告诉你为什么我现在主要使用 WebStorm 而不是 VS 代码进行开发。

## 序言

这是一个非常热门的话题，我知道这会引起一些争议。在下面的文章中，我将讲述我在一个主要用 VS 代码开发的大型 Angular 应用程序中使用 WebStorm 的经历。

## 代码检查

WebStorm 提供了一个健壮、快速、灵活的静态代码分析。这种分析检测语言和运行时错误，提出纠正和改进的建议。它还可以索引你的整个项目，例如，可以检测所有未使用的方法，变量等等。

您还可以使用 VS 代码和 ESLint，通过规则 [no-unused-vars](https://eslint.org/docs/rules/no-unused-vars#disallow-unused-variables-no-unused-vars) 和 [no-unreachable](https://eslint.org/docs/rules/no-unreachable#disallow-unreachable-code-after-return-throw-continue-and-break-statements-no-unreachable) 来检测 JavaScript 方法中未使用的方法。但如果你是，例如，使用一个 TypeScript 项目(如 Angular) VS 代码不会检测未使用的公共方法。请看这个简单的例子:

**VS 代码**

[![](img/0f98f62a72cc2031e0896f614e8d4610.png "source: imgur.com")](https://imgur.com/l3Plzmv)

**网络风暴**

[![](img/3b4e36715a6512bf2740fc5840d960d7.png "source: imgur.com")](https://imgur.com/lIirdG3)

这可能会对主要使用 VS 代码开发的大角度代码库的代码质量产生巨大影响。

要查看不同之处，请打开您用 WebStorm 用 VS 代码开发的项目，并运行[代码检查](https://www.jetbrains.com/help/webstorm/code-inspection.html)。这基本上使我相信使用 WebStorm 会产生一个更干净的代码库。

## 综合因缘测验

WebStorm 有一个我非常喜欢的集成测试运行程序。通过这种方式，您可以直接从 IDE 中运行测试，甚至在那里进行调试。

在 WebStorm 中运行我的 [jasmine](https://jasmine.github.io/) & [Karma](https://karma-runner.github.io/3.0/index.html) 测试，我可以很容易地跳转到失败的测试代码，然后只重新运行这个特定的测试。下图显示了这样的测试运行:

[![](img/cad59fa1687987ad040e19e260330de8.png "source: imgur.com")](https://imgur.com/8EIlmX2)

我在 VS 代码中的角度单元测试工作流程通常是用一个`f`(例如`fdescribe`)来标记一个`describe`或`it`测试块，告诉 Karma 只运行这个特定的测试块。或者，我使用[karma-jasmine-html-reporter](https://www.npmjs.com/package/karma-jasmine-html-reporter)，您也可以通过在 HTML 页面中点击它们来定义只运行某些测试。

目前还有一个 [VS 代码 Karma 测试适配器](https://github.com/hbenl/vscode-example-test-adapter/issues/1#issuecomment-459022876)正在开发中，它应该为 VS 代码提供类似的集成 Karma 测试功能。

## 未兑现的承诺

如果您希望后续代码只在承诺完成后才运行，那么不等待承诺可能会非常棘手。WebStorm 显示是否有未解决的承诺(在这种情况下是对于 TypeScript 应用程序):

**网络风暴**

[![](img/a07ecb98dbccbd6037a7609c457980db.png "source: imgur.com")](https://imgur.com/6w91gli)

VS 代码目前不可能显示这些信息:

**VS 代码**

[![](img/223ff551c982846cfedd9550ff6a226d.png "source: imgur.com")](https://imgur.com/QUqQ6IK)

## 源代码控制/ Git 集成

默认情况下，VS 代码有一个非常基本的 git 集成。如果您喜欢使用 GUI 来完成复杂的 git 工作，您可以使用像 git lens T1 这样的扩展，或者使用像 T2 source tree T3 这样的附加工具。

WebStorm 为复杂的 git 工作提供了开箱即用的所有功能。您可以在 IDE 中使用可视化比较/合并工具提交文件、检查更改和解决冲突。

## 地方历史

VS 代码不会保存你的修改的本地历史，但是你可以使用扩展，比如[本地历史](https://github.com/zabel-xyz/local-history.git)。

WebStorm 会自动跟踪您对文件所做的所有更改，从而防止您意外丢失这些更改。您可以检查文件和目录的历史记录并进行回滚。例如，如果您不小心执行了 git push force，甚至在远程分支上覆盖了您的文件，这可能会很有用。

## 调试

VS 代码只能通过使用 Chrome 扩展的[调试器来调试 Chrome 上的 web 应用程序，然后您需要为您的应用程序配置这个调试器。](https://github.com/Microsoft/vscode-chrome-debug)

使用 WebStorm，你已经有了所有可用的默认设置，例如，Angular 只需要点击“调试应用程序”,你可以在编辑器中设置断点并观察变量等。

## 代码重构

在我看来，使用 WebStorm 重构代码要好得多。您可以重命名组件，它会更新 HTML 和 TypeScript 文件中的所有文件名和用法。一般来说，所有的 JetBrains IDEs 都以其重构特性而闻名:

[![](img/427fb074c2c9461019724c42d3a0ef5a.png "source: imgur.com")](https://imgur.com/P9v25rc)

JetBrain IDEs 的一个众所周知的特性是[安全删除](https://www.jetbrains.com/help/webstorm/2017.1/safe-delete.html)。使用此功能，您可以在重构期间安全地从源代码中移除文件。IDE 将首先搜索文件的用法，如果找到，您可以在删除文件之前检查它们并做出必要的修改。

不幸的是，VS 代码目前还没有那么强大。

## 角度 CLI 积分

WebStorm 通过所谓的角度示意图提供了良好的角度 CLI 集成:

[![](img/761bdba2a4fb969ca0307881d57f2eb9.png "source: imgur.com")](https://imgur.com/AjAfGy9)

总的来说，WebStorm 有很好的角度支持，因为它帮助编辑角度模板，为变量、管道和模板引用变量提供代码完成。

## 速度

VS 代码基于 Electron，由 HTML & JavaScript 提供支持。

WebStorm 是用 Java 开发的，总体来说感觉比 VS 代码慢。我不会说它非常慢，但速度差异是显而易见的。

VS 代码有一个更快的启动时间，但是如果你正在做一个项目，你的 IDE 或者编辑器总是打开的，启动时间并不重要。

## HTML 的可访问性检查

WebStorm 提供了基于[网页内容可访问性指南](https://www.w3.org/WAI/standards-guidelines/wcag/) (WCAG)建议的检查，帮助你编写更易访问的 HTML 代码。

[![](img/9c79e884a60d4b44c8d9c4ffc795237c.png "source: imgur.com")](https://imgur.com/u14WavG)

## 价格

VS 代码是开源的，可以免费使用。

你需要为 WebStorm 许可证付费，除非你选择开源项目、学生、教师、课堂辅助或培训课程、编码学校和新兵训练营可用的免费许可证之一。

另一种选择是使用 [EAP(早期访问计划)](https://www.jetbrains.com/webstorm/eap/)。这些预发布版本包括将添加到下一个版本的功能。在新版本软件发布之前，这些版本暂时可用。

这是 EAP 的官方免责声明:

> 这是该产品的早期版本。您明确承认此版本的产品可能不可靠，可能无法按预期运行，并且可能包含错误。使用 EAP 产品的风险由您自行承担。

## 结论

VS 代码更像是一个编辑器，而不是像 WebStorm 这样的 IDE。WebStorm 的标准安装比 VS Code 的默认安装拥有更多的功能，而且没有额外安装任何扩展。

微软已经用 VS 代码创造了一个惊人的产品，你当然可以把它用于更大的商业应用。一般来说，我更喜欢并推荐使用 WebStorm，原因如下:

*   更好的代码分析功能
*   一体式 IDE，具有良好的基本功能，无需安装许多附加插件
*   更好的代码重构可能性

如果你优先考虑速度，喜欢使用开源软件或者只是想快速编辑一些配置文件，那么你应该选择 VS 代码。

你使用 VS 代码和 WebStorm 有哪些体验？请在评论中告诉我你用什么来开发你的应用程序！

### 我的 VS 代码&网络风暴设置

本文中的截图显示了使用[材质黑暗主题](https://github.com/equinusocio/vsc-material-theme.git)的 VS 代码和使用[材质 UI 和材质黑暗主题](https://plugins.jetbrains.com/plugin/8006-material-theme-ui)的 WebStorm。