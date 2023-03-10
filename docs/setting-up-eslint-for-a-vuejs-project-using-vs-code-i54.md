# 为 Vue 设置 ESLint。使用 VS 代码的 Js 项目

> 原文：<https://dev.to/ferasdawod/setting-up-eslint-for-a-vuejs-project-using-vs-code-i54>

# 简介

在像 Javascript 这样的动态类型语言中检测错误可能是一项困难的任务。在这个简短的教程中，我们将看看如何将 ESLint 集成到 Visual Studio 代码中，以帮助我们 Lint 我们的代码并检测错误。

为了更好地理解林挺代码的好处，请考虑下图:

[![Lint errors](img/79c6e006a4f4fe0ad87e03c6eac4bbf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y71PdswE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y61y1ctayweiuokodcah.jpg)

通过将 linter 添加到您的编辑器中，您可以在编写代码时以及构建和运行您的应用程序之前检测到此类问题。

# 设置

在我们开始之前，请确保您已经做好以下准备:

*   [Visual Studio 代码](https://code.visualstudio.com/)(显然！)
*   一个 Vue.js 项目(使用本教程[您可以快速启动并运行)](https://flaviocopes.com/vue-cli/)

# 安装 Eslint

您可以将 Eslint 和 vue 特定的林挺规则安装到您的项目中，方法是首先在您的项目根目录中打开一个命令行窗口，然后使用下面的命令

```
npm i -D eslint eslint-plugin-vue babel-eslint 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你用的是纱线

```
yarn add eslint eslint-plugin-vue babel-eslint --dev 
```

Enter fullscreen mode Exit fullscreen mode

这会将 Eslint 安装到项目的开发依赖项中，但它还不可用，因为我们需要告诉它我们希望如何 lint 我们的项目，为此我们可以向项目根目录添加一个特殊的配置文件，该文件可以有不同的名称和格式，但我们将坚持使用一个简单的 javascript 文件。

向名为`.eslintrc.js`的项目根目录添加一个新文件，并在其中添加以下代码

```
module.exports = {
  root: true,
  env: {
    // this section will be used to determine which APIs are available to us
    // (i.e are we running in a browser environment or a node.js env)
    node: true,
    browser: true
  },
  parserOptions: {
    parser: "babel-eslint",
    // specifying a module sourcetype prevent eslint from marking import statements as errors
    sourceType: "module"
  },
  extends: [
    // use the recommended rule set for both plain javascript and vue
    "eslint:recommended",
    "plugin:vue/recommended"
  ],
  rules: {
    // we should always disable console logs and debugging in production
    "no-console": process.env.NODE_ENV === "production" ? "error" : "off",
    "no-debugger": process.env.NODE_ENV === "production" ? "error" : "off"
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

添加了前面的文件和代码后，我们的项目就可以运行 ESLint 了。问题是，我们不想手动运行它来了解是否有任何错误，我们希望 vscode 为我们做这件事。为了帮助我们，我们可以从市场上安装令人敬畏的 ESLint 扩展。

安装扩展后，vscode 会自动加载并在我们的项目代码上运行 ESLint，牛逼吧？！

此外，您可以配置扩展，以便在保存文件或键入时对代码运行林挺，并在 vscode 设置页中检查不同的配置选项。

# 自定义

现在我们有了基本的配置设置，您可以向您的`.eslintrc.js`文件添加更多的规则，以更好地适应您的编码风格和标准。例如，你可以在空格上使用标签(这不是在发动一场圣战，只是一个例子！)或者用[单引号代替双引号](https://eslint.org/docs/rules/quotes)甚至[自己写规则](https://eslint.org/docs/developer-guide/working-with-rules)。

查看 [ESLint 规则页面](https://eslint.org/docs/rules/)了解更多可用规则及其用法。

# 结论

无论从短期还是长期来看，在你的工具中加入棉绒都是非常有益的举措。

我希望这个小教程能帮助你设置基本的 ESLint 集成，因为当我第一次遇到 ESLint 时，我很难将其与 vscode 正确集成。

不要犹豫，在评论中问我任何问题，如果你发现任何错别字或问题，请告诉我。