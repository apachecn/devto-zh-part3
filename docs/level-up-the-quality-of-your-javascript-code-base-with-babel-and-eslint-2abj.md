# 用 Babel 和 ESLint 提升你的 javascript 代码库的质量。

> 原文：<https://dev.to/vukhanhtruong/level-up-the-quality-of-your-javascript-code-base-with-babel-and-eslint-2abj>

在这篇文章中，我们将设置 ESlint，更漂亮，以确保我们的代码在整个项目中具有相同的格式。Babel 是一个 transpiler，可以把我们的 ES6 代码变成 ES5。我将假设您知道如何使用 node 和 npm，并在您的机器上安装它们。

# 项目设置

创建一个目录并运行以下命令。

```
npm init 
```

对于本教程，我将添加一个 index.js 文件到`src`文件夹，这将是我们的入口点。我们的文件目录应该是这样的。

```
your-project/
|--src/
  |--index.js
|--package.json 
```

# 安装包

## Nodemon

Nodemon 是一个工具，它通过在检测到文件更改时自动重启节点应用程序来帮助开发基于 Js/Nodejs 的应用程序。

```
npm install nodemon --save-dev 
```

## 通天塔

Babel 是一个工具，用于将 ECMAScript 2015+代码转换为向后兼容的 JavaScript 版本，以便旧浏览器和环境能够理解您的代码。

运行以下命令来安装 babel:

```
npm install @babel/core \ 
            @babel/cli \ 
            @babel/preset-env \
            @babel/node \
            @babel/runtime --save-dev 
```

接下来，我们需要告诉 babel 如何通过在根目录下创建一个`.babelrc`文件并向其中添加以下代码来传输我们的文件。

```
{
  "presets": [
    "@babel/preset-env"
  ]
} 
```

[可选]如果要为目录、特定文件甚至其他 npm 模块设置自定义别名。让我们来看看这个方便的[插件](https://github.com/tleunen/babel-plugin-module-resolver)

## 斯拉夫语+ Airbnb JS style guide + Prettier

这些工具将对 ECMAScript/JavaScript 代码中发现的模式进行识别、报告和格式化，目的是使代码更加一致并避免错误。

运行以下命令进行安装:

```
npm install eslint \
            eslint-config-airbnb-base \
            eslint-config-prettier \
            eslint-plugin-import \
            eslint-plugin-prettier \
            prettier --save-dev 
```

### 更漂亮的配置

在根目录中创建名为`.prettierrc`的文件，并向其中添加以下代码。

```
{
  "trailingComma": "es5",
  "tabWidth": 4,
  "semi": false,
  "singleQuote": true
} 
```

如果您想要一个 JSON 模式来验证您的配置，这里就有:[http://json.schemastore.org/prettierrc](http://json.schemastore.org/prettierrc)。

### Eslint 配置

在根目录中创建名为`.eslintrc.json`的文件，并向其中添加以下代码。

```
{
  "extends": ["airbnb-base", "plugin:prettier/recommended"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }  
} 
```

## 脚本

打开`package.json`，然后将以下代码添加到脚本部分

```
{
  ...
  "scripts": {
    "build": "babel ./src --out-dir ./build",
    "start": "nodemon --exec babel-node src/index.js",
    "lint": "eslint ."
  },
  ...
} 
```

## 与 VSCode 集成

安装[更漂亮的](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)和 [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 扩展

配置 VS 代码

```
{
  ..
  "editor.formatOnSave": true,
  ..
} 
```

检查我的[要点](https://gist.github.com/vukhanhtruong/670c6b4d1c02a5798cb40a50762c7548)找到最终文件。

编码快乐！