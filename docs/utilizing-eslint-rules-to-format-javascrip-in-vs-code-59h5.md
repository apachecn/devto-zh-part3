# 利用 eslint 规则在 vs 代码中格式化 javascript

> 原文：<https://dev.to/hoverbaum/utilizing-eslint-rules-to-format-javascrip-in-vs-code-59h5>

今天我们将快速看一下如何配置 VS 代码，以便在每次保存文件时根据 ESLint 规则自动格式化代码。

这假设您已经有了一些 ESLint 规则。如果不是的话，airbnb 的规则是一个让你开始的好地方。

一旦你建立了规则，并开始编写 VS 代码，每当你忘记“你应该缩进 x”或你的公司可能使用的任何特殊规则时，编辑就会开始抱怨。特别是如果你经常与不同的公司使用不同的风格，这就变得非常麻烦。幸运的是，有两个插件可以帮助我们解决这个问题。

继续安装 [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 和[beautiful](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)。第一个是你可能已经安装的插件，帮助你发现代码风格中的错误。后者是一种通常用于格式化代码的工具。

有了更漂亮的代码，你可以实现更大的成就，甚至在提交时格式化你的代码，这可以极大地帮助整个团队简化编码风格。今天，我们将只在每次在编辑器中保存文件时使用它。为此，我们只需在 settings.json 中添加两行。

```
"editor.formatOnSave":  true,  "prettier.eslintIntegration":  true 
```

你可以在你的命令面板中使用`Preferences: Open User Settings`找到你的 settings . JSON(⇧+⌘+p | ⇧+ctrl+p).

现在每次你保存一个文件，漂亮会根据你的 ES Lint 配置格式化它。

[![Art Palette Emoji](img/c9ead87b92e53ff72081dd2c2638bec0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EmGaAENH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hoverbaum-blog-assets/emojies/emoji-artist-palette.png)

* * *

最初发表于[HoverBaum.net](https://hoverbaum.net/2019/02/22/Utilizing-ESLint-rules-to-format-JavaScript-in-VS-Code/)