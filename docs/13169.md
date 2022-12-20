# 在 TypeScript 项目中使用 ESLint 和 Prettier

> 原文：<https://dev.to/robertcoopercode/using-eslint-and-prettier-in-a-typescript-project-53jb>

> 最初发布在[我的博客](https://www.robertcooper.me/using-eslint-and-prettier-in-a-typescript-project)上。

说到林挺打字稿代码，有两个主要的林挺选项可供选择: [TSLint](https://palantir.github.io/tslint/) 和 [ESLint](https://eslint.org/) 。TSLint 是只能用于 TypeScript 的 linter，而 ESLint 同时支持 JavaScript 和 TypeScript。

在 [TypeScript 2019 路线图](https://github.com/Microsoft/TypeScript/issues/29288#developer-productivity-tools-and-integration)中，TypeScript 核心团队解释说 **ESLint 拥有比 TSLint** 更高性能的架构，他们**在为 TypeScript 提供编辑器林挺集成时将只专注于 ESLint** 。出于这个原因，我建议对林挺的打字稿项目使用 ESLint。

* * *

## 设置 ESLint 以使用 TypeScript

首先，安装所有必需的开发依赖项:

```
yarn add eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin --dev 
```

*   [`eslint`](https://www.npmjs.com/package/eslint) :林挺核心 ESLint 图书馆
*   [`@typescript-eslint/parser`](https://www.npmjs.com/package/@typescript-eslint/parser) :允许 ESLint 输入脚本代码的解析器
*   [`@typescript-eslint/eslint-plugin`](https://www.npmjs.com/package/@typescript-eslint/eslint-plugin) :一个插件，包含一堆特定于类型脚本的 ESLint 规则

接下来，在根项目目录中添加一个`.eslintrc.js`配置文件。以下是 TypeScript 项目的示例配置:

```
module.exports =  {
  parser:  '@typescript-eslint/parser',  // Specifies the ESLint parser
  extends:  [
    'plugin:@typescript-eslint/recommended',  // Uses the recommended rules from the @typescript-eslint/eslint-plugin
  ],
 parserOptions:  {
    ecmaVersion:  2018,  // Allows for the parsing of modern ECMAScript features
    sourceType:  'module',  // Allows for the use of imports
  },
  rules:  {
    // Place to specify ESLint rules. Can be used to overwrite rules specified from the extended configs
    // e.g. "@typescript-eslint/explicit-function-return-type": "off",
  },
}; 
```

**注意:**我更喜欢对`.eslintrc`文件使用 JavaScript 文件(而不是 JSON 文件)，因为它支持可以用来更好地描述规则的注释。

如果使用带有 React 的 TypeScript，应该安装 [`eslint-plugin-react`](https://www.npmjs.com/package/eslint-plugin-react) dev 依赖项，并且可以使用以下配置:

```
module.exports =  {
  parser:  '@typescript-eslint/parser',  // Specifies the ESLint parser
  extends:  [
    'plugin:react/recommended',  // Uses the recommended rules from @eslint-plugin-react
    'plugin:@typescript-eslint/recommended',  // Uses the recommended rules from @typescript-eslint/eslint-plugin
  ],
  parserOptions:  {
  ecmaVersion:  2018,  // Allows for the parsing of modern ECMAScript features
  sourceType:  'module',  // Allows for the use of imports
  ecmaFeatures:  {
    jsx:  true,  // Allows for the parsing of JSX
  },
  },
  rules:  {
    // Place to specify ESLint rules. Can be used to overwrite rules specified from the extended configs
    // e.g. "@typescript-eslint/explicit-function-return-type": "off",
  },
  settings:  {
    react:  {
      version:  'detect',  // Tells eslint-plugin-react to automatically detect the version of React to use
    },
  },
}; 
```

最终由您来决定您想从哪些规则中扩展，以及在您的`.eslintrc.js`文件中的`rules`对象中使用哪些规则。

## 更漂亮

与 ESLint 一起工作得很好的是[更漂亮的](https://prettier.io/)，它在处理代码格式化方面做得很好。安装所需的开发依赖项，以便更好地使用 ESLint:

纱线添加漂亮 eslint-config-漂亮 eslint-plugin-漂亮- dev

*   [`prettier`](https://www.npmjs.com/package/prettier) :核心漂亮点库
*   [`eslint-config-prettier`](https://www.npmjs.com/package/eslint-config-prettier) :禁用可能与更漂亮冲突的 ESLint 规则
*   [`eslint-plugin-prettier`](https://www.npmjs.com/package/eslint-plugin-prettier) :作为 ESLint 规则运行起来更漂亮

为了配置得更漂亮，根项目目录下需要一个`.prettierrc.js`文件。下面是一个示例`.prettierrc.js`文件:

```
module.exports =  {
  semi:  true,
  trailingComma:  'all',
  singleQuote:  true,
  printWidth:  120,
  tabWidth:  4,
}; 
```

接下来，需要更新`.eslintrc.js`文件:

```
module.exports =  {
  parser:  '@typescript-eslint/parser',  // Specifies the ESLint parser
  extends:  [
    'plugin:@typescript-eslint/recommended',  // Uses the recommended rules from the @typescript-eslint/eslint-plugin
    'prettier/@typescript-eslint',  // Uses eslint-config-prettier to disable ESLint rules from @typescript-eslint/eslint-plugin that would conflict with prettier
    'plugin:prettier/recommended',  // Enables eslint-plugin-prettier and displays prettier errors as ESLint errors. Make sure this is always the last configuration in the extends array.
  ],
  parserOptions:  {
    ecmaVersion:  2018,  // Allows for the parsing of modern ECMAScript features
    sourceType:  'module',  // Allows for the use of imports
  },
}; 
```

使用`eslint-plugin-prettier`将更漂亮的设置作为 ESLint 规则的好处是，可以使用 ESLint 的`--fix`选项自动修复代码。

## 自动修正代码(VS 代码)

为了获得良好的开发体验，设置编辑器在保存文件时自动运行 ESLint 的自动修复命令(即`eslint --fix`)是很有用的。由于我使用的是 VS 代码，下面是 VS 代码中的`settings.json`文件中需要的配置，以便在保存文件时自动修复:

```
"eslint.autoFixOnSave":  true,
"eslint.validate":  [
  "javascript",
  "javascriptreact",
  {"language":  "typescript",  "autoFix":  true  },
  {"language":  "typescriptreact",  "autoFix":  true  }
], 
```

如果您还在`settings.json`中将`editor.formatOnSave`选项设置为`true`，那么您需要添加以下配置，以防止在保存 JavaScript 和 TypeScript 文件时运行 2 个格式化命令:

```
"editor.formatOnSave":  true,  "[javascript]":  {  "editor.formatOnSave":  false,  },  "[javascriptreact]":  {  "editor.formatOnSave":  false,  },  "[typescript]":  {  "editor.formatOnSave":  false,  },  "[typescriptreact]":  {  "editor.formatOnSave":  false,  }, 
```

* * *

现在你知道了。这就是如何使用 ESLint 对 TypeScript 项目进行 lint。如果您想确保您提交给 git 的所有文件都通过了 ESLint 检查，那么请看一下 [lint-staged](https://github.com/okonet/lint-staged) ，它可以对提交的文件运行 ESLint。