# JavaScript 项目中依赖项和开发依赖项的区别

> 原文：<https://dev.to/clairecodes/the-difference-between-dependencies-and-devdependencies-in-a-javascript-project-22f2>

当您使用 [npm](https://www.npmjs.com/) 通过命令`npm install <package-name>`将包安装到您的项目中时，包的名称和版本会出现在项目的 package.json 文件中的“dependencies”键下。例如，`npm install react`会把这样的东西写到文件中:

```
{
    ...
    "dependencies": {
        "react": "^16.8.4",
        ...
    }
    ...
} 
```

该包的代码将安装在项目的本地“node_modules”文件夹中。

(注意:对于旧版本的 npm，为了将包写入“package.json ”,需要使用`--save`或`-S`标志。撰写本文时 npm 的最新版本是 6.9.0)。

当从头开始安装应用程序时，npm 会使用“package.json”中的包列表。除了“package-lock.json”文件，它还可以确保应用程序中使用的包是一致的版本。

也可以在“package.json”中的“devDependencies”键下添加包。而是在安装时添加`--save-dev`或更短的等效`-D`标志，例如:

```
npm i -D jest 
```

## 【devDependencies 和 Dependencies 有什么区别？

### 依赖关系

“依赖项”是在生产就绪环境中运行应用程序所需的包。没有这些包，你的应用就无法运行。几个常见的例子是:

*   **框架** : React，AngularJS，Vue.js
*   **实用程序库** : lodash，Ramda，date-fns，抛光

### 依赖关系

“devDependencies”是开发和构建您的应用程序所必需的，但不是运行客户将使用的最终版本所必需的。例如:

*   **测试库** : Jest，Mocha，Jasmine
*   棉绒:越来越漂亮
*   transpilers : webpack，Babel(因为生产就绪代码已经被传输和压缩)

当您在带有“package.json”文件的项目根目录下运行`npm install`时，**依赖项和 devDependencies 中的所有包**都会被安装。这是因为您正在处理源代码，所以可能需要每个包中的代码来开发它。然而，如果你只想安装 dependencies 项下列出的包，那么使用`—-production`标志，比如`npm install --production`。

总之，当决定一个包应该放在“package.json”文件中的什么位置时，问问自己这个包是否是应用程序在最终的生产就绪版本中工作所必需的。如果不是，则将其添加到 devDependencies 对象中，否则，它属于 Dependencies。

你还有其他的例子吗？让我知道！

有关更多信息，请参见 npm 安装命令的[官方 npm 文档页面。](https://docs.npmjs.com/cli/install)

## 等效纱线命令

如果你使用 [yarn](https://yarnpkg.com/) 作为你的包管理器而不是 npm，这篇文章中提到的等效命令是:

| npm | 故事 |
| --- | --- |
| npm 安装 | 纱线安装 |
| npm 安装反应 | 纱线添加反应 |
| npm i -保存-开发反应 | 纱线添加-开发反应 |
| NPM id 反应 | 纱线添加反应 |