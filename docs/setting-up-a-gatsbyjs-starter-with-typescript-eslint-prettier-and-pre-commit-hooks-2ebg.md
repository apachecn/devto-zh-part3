# 使用 TypeScript、ESLint、pre-commit 挂钩设置 GatsbyJS 启动程序

> 原文：<https://dev.to/ardennl/setting-up-a-gatsbyjs-starter-with-typescript-eslint-prettier-and-pre-commit-hooks-2ebg>

我的博客所基于的静态站点生成器 GatsbyJS 肯定是我最喜欢的网关技术。它教会了我如何适应 React，并向我介绍了 GraphQL。由于现在我从事的每个项目都包含 TypeScript(TS ),用 TypeScript 更新 Gatsby starter 似乎是获得一些深入实用知识的完美方式。

在本文中，我们将使用 TypeScript、ESLint、Prettier 设置 Gatsby 默认启动博客，并在每次使用`lint-staged`和`husky`提交之前运行它们。

## 为什么打字稿？

这个问题的答案可能是一篇独立的博客文章，但是这个来自 Lodewijk Bogaards 的精彩的 [StackOverflow 答案无疑会回答你的大部分问题。从回答来看:](https://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript/35048303#35048303)

> TypeScript 是现代的 JavaScript +类型。它是关于尽早捕捉 bug，让你成为更高效的开发人员，同时利用 JavaScript 社区。

你在“及早捕捉虫子”上骗过了我。我们开始吧！

## 分叉、克隆并安装盖茨比博客启动器

对于本教程，我建议您将 gatsby blog starter 分支到您自己的 Github 帐户，并从那里克隆到您的本地机器上。

*   去 https://github.com/gatsbyjs/gatsby-starter-blog。
*   点击`fork`
*   用`git clone git@github.com:<youraccount>/gatsby-starter-blog.git`将存储库克隆到您的本地机器上
*   `cd`进入文件夹
*   *可选*用`git checkout -b "typescript"`创建一个新的分支并推送
*   运行`yarn install`
*   运行`yarn develop`

瞧，你的 Gatsby starter 正在`http://localhost:8000/`上运行，我们可以开始设置打字稿了！

## 安装`gatsby-plugin-typescript`并打字

为了在 Gatsby 中使用 TypeScript，我们需要添加两个新的包，从`gatsby-plugin-typescript`开始。`gatsby-plugin-typescript` 的[描述页面让我有点困惑，因为它明确表示*不*做类型检查。那么这个插件到底是做什么的？](https://www.gatsbyjs.org/packages/gatsby-plugin-typescript/)

事实证明，TypeScript 本身就是一个 Transpiler，就像 Babel 一样。它可以进行类型检查*和*，生成几种浏览器可读的 JavaScript。在 GatsbyJS 中，我们只需要类型检查，因为 Gatsby 已经使用 Babel 来传输我们的 ESNext 代码。

这就是为什么`gatsby-plugin-typescript`扩展了 GatsbyJS WebPack 和 Babel 配置以包含`@babel/preset-typescript`插件。这样，Babel 及其插件可以将 TypeScript *和* ESNext 代码转换成浏览器可读的 JS，我们将独立设置 TypeScript，为我们提供全面的类型检查支持，而无需编译任何内容。

为了进一步的解释，我推荐你参考 Matt Turnbull 写的这篇关于 [TypeScript + Babel 的精彩文章。](https://iamturns.com/typescript-babel/)

所以让我们开始吧，把`gatsby-plugin-typescript`和打字稿添加到你的 Gatsby 设置中。TypeScript 可以添加到`devDependencies`中，而 Gatsby 插件应该作为依赖项添加:

```
yarn add gatsby-plugin-typescript
yarn add typescript --dev 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记启用项目根目录下的`gatsby-config.js`文件中的插件:

```
 ...
  `gatsby-plugin-offline`,
  `gatsby-plugin-react-helmet`,
  `gatsby-plugin-typescript`,
  ... 
```

Enter fullscreen mode Exit fullscreen mode

### 添加并配置 tsconfig.json 和类型检查脚本

接下来，我们需要添加一个 [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html) 到我们项目的根目录。TypeScript 有一个 CLI 命令`tsc`，当使用它而不指定任何文件时，TypeScript 将总是寻找一个`tsconfig.json`。如果`tsconfig.json`是空的，TypeScript 将恢复其默认值，但是我们需要设置一些东西。

#### `compilerOptions`

*   `"module": "commonjs"`当我们使用 Node.js 并以 CommonJS 的方式导入我们的 NPM 包时，我们想确保这个选项被设置为`commonjs`
*   老实说，当我们不使用 TypeScript 作为编译器时，我不确定这是否有用。当我们使用 TypeScript 作为编译器时，我们可以在这里指定 ECMA 脚本目标。我还是把它留在这里，因为比我聪明的人似乎也是这么做的。在我们的例子中，我们将只瞄准`esnext`。
*   TypeScript 有几个不同的选项来编译 JSX。同样，我们没有用 TypeScript 编译，但是当我们使用 JSX 时，它会期望这个选项存在。`preserve`选项通常会确保 JSX 代码不会被编译。
*   `"lib": ["dom", "esnext"]``lib`选项将告诉 TypeScript 支持哪些库。这不包括任何 polyfills 或任何东西，只是告诉 TypeScript 在编译和类型检查时允许哪些方法。如果我们在选项中省略了`dom`，而包含了`document.querySelector`，TypeScript 会显示一个错误。
*   这个选项启用了一系列严格的类型检查选项，如`noImplitAny`、`noImplicitThis`和`strictFunctionTypes`。要么努力，要么回家！
*   由于我们不希望 TypeScript 创建任何新文件，因为我们要把它留给 Gatsby Babel 设置，所以不要忘记这个选项很重要。
*   这两个选项主要用于保持与 Babel 的适当兼容性。你可以在这篇由微软撰写的关于 TypeScript 和 Babel 7 的[文章中读到更多。](https://devblogs.microsoft.com/typescript/typescript-and-babel-7/)
*   我不知道你是怎么想的，但我总会留一些变量以备不时之需。也许这是一个坏习惯，我应该在我的代码中应用更多的 Marie Kondo 实践，但不是今天。

#### 包含和排除

我们可以在配置文件中指定`include`和`exclude`。如果没有指定`include`，TypeScript 将包含根目录和所有子目录中所有兼容的文件。在我的例子中，我决定只使用 exclude 选项，以确保 TypeScript 不会浪费时间检查公共文件夹、我的`node_modules`或我的`.cache`目录中编译的 JavaScript。

我们的配置文件现在应该是这样的:

```
{  "compilerOptions":  {  "module":  "commonjs",  "target":  "esnext",  "jsx":  "preserve",  "lib":  ["dom",  "esnext"],  "strict":  true,  "noEmit":  true,  "isolatedModules":  true,  "esModuleInterop":  true,  "noUnusedLocals":  false  },  "exclude":  ["node_modules",  "public",  ".cache"]  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加文字检查 NPM 脚本。

接下来，给你的`package.json` :
添加一个新的脚本

```
"scripts":  {  ...  "type-check":  "tsc"  } 
```

Enter fullscreen mode Exit fullscreen mode

不要担心传递任何标志。运行`tsc`会让 TypeScript 寻找我们的`tsconfig.json`，它保存了我们所有的配置。如果一切正常，我们现在可以运行`yarn type-check`，这可能会导致以下错误:

```
$ tsc
error TS18003: No inputs were found in config file '~/gatsby-starter-blog/tsconfig.json'.
Specified 'include' paths were '["**/*"]' and 'exclude' paths were '["node_modules","public",".cache"]'. 
```

Enter fullscreen mode Exit fullscreen mode

这个不用担心！这只是因为我们的设置中还没有任何类型脚本文件。我们所有的文件仍然是`.js`，并且看到我们还没有在我们的`tsconfig.json`中设置`allowJs`为真，没有什么要检查的。我们会尽快解决这个问题。

### 将文件转换成打字稿

此时，开始将您的`*.js`文件重命名为`*.ts`和`*.tsx`(如果它们包含 JSX)可能是个好主意。你可以转换`./src/`文件夹中的所有文件，如果你的编辑器支持智能感知，它会立刻用一大串红色的曲线对你大喊大叫。同时，运行`yarn type-check`会给你一大堆错误，这是一个很好的改变，因为这意味着你的配置工作了！

通常我还会建议你开始修复 TypeScript 正在抱怨的当前类型错误。因为我想确保你有一个完整的设置，包括林挺，我将这些错误的实际修复留给后续的博客帖子。现在，请容忍我，因为我们建立了一个棉绒和提交挂钩！

此外，不要忘记提交您的代码并休息一下！

### 设置棉绒

#### ESLint 还是 TSLint？

只是为了避免任何混淆:与 TypeScript 一起使用的首选 linter 是 ESLint。你可能还会看到很多`tslint`配置文件，但是我相信 [TSLint 很快就会被弃用](https://medium.com/palantir/tslint-in-2019-1a144c2317a9)。

#### 设置 ESLint 和 beauty

为了用 TypeScript、更漂亮和一些 React 最佳实践来设置 ESLint，我们需要添加一些`devDependencies` :

```
yarn add eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-prettier eslint-plugin-prettier eslint-plugin-react --dev 
```

Enter fullscreen mode Exit fullscreen mode

现在所有必要的包都安装好了，我们需要添加一个`.eslintrc.js`配置文件到我们项目的根目录(我更喜欢一个`.js`文件，这样我可以添加注释)。下面你会发现一个我的 ESLint 配置的例子

```
module.exports = {
  parser: '@typescript-eslint/parser', // Specifies the ESLint parser
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier/@typescript-eslint',
    'plugin:prettier/recommended'
  ],
  settings: {
    react: {
      version: 'detect'
    }
  },
  env: {
    browser: true,
    node: true,
    es6: true
  },
  plugins: ['@typescript-eslint', 'react'],
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: 2018, // Allows for the parsing of modern ECMAScript features
    sourceType: 'module' // Allows for the use of imports
  },
  rules: {
    'react/prop-types': 'off', // Disable prop-types as we use TypeScript for type checking
    '@typescript-eslint/explicit-function-return-type': 'off'
  },
  overrides: [
    // Override some TypeScript rules just for .js files
    {
      files: ['*.js'],
      rules: {
        '@typescript-eslint/no-var-requires': 'off' //
      }
    }
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这种设置中，TypeScript linter 将与 Prettier 和 ESLint 完美配合，同时还能够扩展其他 ESLint 设置和建议。

#### 添加 Lint 脚本

为了让我们自己的生活更轻松，我们将在我们的`package.json`
中添加两个 lint 脚本

```
"scripts":  {  ...  "lint":  "eslint --ignore-path .gitignore . --ext ts --ext tsx --ext js --ext jsx",  "lint:fix":  "yarn lint --fix"  } 
```

Enter fullscreen mode Exit fullscreen mode

第一个脚本对每个`*.ts`、`*.js`、`*.tsx`和`*.jsx`文件运行 ESLint，并显示错误。第二个也将修复任何 ESLint 可以自己修复的错误。如果您现在运行`yarn lint`，您应该会在终端中看到一大堆 lint 错误。

### 设置编辑器

VSCode 拥有出色的林挺支持，但是为了确保我们不仅能看到类型错误，还能看到我们在编码时在`.eslint`文件中声明或扩展的规则，我们需要在 VSCode 设置中添加一些内容。

```
"eslint.validate":  [  {  "language":  "javascript",  "autoFix":  true  },  {  "language":  "javascriptreact",  "autoFix":  true  },  {  "language":  "typescript",  "autoFix":  true  },  {  "language":  "typescriptreact",  "autoFix":  true  }  ], 
```

Enter fullscreen mode Exit fullscreen mode

您可以将它添加到您的常规设置中，或者将它包含在项目根目录中标有`.vscode`的文件夹中的一个文件中。如果你愿意，你可以在这里下载这个文件:[https://github . com/aderaaij/Gatsby-starter-blog/tree/typescript/。vscode](https://github.com/aderaaij/gatsby-starter-blog/tree/typescript/.vscode)

### 设置 Husky 和 Lint 分级

让我们的编辑器突出显示类型错误是很好的，但是当然，最终的目的是确保每个处理我们代码的人都会提交格式相同的代码，并根据相同的规则进行检查。如果它没有通过类型检查*和*林挺，它就不能被添加到代码库中。

为此，我们将使用 NPM 包`husky`和`lint-staged`。`husky`允许我们运行提交前和提交后的钩子，`lint-staged`允许我们只对*和*准备提交的文件运行 linter。
要安装它们，运行:

```
yarn add husky lint-staged --dev 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在我们的`package.json`或单独的文件中配置`husky`和`lint-staged`。我更喜欢单独的文件，因为看一下文件结构就可以知道已经配置了哪些好东西。

首先，让我们在我们的项目的根目录中添加一个`.lintstagedrc`，并添加以下内容:

```
{  "*.{js,jsx,ts,tsx}":  ["yarn lint:fix",  "git add"],  "*.scss":  ["prettier --write",  "stylelint --fix",  "git add"],  "{*.{json,md}}":  ["prettier --write",  "git add"]  } 
```

Enter fullscreen mode Exit fullscreen mode

这将在提交时运行您的`lint:fix`脚本，同时在`*.scss`、`*.json`和`*.md`文件上运行得更漂亮。这只在暂存的文件上运行。

接下来，将一个`.huskyrc`文件添加到您的项目的根目录中，并将以下内容添加到其中:

```
{  "hooks":  {  "pre-commit":  ["yarn type-check && lint-staged"]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这将`type-check`挂接`pre-commit`上的所有文件，并运行`lint-staged`命令，该命令反过来运行我们添加到`.lintstagedrc`文件中的命令，但只针对暂存文件。

现在尝试并提交您的新更新...不可以！由于`type-check`脚本在*所有*你的类型脚本文件上运行，我们到目前为止所做的只是将`*.js`文件重命名为`*ts(x)`，这里有大量的类型和 lint 错误。

如果您确实希望能够提交您的配置文件，您可以在终端中向您的提交命令添加一个`--no-verify`。

## 包装完毕

所以我们有它！你一开始有一个非常好的盖茨比启动器，现在我们把它全搞砸了。你的编辑器充满了愤怒的曲线，当你试图提交代码时，你的终端对你大喊大叫。恭喜你！

好的一面是，TypeScript、ESLint、pre attender 和一堆预提交钩子都配置好了。而且这还不是全部:如果你跑`yarn develop`或者`yarn build`，盖茨比还是会跑。这是因为我之前提到的巴别塔结构。只要 JS 有效，TypeScript 错误就不会阻止代码的转换。

给你留下一个充满错误的博客，我确实感到有点内疚，但在下一篇博客文章中，我们将努力与这些歪歪扭扭的线条作斗争

*   为我们的包安装类型定义
*   为没有自己的类型定义的包创建新定义
*   为对象创建接口
*   尝试为我们的 GraphQL 查询生成一些定义

不管我们的道路上会遇到什么。

你可以在下面的分支中找到到目前为止的进度:[https://github . com/aderaaij/Gatsby-starter-blog/tree/typescript](https://github.com/aderaaij/gatsby-starter-blog/tree/typescript)

如果你想看到一切正常运行，没有任何错误，你可以看看 Arden.nl 的 GitHub repo

## 资源

*   StackOverflow 对“什么是 TypeScript，为什么我要用它来代替 JavaScript？”这个问题的回答。[closed]"来自 Lodewijk Bogaards
*   [ts config . JSON 文件上的打字手册](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)
*   编译器选项打字手册
*   [Gatsby starter 博客 TypeScript GitHub 上的分支](https://github.com/aderaaij/gatsby-starter-blog/tree/typescript)
*   [Gatsby TypeScript 插件概述](https://www.gatsbyjs.org/packages/gatsby-plugin-typescript/)
*   马特·特恩布尔的
*   [*在罗伯特·库珀的*打字稿项目中使用 ESLint 和 pretty](https://www.robertcooper.me/using-eslint-and-prettier-in-a-typescript-project)
*   [*2019 年 ts lint*by Palantir](https://medium.com/palantir/tslint-in-2019-1a144c2317a9)