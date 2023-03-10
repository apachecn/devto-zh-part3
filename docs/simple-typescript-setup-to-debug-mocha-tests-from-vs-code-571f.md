# 从 VS 代码中调试 Mocha 测试的简单类型脚本设置

> 原文：<https://dev.to/akosyakov/simple-typescript-setup-to-debug-mocha-tests-from-vs-code-571f>

[ts-node](https://github.com/TypeStrong/ts-node) 是一个无需编译即可运行 TypeScript 代码的便捷工具。在[忒伊亚](https://github.com/theia-ide/theia)项目中，它使我们能够从 [VS Code](https://code.visualstudio.com/) 或 [Gitpod](https://www.gitpod.io) 中一键调试任何 Mocha 规范 TS 文件。它在很长一段时间内运行良好，并提供了快速的周转。

随着时间的推移，代码库越来越大，ts-node 不得不解释越来越多的代码来运行一个文件。除了速度慢之外，ts-node 的评估方式与 TypeScript 的编译方式并不完全相同。例如，它[忽略了 tsconfig](https://github.com/TypeStrong/ts-node#help-my-types-are-missing) 中的一些属性，导致类型丢失。

我们[开始寻找](https://github.com/theia-ide/theia/pull/2563)一个 TypeScript 设置来运行和调试没有 ts-node 的测试。一般来说，删除对 ts-node 的依赖非常简单。我们得到了立竿见影的好处，比如测试的即时启动，需要安装的 npm 包更少等等。

缺少的部分是从 VS 代码中为单个 spec 文件保留测试执行的快速周转。人们应该能够在 TS 源代码中放置一个断点，然后一键调试。更改后，只有生成的 JS 代码才有可能。

我开始用最简单的设置来重现这个问题:

*   只安装了 TypeScript 和 Mocha
*   [观看](https://github.com/akosyakov/ts-mocha-debugging/blob/b40f7781caed2aa9223c37af380f74903938b73d/package.json#L15) npm 脚本用`tsc -w`编译 TS 代码；
*   而[测试](https://github.com/akosyakov/ts-mocha-debugging/blob/b40f7781caed2aa9223c37af380f74903938b73d/package.json#L16)脚本用`mocha lib/**/*.spec.js`运行所有测试。

在那之后，我专注于获得正确的启动配置，以便从单个 ts 文件中调试测试。问题归结为解释 VS 代码生成 JS 文件来运行当前打开的 TS 文件。这里[变量替换](https://code.visualstudio.com/docs/editor/variables-reference)非常方便。特别是，`fileBasenameNoExtension`变量提供了当前打开文件的名称，没有扩展名。例如，如果你打开`src/repo.spec.ts`文件，那么它将解析为`repo.spec`。

最终，[摩卡测试](https://github.com/akosyakov/ts-mocha-debugging/blob/b0b56ce858a86dbb1c99776b0915662dbb85b99c/.vscode/launch.json#L9)的配置出现了。它从所有匹配`**/${fileBasenameNoExtension}.js`的文件中执行测试。在使用`repo.spec.ts`的情况下，这些测试来自与`../repo.spec.js`匹配的文件。

你可以用 VS 代码或者[从你的浏览器用 Gitpod](https://gitpod.io#https://github.com/akosyakov/ts-mocha-debugging) (需要 OAuth 用 GitHub):
[![Debugging TypeScript Mocha Tests](img/feca9dc1d2687f92975b333115d96e85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5kk0csqo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/3082655/50573829-99274f80-0ddb-11e9-9c70-b88520888678.gif)