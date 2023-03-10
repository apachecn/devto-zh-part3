# 开发前端库时，如何让您的生活更轻松

> 原文：<https://dev.to/natseg/how-to-make-your-life-easier-when-developing-a-front-end-library--4pjk>

假设您有一个开发前端库的好用例。

可能是这样的:

*   你在不同的应用中重复使用许多相似的组件
*   您希望将您的应用程序与外部 api 分离
*   你想为了乐趣而玩 javascript 吗
*   ...

1.  使用您喜欢的提供者(github，gitlab，...)
2.  您可以选择为您希望使用的框架使用样板文件(Typescript、React、both，...)
3.  一旦你做到了这一点，你就会意识到为了与你的应用集成:

    *   您需要根据您使用的捆绑管理器(rollup，webpack，...)
    *   你需要告诉你的应用程序在哪里可以找到正在开发的包
4.  在这一点上，我建议使用`link`，这是一个对[纱线](https://yarnpkg.com/lang/en/docs/cli/link/)和 [npm](https://docs.npmjs.com/cli/link.html) 都存在的命令。
    这在你的应用和你正在使用的库之间创建了一个符号链接。

5.  为了避免在库中每次代码更改后都必须“手动”重建，请使用带有 [`watch`](https://webpack.js.org/api/cli/#watch-options) 标志的 bundler cli 递归地监视文件

6.  选择一个测试框架，您就可以开始了！

陷阱:

*   在此之后的任何时候运行`install`都将覆盖该链接。
*   注意什么锁定了你的`package.json`中有疑问的版本，你可以从`caret`切换到`tilde`或者锁定精确版本(用[保存精确标志](https://docs.npmjs.com/cli/install)

**编码、流汗、骂人、测试**

在部署和发布需要代码审查和 QA 的包之前:

1.  构建最新版本的代码
2.  在你的库中使用 [`npm pack`](https://docs.npmjs.com/cli/pack.html)
3.  这将创建一个 tarball 文件，您可以将它移动到您希望 QA 对其进行测试的应用程序的存储库中。
    运行`mv nameoftarball ../wherever/your_app/lives`
    然后`cd ../wherever/your_app/lives && npm i nameoftarball`

4.  提交、推送、部署到试运行/测试环境

5.  当你解除警报后，无论你部署什么都将准确无误！