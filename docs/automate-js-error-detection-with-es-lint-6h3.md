# 使用 ES Lint 自动检测 JS 错误

> 原文：<https://dev.to/teodeleanu/automate-js-error-detection-with-es-lint-6h3>

ESLint 是一个 Javascript 代码质量工具，它分析你的代码而不执行它。它会提醒你注意错误，并为你赢得大量时间。
ESLint 是一个识别和报告 ECMAScript/JavaScript 代码中模式的工具。在许多方面，它类似于 JSLint 和 JSHint，但有一些例外:它们带有一组预定义的规则。ESlint 具有灵活性，因此您可以设置。

一个简单的例子可以帮助你吗？

那么我们来举个简单的例子:

```
function(a,b){
    //do something with the variables
    return a * d;
} 
```

当您有少量代码时，这个错误是很明显的。但是如果你有大量的话，这将会非常有用。
通过对代码运行 eslint，您会得到下一个输出:

```
1:10  error  'test' is defined but never used                  no-unused-vars
1:17  error  'b' is defined but never used                     no-unused-vars
2:1   error  Expected indentation of 1 tab but found 4 spaces  indent
3:1   error  Expected indentation of 1 tab but found 4 spaces  indent
3:16  error  'd' is not defined                                no-undef 
```

如果没有 ES lint，您可能会有不必要的错误，这会让您的客户不高兴。因此，作为代码质量自动化过程的一部分，再加上自动化测试，你可以设置 eslint 来获得更好的代码质量。这将修复不必要的错误，您的客户会更高兴。

你可以在上面的代码中看到，这已经发现了一些错误，我已经设置了 eslint 配置。现在就让我们为你做吧！

如果您想将 ESLint 作为项目构建系统的一部分，我们建议将其安装在本地。您可以使用 npm 做到这一点:

安装使用
先决条件:Node.js ( > =6.14)，npm 版本 3+。

> 与这个主题有一点关系:在 [Appseed PRO](https://appseed.us/fullstack-apps-generator) 版本中，你也可以在 VSCode 中轻松调试测试。你甚至可以免费获得以上所有代码，自己使用。麻省理工的执照。

安装 ESLint 有两种方式:全局安装和本地安装。

本地安装和使用

$ npm install eslint - save-dev
然后，您应该设置一个配置文件:

$ ./node_modules/。之后，您可以在项目的根目录中运行 eslint，如下所示:

$ ./node_modules/。bin/eslint yourfile.js
而不是导航到。/node_modules/。bin/您也可以使用 npx 来运行 eslint:

$ npx eslint
注意:如果 eslint 不是手动安装的(通过 npm)，npx 会将 ESLint 安装到一个临时目录并执行它。

您使用的任何插件或可共享配置也必须安装在本地，以便使用本地安装的 ESLint。

全球安装和使用
如果您想让 ESLint 可用于在所有项目中运行的工具，我们建议您全球安装 ESLint。您可以使用 npm 做到这一点:

然后，您应该设置一个配置文件:

$ eslint - init
之后，您可以对任何文件或目录运行 eslint，如下所示:

$ eslint yourfile . js 您的档案

您使用的任何插件或可共享配置也必须全局安装，以便与全局安装的 ESLint 配合使用。

因此，ESLint 可以发现错误，强化规则，还可以自动修复问题。
运行修复命令很容易:

```
./node_modules/.bin/eslint --fix --ext .js test.js

/appseed/starter-express/test.js
  1:10  error  'test' is defined but never used  no-unused-vars
  1:17  error  'b' is defined but never used     no-unused-vars
  3:13  error  'd' is not defined                no-undef 
```

运行 fix 命令后，它只返回需要您注意的错误。很明显，在我们的例子中，我们从来没有使用过测试函数。看起来我在使用变量 d 而不是 b 上犯了一个错别字。
让我们修复它，看看输出。代码现在看起来是这样的:

```
function test(a,b){
    //do something with the variables
    return a * b;
}

test(1,2); 
```

eslint 的输出如下:

```
node_modules/eslint/bin/eslint.js test.js
➜  starter-express git:(feat/sequelize-orm-register) ✗ 
```

可以与 npm 一起使用的 node 的最佳特性之一是包脚本。所以在你的 package.json 中，你可以设置一些命令来轻松地从你的项目中运行 eslint 或者从你的 travis ci 中调用它。

```
"scripts": {
    "start": "node index.js",
    "dev": "nodemon start",
    "lint": "./node_modules/.bin/eslint --ext .js config/ models/ routes/ seeders/ index.js",
    "lint-fix": "./node_modules/.bin/eslint --fix --ext .js config/ models/ routes/ seeders/ index.js",
  }, 
```

这很有帮助，因为现在您可以运行一个简单的命令来测试和修复您的小错误。

```
npm run lint
npm run lint-fix 
```

PS:本文最早发表于 [appseed.us 博客](https://blog.appseed.us/automate-error-detection-with-es-lint/)。